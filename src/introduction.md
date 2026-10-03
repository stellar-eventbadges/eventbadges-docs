# Introduction

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
  24 tests pass, the error table is machine-checked against the code, and the
  wasm builds with Stellar CLI 28.1.0. Its CI is green on GitHub.
- **Nothing is deployed.** No contract exists on any network.
- The app (`eventbadges-app`) is **not built yet** (planned Day 5).
- **No pilot has happened.** No organizer, no attendee, no real event. The
  [Pilots](pilots/README.md) page is a placeholder until a real pilot
  produces real facts.

Every page in this book describes only what the code does today. Anything
else is marked **Not implemented yet**.

## The one-paragraph version

A community organizer wants to prove who attended a meetup or workshop.
Today that is a paper list or a spreadsheet nobody can check. With
`eventbadges`, the organizer records the event on the Stellar testnet with a
cap and a claim deadline, and hands each attendee a secret claim code
out-of-band. An attendee proves the code to the contract and receives a badge
bound to their own wallet address. The badge can be verified by anyone,
forever, and it can never be traded away — which is the point: it attests
that *this address* was *at this event*, and an attestation that can be sold
proves nothing.
