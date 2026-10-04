# Privacy: what is on-chain

A public blockchain is the opposite of a private database: every byte in
every contract, argument and event is world-readable, effectively forever.
This page is the field-by-field inventory of what `eventbadges` puts there,
written from `src/types.rs` and `src/storage.rs` of the contracts repo. The
short version: **no names, no contact details, no identifiers of any person
— only hashes and wallet addresses.**

## What the chain holds

| Data | On-chain because | Privacy character |
|---|---|---|
| Wallet addresses (organizer, attendees) | storage keys and signature checks | pseudonymous: identifies the *key*, not the person — but anyone who links an address to a person (off-chain leak, explorer analytics) sees that person's full attendance history |
| `name_hash` (SHA-256 of an event's name) | lets the organizer recognize their own event without storing the name | low-entropy: human event names have few possibilities, so this hash **can be brute-forced**. Treat it as "obscured", not "secret". Choose names you are comfortable publishing |
| `claim_root` (Merkle root over one `SHA-256(code)` leaf per attendee) | the commitment a claim verifies its proof against; since [ADR 0003](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/decisions/0003-per-attendee-claim-codes.md), the only claim-related value the event stores | public, like everything else here, and harmless: leaves are **not** derivable from a root, so it is not a bearer credential. One attendee's leaf travels in the claim transaction and is spent by the first successful claim, so a code is good for one place. Codes stay random and long (128+ bits of entropy), generated per `docs/claim-codes.md` in the contracts repo and shared out-of-band only |
| `max_claims`, `closes_at`, `claim_count`, `issued_at` | the event's rules and state | attendance statistics are public per event |
| Events (`event_created`, `badge_claimed`, `badge_awarded`, `badge_revoked`) | the public audit trail | topics carry the event id and the attendee address, so attendance is indexable by anyone, and the three badge events repeat the organizer address in their data. `event_created`'s data map also republishes `name_hash` — the brute-forceable value from the `name_hash` row above, in a second place an indexer can read — along with `max_claims` and `closes_at`. `claim_root` is deliberately **not** in the event data: see `docs/events.md` in the contracts repo, asserted field by field in `lifecycle_publishes_documented_events` |

## What the chain never holds

- Names, usernames, phone numbers, emails, or any contact detail.
- Claim codes themselves — only hashes: the event stores a Merkle root, a
  claim transaction carries one attendee's leaf, and neither yields a code. The
  code is in neither the ledger nor its transaction history.
- Any off-chain document or profile — there is no field for one.
- Anything about children. The app must never be pointed at events involving
  minors without the organizer understanding everything above is public.

## The honest caveats

1. **Addresses are pseudonymous, not anonymous.** Attendance patterns are
   linkable across events by address. If an attendee's identity is ever
   connected to their wallet off-chain, every badge they claimed becomes
   attributable. Attendees who do not want that should use a fresh address
   per event. The app's claim screen says so, in the full privacy notice
   (`src/lib/privacyNotice.ts` in the app repo, shipped 2026-10-03).
2. **`name_hash` is reversible in practice.** Unlike the claim code, an
   event's name is chosen from a small space. This book deliberately calls
   the field "obscured" rather than "hashed-safe".
3. **Deletion is not a thing.** `revoke` removes the badge entry, but the
   `badge_claimed` event that announced it remains on-chain forever. A chain
   is the wrong place for anything anyone might later want erased.
4. **Off-chain sharing is on the organizer.** The claim code, the attendee
   list, any spreadsheet of who attended — none of that is this contract's
   problem to protect, but it is still the organizer's data-handling
   responsibility.

## Rules this project follows

From the AGENTS.md files of the three repos, binding on code, docs, tests and
app: no names, phone numbers, emails or attendee identifiers on-chain; opaque
references or hashes only; test fixtures use synthetic bytes only; and the app
carries no analytics, trackers or backend of its own (`no backend in v0`).
That last rule is about *this project's* servers, not about secrecy or
seclusion in transit: the app's addresses and transactions go to a public
Stellar RPC endpoint operated by someone else, which is an open question on
the [roadmap](https://github.com/stellar-eventbadges/eventbadges-docs/blob/main/ROADMAP.md)
(*third parties in the path*).

*This page is a privacy description, not legal advice and not a GDPR/NDPR
analysis. Where a real pilot with real attendees is planned, that review is
a [decision needed from Tim](https://github.com/stellar-eventbadges/eventbadges-docs/blob/main/ROADMAP.md).*
