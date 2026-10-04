# Product requirements

This page states what the product **is, as built** — not a wish list. Every
requirement traces to real code, in `eventbadges-contracts` for rows 1–10 and
in `eventbadges-app` for rows 11–17. Anything not in the code is listed under
[Not built](#not-built-yet).

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
| 2 | An attendee can claim a badge by proving their code is one of the event's committed leaves — one badge per attendee per event, and one place per code. | `claim`, `src/badges.rs`; errors `ClaimProofInvalid`, `ClaimCodeUsed`, `AlreadyHeld` |
| 3 | The organizer can award a badge directly, under the same cap, window and one-per-attendee rules. | `award`, `src/badges.rs` |
| 4 | The organizer can revoke a badge at any time, including after the window closes. | `revoke`, `src/badges.rs`; test `claim_after_the_deadline_fails_but_revoke_still_works` |
| 5 | Anyone can read event records, badge existence, and an attendee's badges. | `get_event`, `has_badge`, `badges_of`, `src/badges.rs` |
| 6 | Badges cannot be transferred, approved or delegated — no such entrypoint exists. | absence enforced by design; see the contracts repo's `docs/decisions/0001-nft-approach.md` |
| 7 | Every failure has a documented, machine-checked error code with user-facing wording. | `ERRORS.md` + `scripts/check-errors.mjs` in the contracts repo (9 variants checked in CI) |
| 8 | State changes announce themselves with documented events. | `docs/events.md` in the contracts repo; layouts asserted in `src/test.rs` |
| 9 | Records stay readable past the deadline without manual babysitting. | TTL from `closes_at` + 30-day margin, 7-day floor, `src/storage.rs`; tests `create_event_extends_the_*_ttl` |
| 10 | No personal data on-chain — hashes and opaque values only. | [Privacy](privacy.md); types in `src/types.rs` |

## The app, as built

`eventbadges-app` is a browser app with four screens — home, organizer, claim,
verify. Every contract call it makes is one of the seven entrypoints above;
there is no other path from the app to the chain, and no backend of any kind.

| # | Requirement | Where it lives |
|---|---|---|
| 11 | An organizer records an event in the browser and sees the generated claim code exactly once. | `src/pages/OrganizerPage.tsx`, `src/lib/claimCode.ts` |
| 12 | An attendee claims a badge by pasting the organizer's code, and lists the badges an address holds. | `src/pages/AttendeePage.tsx`, `src/lib/flow.ts` |
| 13 | Anyone, with no wallet connected, can verify that an address holds a badge for an event. | `src/pages/VerifyPage.tsx` |
| 14 | Contract failures are shown with the wording from `ERRORS.md`, never reworded in the app. | `src/lib/contractErrors.ts`, with a test that fails if a variant has no mapped message in either direction |
| 15 | The app refuses to run on any network but testnet, and re-reads the wallet's network before every write. | `src/lib/network.ts`, `src/lib/flow.ts`; the wrong-network refusal is tested in `src/lib/flow.test.ts` |
| 16 | Every screen and every state it can be in is keyboard-operable and passes an automated accessibility check. | axe-core check in `src/test/render.tsx`, run by `npm test` |
| 17 | Nothing leaves the browser except the RPC call: no backend, no analytics, no third-party scripts. | by construction; stated in the app repository's README |

**Every one of those rows is "built, never run."** What passes is unit, render,
accessibility, lint, type-check and build. One scope limit worth naming: the
organizer screen generates a single code and commits it as a one-leaf tree, so
an app-created event can be claimed by exactly one attendee. The contract
itself supports one leaf per attendee; building multi-attendee trees and
handing out per-attendee tickets is drafted in the app repository's issue 12,
not built. What has never happened is the part
that matters to a person: no wallet has connected or signed, and no contract
call has reached a deployed contract, because none exists. The app repository
says so itself, under "What is proven vs assumed".

## Not built yet

- Everything under "Deliberately unimplemented" in the contracts
  [ROADMAP](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/ROADMAP.md):
  address-bound claim leaves, badge metadata, batch awarding, event series,
  pagination.
- Everything in the app repository's `docs/issue-drafts/`, including the
  per-event fresh-address advice that [privacy](privacy.md) recommends.
- Any deployment. There is no testnet instance; there is no pilot; there is no
  contract id in the app's configuration.

## Non-goals

- Transferable badges or any token economics — the opposite of the point.
- Mainnet, real money, or storage of personal data — all permanently out of
  scope for this project.
