# Architecture

Every claim on this page points at a file, function or test in
[`eventbadges-contracts`](https://github.com/stellar-eventbadges/eventbadges-contracts).
Where the book and the code disagree, the code wins.

## 1. System position

`eventbadges` is one Soroban smart contract: a **claim registry for
non-transferable attendance badges**. It records events and who attended
them. It is not a token contract (there is no transfer path by design), not a
custodial service (it holds no funds), and not a database (everything on it
is public and permanent-ish; see [limitations](limitations.md)). It depends
on: the Stellar network (testnet), a Soroban wallet for signatures, and
nothing else — its only dependency is `soroban-sdk` (28.0.0 in
`Cargo.lock`), decided in `docs/decisions/0001-nft-approach.md`.

## 2. Runtime topology

| Component | Runs where | Status |
|---|---|---|
| `eventbadges` contract | On-chain, Soroban host | built, **not deployed** |
| Organizer's / attendee's wallet | User's device; signs `require_auth`-guarded calls | any Soroban wallet |
| `eventbadges-app` | Browser | built, locally verified, **never run against a wallet** |
| Indexer / explorer | Third party, reads events | none; the events exist for one |

The app holds no state of its own: no backend, no database, no server of any
kind. It re-reads contract state over RPC whenever a screen needs it, so there
is nothing to keep in sync and nothing to migrate. Two rows in that table have
never been executed: the contract, because it is not deployed, and the app,
because no wallet has ever connected to it — see
[limitations](limitations.md).

The contract is self-contained: no admin entrypoint, no upgrade path, no
external calls except reads of its own storage. A wallet signature is the
only key material involved; the contract never holds or moves tokens.

## 3. Core protocol objects

All types live in `src/types.rs`.

**`Event`** — one persistent entry per event (`DataKey::Event(u64)`).

| Field | Meaning | Rule |
|---|---|---|
| `id` | Sequential id starting at 1 | `NextEventId` counter in instance storage |
| `organizer` | Controls the event's badges | set at creation, immutable |
| `name_hash` | SHA-256 of the event name | opaque bytes; never a plain name |
| `claim_code_hash` | SHA-256 of the secret claim code | checked on every claim; never published in events |
| `max_claims` | Badge cap | 1–10,000 (`MAX_CLAIMS_PER_EVENT` in `src/badges.rs`) |
| `closes_at` | Claim deadline (Unix seconds) | must be in the future at creation |
| `claim_count` | Badges issued so far | incremented by `claim`/`award`, decremented by `revoke` with `saturating_sub` |

**`Badge`** — one persistent entry per holder per event
(`DataKey::Badge(u64, Address)`): `event_id`, `attendee`, `organizer`,
`issued_at`. An attendee can hold at most one badge per event (the `AlreadyHeld`
check precedes every issuance). `DataKey::AttendeeBadges(u64, Address)` backs
`badges_of` with a keyed, bounded list (length ≤ 1 by the same rule).

## 4. Lifecycle

The event itself has no stored state flag — "open" and "closed" are derived
from the clock, and "full" from the counters. The badge flow:

```
create_event ──▶ event open (before closes_at, claim_count < max_claims)
                    │ claim(attendee, code)      ◀─ attendee's signature
                    │ award(attendee)            ◀─ organizer's signature
                    ▼
                badge held ──▶ revoke (organizer, any time) ──▶ slot freed
```

- `claim` checks, in order: attendee's auth → event exists → cap → deadline
  → not already held → code hash matches. Any failure aborts the whole call
  with a typed error from `enum Error` (ranges: 1–9 lookup, 10–29 lifecycle,
  30–49 validation).
- `award` is the same flow minus the code, gated on the organizer's
  signature instead.
- `revoke` removes the badge and the attendee's list entry, decrements
  `claim_count` (saturating), and is deliberately **not** window-bound (test:
  `claim_after_the_deadline_fails_but_revoke_still_works`).

## 5. Component responsibilities

**`src/lib.rs`** — entrypoints only; `#[contractimpl]` delegation to
`src/badges.rs`. No logic. **`src/badges.rs`** — all validation, storage
rules, hash checks, event publication. **`src/storage.rs`** — the `DataKey`
enum, TTL constants and `extend_*` helpers. **`src/types.rs`** — `enum
Error`, the `#[contracttype]` records, the four `#[contractevent]` types.
**`src/error_paths.rs`** — exactly one test per error variant, triggering the
real path. **`src/test.rs`** — lifecycle, authorization and event-layout
tests. The contract does **not** own: any UI, any off-chain code storage, any
policy about who deserves a badge — that is the organizer's judgment, applied
through their signature.

## 6. Trust boundaries

- **Organizer ↔ contract:** `require_auth` on `create_event`, `award`,
  `revoke` — only the recorded organizer can act on their event (tests
  `*_requires_the_organizer_signature`).
- **Attendee ↔ contract:** `claim` requires the attendee's own signature, so
  nobody can claim *for* an address, and the code proves the attendee was
  told the secret in person (test: `claim_requires_the_attendee_signature`).
- **Verifier ↔ contract:** read functions need no signature; the contract is
  the source of truth. The verifier must still trust that the *off-chain*
  claim code reached only genuine attendees — the chain proves the hash
  matched, not who was in the room.
- **Contract ↔ chain:** everything is public. The hash of an event's name is
  low-entropy — see [privacy](privacy.md) for what that means.

## 7. Design invariants

| Invariant | Where enforced / tested |
|---|---|
| A badge exists only under a key `(event_id, attendee)` and no code path can move or copy it | no transfer/approve/operator function exists (`docs/decisions/0001-nft-approach.md`) |
| At most one badge per (event, attendee) | `AlreadyHeld` check in `claim`/`award`; `error_path_already_held` |
| `0 ≤ claim_count ≤ max_claims` and `max_claims ≤ 10_000` | `count_after_issue` checked arithmetic; `error_path_max_claims_too_large`, `error_path_cap_reached` |
| Ids are sequential and never reused (revocation frees the *slot*, not the id) | counter in instance storage; `create_event_records_the_event` |
| Every error variant is reachable and documented | `src/error_paths.rs` (one test per variant) + `scripts/check-errors.mjs` in CI |
| Event layouts on the chain match `docs/events.md` | exact topic/data assertions in `lifecycle_publishes_documented_events`, `award_publishes_the_documented_event`, and `scripts/check-events.mjs`, which compares every topic and data field against `src/types.rs` in CI |
| Records survive past `closes_at` | TTL tests: `create_event_extends_the_instance_ttl`, `claim_extends_the_badge_ttl`, and the deadline-horizon test |

## Related documentation

- [Privacy: what is on-chain](privacy.md) — the field-by-field data inventory.
- [`eventbadges-app`](https://github.com/stellar-eventbadges/eventbadges-app)
  — the browser UI; its README is the honest record of what has and has not
  run.
- [Known limitations](limitations.md) — what this design does not do.
- [Threat model](threat-model.md) — who could attack what, and the mitigations.
- `ERRORS.md` in the contracts repo — one row per failure, with the
  user-facing wording the app must reuse verbatim.
