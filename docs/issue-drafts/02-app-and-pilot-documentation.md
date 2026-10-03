# Document the participant-facing app flow
**Difficulty:** medium
**Labels:** help wanted | area:docs

## Problem
The book currently documents the contract. When `eventbadges-app` exists
(planned as the next build in the program), the plain-language pages —
worked example, what-can-go-wrong — need a real walkthrough of the actual
screens: how an organizer creates an event in the app, how an attendee
claims with a wallet, how a verifier checks a badge. Documentation of an app
that does not exist would be fiction, so this is deliberately deferred.

## Scope
- Rewrite [A worked example](../../src/worked-example.md) step by step
  against the real screens, with screenshot placeholders for the maintainer
  to fill.
- Extend [What can go wrong](../../src/what-can-go-wrong.md) with where each
  error message appears in the app (wording must stay verbatim from
  `ERRORS.md`).
- Add a wallet-setup page for non-technical users (install, create, fund
  from testnet faucet), tested on a real phone.

Out of scope: app development itself; anything about a pilot's outcome.

## Acceptance criteria
- [ ] Every step in the walkthrough matches a real screen and a real
      contract call.
- [ ] Error wordings match `ERRORS.md` exactly.
- [ ] Screenshot placeholders are clearly marked for the maintainer.
- [ ] Link checker and tests pass.

## Where to start
`src/worked-example.md`, `src/what-can-go-wrong.md`, and the app repo's
`ERRORS.md`-consuming error-map module.

## How to test
```
node scripts/check-links.mjs
node --test
```
