# Threat model

A STRIDE walk-through (Spoofing, Tampering, Repudiation, Information
disclosure, Denial of service, Elevation of privilege) for the v0 contract
as built. The asset inventory comes first; honest gaps come last.

## Assets

- **The integrity of the attendance record** — the thing verifiers rely on.
  A forged badge is the headline attack.
- **Attendees' pseudonymity** — address-linked attendance history (see
  [privacy](privacy.md)).
- **The reputation of any organizer using this** — a badge from "their"
  event that they did not issue.
- No funds are at stake: the contract holds no tokens and has no payable
  path. That materially shrinks this model.

## Adversaries

- A **fake attendee**: wants a badge for an event they did not attend.
- A **badge trader**: wants badges to become transferable, or to hold badges
  for sale.
- An **uninvolved attacker**: wants to spam events or badges to devalue the
  record (or the pilot).
- A **malicious organizer**: wants to rework history — deny a badge, fake
  attendance, dodge blame.
- A **curious outsider**: reads the chain; not an attacker, just the public.

## STRIDE

**Spoofing** — could someone act as another address?
Only with that address's signature: `claim` requires the attendee's own
`require_auth`, `create_event`/`award`/`revoke` require the recorded
organizer's (tests `claim_requires_the_attendee_signature`,
`*_requires_the_organizer_signature`). The residual risk is procedural: the
claim *code* is a bearer secret — whoever holds it can claim *their own*
address, which is exactly its purpose; it cannot be used to claim *someone
else's* address.

**Tampering** — could stored data be changed by someone unauthorized?
Badges and events live under explicit keys and mutate only through the four
entrypoints above, all auth-gated; `claim_count` arithmetic is checked and
capped (`count_after_issue`). One honest exposure: a legitimate organizer
*can* tamper with their own event's record via `award`/`revoke` — that is
their role, not a bug; verifiers should treat organizer-controlled fields
accordingly. A "stranger signs the revoke" attempt fails
(`revoke_rejects_a_signature_from_someone_other_than_the_organizer`).

**Repudiation** — can an action be denied afterward?
Every state change emits a documented event (`docs/events.md` in the
contracts repo), asserted byte-exact in tests. Attendance issuance, direct
awards and revocations are all attributable to the signature that triggered
them. Not emitted (and therefore not provable): *why* a badge was revoked;
anything about off-chain code distribution. An organizer can always claim
the code was shared accidentally — the chain cannot contradict them.

**Information disclosure** — is anything meant to be private actually
public?
Everything on-chain is public; the contract stores only hashes, addresses
and counters ([privacy](privacy.md)). The two real exposures are stated
there: `name_hash` is brute-forceable for human-chosen names, and attendance
histories are linkable by address. The claim code itself is safe iff it is
random and long — a weak human-chosen "code" is brute-forceable exactly like
the name. The code's **hash** is not safe in that sense and never was: the
contract stores it and `get_event` hands it out, so anyone who reads the chain
can take one of the remaining places by claiming to their own address, no code
required. That is the open "sharing" defect the roadmap's per-attendee Merkle
item is for: presenting the hash rather than the code removed the raw secret
from the transaction, and it did nothing about this.

**Denial of service** — can one user block others?
- Event-level: filling `max_claims` is possible (it is the cap working);
  the organizer can revoke to free slots. Deadline-based griefing is capped
  by the same deadline that limits claims.
- Contract-level: `create_event` is permissionless, so the contract can be
  spammed with events — bounded per-transaction by fees, but it will pollute
  any naive indexer. No one event's problems can block another event.
- Storage: all lists are bounded (`MAX_CLAIMS_PER_EVENT`; the attendee list
  is length ≤ 1); no unbounded loops exist in the code.
- Archive risk: records extended from `closes_at` + 30 days can still lapse
  if nobody touches them for months (State Archival applies to testnet
  too). Accepted for v0; a public extend-TTL entrypoint is drafted, not
  built.

**Elevation of privilege** — could a non-admin perform an admin action?
The only admin-like power is the organizer role, fixed at `create_event`
(immutable field, no transfer, no second organizer). There is no upgrade
authority, no owner proxy, no pause switch — nothing to elevate to. Missing
auth fails loudly (`Error(Auth, InvalidAction)`), as the negative tests
assert.

## Out of scope (honest limits)

- **Front-running / mempool watching** is not analyzed. On a public
  testnet, a watched `claim` can be copied by anyone before the original
  lands (same code, different attendee). Consequence: last-slot races are
  possible; the cap and `AlreadyHeld` keep it orderly, but "who got the
  last badge" is not guaranteed fair.
- **Wallet, key management and phishing** are out of the contract's reach.
- **The app.** `eventbadges-app` now exists, and it is **built, never run**:
  every contract call and every wallet interaction in it has never executed
  against a real contract or a real wallet. Adversarial analysis of it would
  be analysis of code that has never behaved, so none is offered. Nothing on
  this page should be read as covering the app.
- **Economic attacks** on testnet value are not a thing worth modeling —
  there is no value.
- This model reviews code as of 2026-10-03 (commit
  `2c38812` in the contracts repo). Changes to entrypoints invalidate it.
