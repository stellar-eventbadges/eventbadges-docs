# Known limitations

What this project has **not** proven and does not handle. Written to be
plain, per the repo rules; a limitations doc with nothing in it is a sign it
was not written carefully.

## Network scope

Testnet only. No mainnet deployment exists, none is planned for v0, and no
real value should ever touch this contract. Nothing here has run against any
live network: the contract and the app are both built and locally verified,
the contract's CI is green, and that is the entire operational history of this
project.

## What is not enforced on-chain

- **That attendees are who the organizer thinks they are.** The contract
  checks a code against a hash; it cannot know the code was given only to
  people actually in the room.
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

- **A leaked claim code cannot be rotated.** The hash is fixed at creation;
  the only responses are procedural (short window, tight cap, revoke).
- **Claiming is one code for everyone.** There are no per-attendee codes
  (Merkle approach drafted, not built), so any holder of *the* code can
  claim until slots run out.
- **No listing of an event's attendees.** `badges_of` is per-attendee and
  capped at one; there is no way to enumerate holders of an event's badges
  on-chain. An indexer reading `badge_claimed` events is the workaround.
- **No claim-code expiry inside the window.** `closes_at` is the only
  timing control.
- **Nothing in the app has ever run.** The four screens exist and pass their
  checks, but no wallet has connected, signed or submitted through the app, and
  no contract call has ever reached a deployed contract. The `ERRORS.md`
  wording is mapped and unit-tested both ways, and has still never been seen
  rendered by a real failure. Treat the app as a user interface nobody has
  used, not as a working product — and expect the first person to run it to
  find what static checks cannot.
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
