# Quickstart

How to check out the real contract today. There is no app yet, so everything
happens in the contract repository.

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
cargo test                       # 24 tests: error paths + lifecycle/auth/TTL
node --test                      # tests for the error-table checker
node scripts/check-errors.mjs    # ERRORS.md matches enum Error exactly
stellar contract build           # builds the wasm (needs the Stellar CLI)
```

Every command above passes as of 2026-10-03. The last one needs the
[Stellar CLI](https://developers.stellar.org/docs/tools/cli/stellar-cli)
(v28.1.0 was used) or you can stop before it.

## Deploying

**You cannot deploy from this book, and that is deliberate.** Deployment is
gated on a real organizer agreeing to try the flow (see the [pilot
playbook](pilot-playbook.md)). When that gate opens, the human maintainer
runs `eventbadges-contracts/scripts/deploy-testnet.sh` themselves. Until a
real deployment happens, no contract id exists and none may be invented.
