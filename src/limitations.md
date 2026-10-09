# Known limitations

What this project has **not** proven and does not handle. Written to be
plain, per the repo rules; a limitations doc with nothing in it is a sign it
was not written carefully.

## Network scope

Testnet only. No mainnet deployment exists, none is planned for v0, and no
real value should ever touch this contract. The contract has a
[verified synthetic testnet deployment](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/TESTNET_DEMONSTRATION.md).
[Synthetic CLI/RPC badge smoke tests](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/LIVE_TESTNET_SMOKE.md) passed. No browser-wallet flow or real pilot has been performed; each check establishes only its stated scope.

## What is not enforced on-chain

- **That attendees are who the organizer thinks they are.** The contract
  checks a Merkle proof against the event's root; it cannot know the code was
  given only to people actually in the room.
- **That an event is real.** Anyone with a wallet can create an event with
  any name hash. There is no verification of organizers, no registry, no
  identity layer.
- **Rate limits or spam controls on `create_event`.** Creating events is
  permissionless by design; the only cost is the caller's own fees.
- **One badge per *person*.** The contract enforces one badge per *address*.
  One person with many wallets is many attendees, as far as the chain knows.
- **Badge metadata.** A badge is four fields (`Badge` in `src/types.rs`).
  No images, no attributes, no standard metadata interface (drafted as an
  issue in the contracts repo).

## Not yet handled

Real gaps from the contracts
[ROADMAP](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/ROADMAP.md)
and the app repository's `docs/issue-drafts/`, stated as risks rather than
hidden:

- **A leaked claim code cannot be rotated.** The root is fixed at creation
  and the code is not recoverable from it; a leaked code is usable *first* for
  the one place it was for. The responses are procedural: keep the event's
  claim window short, then revoke the badge that used the code and `award` the
  shut-out attendee one.
- **Tickets are shown once, as text.** The create screen generates one ticket
  per attendee — code and proof, in the browser — but shows them on the success
  screen only: no QR code, no print view, no way to see them again (drafts 01
  and 02 in the app repo). A lost ticket is recoverable only through `award`.
- **No listing of an event's attendees.** `badges_of` is per-attendee and
  capped at one; there is no way to enumerate holders of an event's badges
  on-chain. An indexer reading `badge_claimed` events is the workaround.
- **No claim-code expiry inside the window.** `closes_at` is the only
  timing control.
- **The browser-wallet business flow has not been validated.** The contract
  has a synthetic testnet deployment and CLI/RPC smoke checks, but no wallet
  has completed organizer or attendee actions through the hosted app. Error
  handling has not been observed across a full browser-to-contract transaction.
  Static and component checks cannot establish that end-to-end flow.
- **The app's CI has never run.** `.github/workflows/web.yml` exists and its
  four steps pass locally, but it has never executed on GitHub. One green run
  is pending, and a slow cold-runner install of the wallet kit's dependency
  tree is the most likely first failure.

## Pilot evidence boundary

No pilot has happened. There are no users, no test groups, no evidence of
any kind about how this behaves with real people — including whether anyone
would want it. When a pilot runs, its facts go in
[pilots/](pilots/README.md), and this page gets updated with whatever it
reveals. A pilot, even a successful one, will not be evidence about load,
adversarial behavior, or long-term use.

## Production boundary

An independent review is required before this contract is used for anything
beyond a small testnet pilot with people who know what testnet means. The
badge logic is purpose-written (the OpenZeppelin evaluation is recorded in
the contracts repo's `docs/decisions/0001-nft-approach.md`); no audit has
occurred; nothing here is reviewed by anyone but its author. Do not soften
this boundary when quoting this page.
