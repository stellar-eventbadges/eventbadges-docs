# Quickstart

How to inspect the contract, testnet demonstration, and hosted app. The
synthetic contract deployment is recorded in the
[deployment record](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/TESTNET_DEMONSTRATION.md).
The browser-wallet business flow has not been validated, so this page does not
claim an end-to-end user flow is working.

## Read the contract without running anything

1. Open [`eventbadges-contracts`](https://github.com/stellar-eventbadges/eventbadges-contracts).
2. Read [`README.md`](https://github.com/stellar-eventbadges/eventbadges-contracts#readme) — the entrypoints in plain words.
3. Read [`ERRORS.md`](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/ERRORS.md) — every failure, with the user-facing wording the app must use.
4. Read [`docs/events.md`](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/events.md) — what the contract announces when things happen.

## Run the checks yourself

You need Rust (1.84.0+, with the `wasm32v1-none` target) and Node 22+. From a
clone of `eventbadges-contracts`:

```bash
cargo fmt --all --check          # formatting
cargo clippy --all-targets -- -D warnings   # lints, warnings are errors
cargo test                       # 35 tests: error paths + lifecycle/auth/Merkle
node --test                      # tests for the two sync checkers
node scripts/check-errors.mjs    # ERRORS.md matches enum Error exactly
node scripts/check-events.mjs    # docs/events.md matches the event structs
stellar contract build           # builds the wasm (needs the Stellar CLI)
```

Every command above passes as of 2026-10-04. The last one needs the
[Stellar CLI](https://developers.stellar.org/docs/tools/cli/stellar-cli)
(v28.1.0 was used) or you can stop before it.

## Run the app's checks yourself

The web app exists ([`eventbadges-app`](https://github.com/stellar-eventbadges/eventbadges-app))
and is hosted as a testnet demo at
https://eventbadges-testnet-xteesamz.vercel.app. Its browser-wallet business
flow has not been validated. Node 24 (the version its CI uses),
from a clone of `eventbadges-app`:

```bash
npm install                   # or npm ci, which is what CI runs
npm run lint                  # oxlint
npm run typecheck             # tsc -b (strict)
npm test                      # unit and render tests, incl. axe
npm run build                 # production build
```

These commands were recorded as passing locally before the hosted demo was
published; rerun them against the current checkout when making changes. The
demo uses the synthetic testnet contract configuration. Connecting a wallet
and completing organizer or attendee transactions through the browser remains
unverified.

## Deploying

The recorded deployment is a synthetic testnet demonstration, not a real
pilot. A real-user pilot still requires an organizer's agreement and the
maintainer's review (see the [pilot playbook](pilot-playbook.md)). Never treat
the testnet deployment as production-ready or use real funds.
