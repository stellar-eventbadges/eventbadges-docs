# Introduction

<div class="project-brand">
  <img class="brand-light" src="brand/logo.svg" alt="EventBadges" width="360">
  <img class="brand-dark" src="brand/logo-dark.svg" alt="EventBadges" width="360">
</div>


`eventbadges` is a Stellar/Soroban project for **event attendance badges**. An
organizer records an event on the Stellar testnet; attendees claim a badge that
proves they were there. The badge is **non-transferable**: it cannot be sold,
given away or moved, because the contract has no way to move it.

This book has two audiences:

- **Organizers and attendees** — read [A worked example](worked-example.md),
  [What can go wrong](what-can-go-wrong.md) and the [FAQ](faq.md). No
  blockchain knowledge assumed; terms are explained as they appear and again
  in the [Glossary](glossary.md).
- **Developers** — read [Architecture](architecture.md) (every claim points at
  the real contract code), [Privacy](privacy.md), [Known
  limitations](limitations.md), [Threat model](threat-model.md) and the
  [Pilot playbook](pilot-playbook.md).

## Where the project stands (honest status)

- The contract (`eventbadges-contracts`) is **built and locally verified**:
  35 tests pass, the error table is machine-checked against the code, and the
  wasm builds with Stellar CLI 28.1.0. Its CI is green on GitHub.
- The app (`eventbadges-app`) is **built and locally verified**: four screens
  (home, organizer, claim, verify), 225 unit and render tests including an
  automated accessibility check on every screen and state, plus lint, strict
  type-check and a production build, all green. Its CI workflow is written but
  has never run on GitHub, so that one is unproven.
- **A synthetic testnet contract demonstration is deployed** and recorded in
  the [deployment record](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/TESTNET_DEMONSTRATION.md).
  The frontend is hosted at
  [eventbadges-testnet-xteesamz.vercel.app](https://eventbadges-testnet-xteesamz.vercel.app).
- **The browser-wallet business flow has not been validated.** The deployment
  and synthetic CLI/RPC checks do not establish that organizers or attendees
  can complete the flow through the app with a wallet.
- **No pilot has happened.** No organizer, no attendee, no real event. The
  [Pilots](pilots/README.md) page is a placeholder until a real pilot
  produces real facts.

Every page in this book describes only what the code does today, using two
status markers that mean different things:

- **Not implemented yet** — planned, not written. There is no code.
- **Built, never run** — written and passing its checks, but never executed
  against the real thing. This describes the unvalidated browser-wallet flow;
  it does not mean the testnet contract or hosted frontend is undeployed.

## The one-paragraph version

A community organizer wants to prove who attended a meetup or workshop.
Today that is a paper list or a spreadsheet nobody can check. With
`eventbadges`, the organizer records the event on the Stellar testnet with a
cap and a claim deadline, and hands each attendee their own secret claim code
out-of-band. An attendee proves that their code is one the organizer committed
— the code stays on their device; only hashes travel — and receives a badge
bound to their own wallet address. The badge can be verified by anyone,
forever, and it can never be traded away — which is the point: it attests
that *this address* was *at this event*, and an attestation that can be sold
proves nothing.
