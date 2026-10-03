# Product requirements

This page states what the product **is, as built** — not a wish list. Every
requirement traces to real code in `eventbadges-contracts`. Anything not in
the code is listed under [Not built](#not-built-yet).

## Problem

Community organizers prove attendance with paper lists and spreadsheets.
Attendees get nothing portable, verification depends on trusting the
organizer's records, and any proof that *can* be handed to someone else
proves nothing about who earned it.

## Who it is for

- **Organizers** of meetups, workshops, community classes — anyone who wants
  a tamper-proof attendance record.
- **Attendees** who want proof they were there, bound to their own address
  and not transferable to anyone else.
- **Verifiers** (employers, other organizers, communities) who want to check
  a claim without trusting either party.

## Requirements, as built

| # | Requirement | Where it lives |
|---|---|---|
| 1 | An organizer can record an event with a cap (1–10,000 badges) and a claim deadline in the future. | `create_event`, `src/badges.rs`; validation errors `MaxClaimsTooLarge`, `ClosesAtInPast` |
| 2 | An attendee can claim a badge by presenting a secret whose SHA-256 matches the event's stored hash — one badge per attendee per event. | `claim`, `src/badges.rs`; errors `ClaimCodeMismatch`, `AlreadyHeld` |
| 3 | The organizer can award a badge directly, under the same cap, window and one-per-attendee rules. | `award`, `src/badges.rs` |
| 4 | The organizer can revoke a badge at any time, including after the window closes. | `revoke`, `src/badges.rs`; test `claim_after_the_deadline_fails_but_revoke_still_works` |
| 5 | Anyone can read event records, badge existence, and an attendee's badges. | `get_event`, `has_badge`, `badges_of`, `src/badges.rs` |
| 6 | Badges cannot be transferred, approved or delegated — no such entrypoint exists. | absence enforced by design; see the contracts repo's `docs/decisions/0001-nft-approach.md` |
| 7 | Every failure has a documented, machine-checked error code with user-facing wording. | `ERRORS.md` + `scripts/check-errors.mjs` in the contracts repo (8 variants checked in CI) |
| 8 | State changes announce themselves with documented events. | `docs/events.md` in the contracts repo; layouts asserted in `src/test.rs` |
| 9 | Records stay readable past the deadline without manual babysitting. | TTL from `closes_at` + 30-day margin, 7-day floor, `src/storage.rs`; tests `create_event_extends_the_*_ttl` |
| 10 | No personal data on-chain — hashes and opaque values only. | [Privacy](privacy.md); types in `src/types.rs` |

## Not built yet

- The web app (organizer, attendee and public verify screens) — planned as
  `eventbadges-app`.
- Everything under "Deliberately unimplemented" in the contracts
  [ROADMAP](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/ROADMAP.md):
  per-attendee Merkle claim codes, badge metadata, batch awarding, event
  series, pagination.
- Any deployment. There is no testnet instance; there is no pilot.

## Non-goals

- Transferable badges or any token economics — the opposite of the point.
- Mainnet, real money, or storage of personal data — all permanently out of
  scope for this project.
