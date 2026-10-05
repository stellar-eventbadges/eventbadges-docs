# Quickstart

How to check out the real contract and the real app UI today. **Nothing is
deployed, and no flow has ever run against a real wallet** — so this page is
about reading code and running checks, not about using the product end to end.

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
cargo test                       # 34 tests: error paths + lifecycle/auth/Merkle
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
with all four v0 screens. It is **built, never run**: the checks below pass,
and no wallet has ever connected to it. Node 24 (the version its CI uses),
from a clone of `eventbadges-app`:

```bash
npm install                   # or npm ci, which is what CI runs
npm run lint                  # oxlint
npm run typecheck             # tsc -b (strict)
npm test                      # 185 unit and render tests, incl. axe
npm run build                 # production build
```

All four pass as of 2026-10-04. `npm run dev` also serves the UI, but it needs
a contract id in `.env`, and there is no deployed contract to put there: until
a pilot happens the app shows its configuration notice instead of a working
screen. That is the intended behaviour, not a bug to report.

## Deploying

**You cannot deploy from this book, and that is deliberate.** Deployment is
gated on a real organizer agreeing to try the flow (see the [pilot
playbook](pilot-playbook.md)). When that gate opens, the human maintainer runs
the app repo's `scripts/deploy-testnet.sh` themselves — it refuses to start
unless `PILOT_CONFIRMED=yes` is set, builds the wasm if it is missing, and
prints the contract id that goes into the app's `.env`. The contracts repo has
its own `scripts/deploy-testnet.sh`, which deploys from inside that repo and
enforces the same gate. Until a real deployment happens, no contract id exists
and none may be invented.
