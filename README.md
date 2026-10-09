# eventbadges — docs

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="brand/logo-dark.svg">
  <img src="brand/logo.svg" alt="EventBadges" height="72">
</picture>


The contract now has a [verified synthetic testnet demonstration](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/TESTNET_DEMONSTRATION.md).
The browser app is hosted as a synthetic testnet demonstration at
https://eventbadges-testnet-xteesamz.vercel.app. Its browser-wallet business
flow has not been validated. No real pilot or production readiness is claimed.

The documentation for `eventbadges`, written as an
[mdBook](https://rust-lang.github.io/mdBook/). `eventbadges` is a
Stellar/Soroban project for **non-transferable event attendance badges**: an
organizer records an event, attendees claim a badge with a secret claim
code, and nobody — including the organizer — can move a badge between
addresses. **Testnet only. A synthetic contract demonstration is deployed; no
pilot has happened, and no browser-wallet business flow has been validated.**

Part of the eventbadges project, which is three repositories:
[`eventbadges-contracts`](https://github.com/stellar-eventbadges/eventbadges-contracts)
(the Rust contract, built and locally verified),
[`eventbadges-app`](https://github.com/stellar-eventbadges/eventbadges-app)
(the hosted testnet web demo; browser-wallet business flow not yet validated)
and this one.

## The book

| Page | What it covers |
|---|---|
| [Introduction](src/introduction.md) | What the project is, in plain language, with the honest status |
| [A worked example](src/worked-example.md) | The full life of an event, entrypoint by entrypoint |
| [Quickstart](src/quickstart.md) | How to read and run the real checks yourself today |
| [Product requirements](src/prd.md) | What the product is, from what the code does |
| [Architecture](src/architecture.md) | The contract, point by point, from the real code |
| [Privacy: what is on-chain](src/privacy.md) | The field-by-field data inventory, with the honest caveats |
| [What can go wrong](src/what-can-go-wrong.md) | Every failure in plain language |
| [Known limitations](src/limitations.md) | What is not proven and not handled |
| [Threat model](src/threat-model.md) | A STRIDE walk-through, with honest out-of-scope rows |
| [Pilot playbook](src/pilot-playbook.md) | How a pilot will be run, and the deployment gate |
| [Pilots](src/pilots/README.md) | The record of real pilots (currently: none) |
| [FAQ](src/faq.md) | Short answers to common questions |
| [Glossary](src/glossary.md) | The terms, in plain words |

## Working on the book

```bash
node scripts/check-links.mjs   # every link and every SUMMARY entry resolves
node --test                    # tests for the link checker
```

CI runs both of those, then installs mdBook and runs `mdbook build`
(`.github/workflows/docs.yml`). mdBook is deliberately **not** installed on
the maintainer's machine, so the book build is verified in CI only.

To preview the book locally you need mdBook:

```bash
cargo install mdbook
mdbook serve --open
```

## Layout

```text
├── book.toml                 # mdBook configuration
├── src/
│   ├── SUMMARY.md            # table of contents
│   ├── introduction.md … glossary.md   # the pages in the table above
│   └── pilots/               # one real pilot per file; a placeholder until then
├── scripts/
│   ├── check-links.mjs       # link + SUMMARY checker (no dependencies)
│   └── check-links.test.mjs  # its tests
├── docs/issue-drafts/        # drafts for contributors (never created on GitHub for you)
├── AGENTS.md                 # rules for AI agents working in this repo
└── ROADMAP.md                # what is next, and what is deliberately not built
```

## Rules this book follows

The full set is in [AGENTS.md](AGENTS.md). The short version:

- Describe only what the code does. Anything not built is marked as such, and
  anything built but never executed is marked **Built, never run**.
- Every technical claim points at a real file, function, test or command in
  `eventbadges-contracts` or `eventbadges-app`. Where the book and the code
  disagree, the code wins.
- Never invent addresses, transaction hashes, testers, events or outcomes.
  No pilot is recorded until it really happens.
- Never include personal data, even in examples — placeholders only.
- Never soften the pilot-evidence boundary or the production boundary.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The plan for what comes next is in
[ROADMAP.md](ROADMAP.md).

## License

MIT — see [LICENSE](LICENSE).
