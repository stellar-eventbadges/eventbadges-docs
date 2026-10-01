# AGENTS.md

Rules for any AI agent working in this repository (`eventbadges-docs`). Read this file at the start of every task.

## Project context

`eventbadges` is a Stellar/Soroban project with three repos: `eventbadges-contracts` (Rust contract), `eventbadges-app` (web app) and `eventbadges-docs` (this repo, an mdBook). Built by one person, public, open to outside contributors. Testnet only, never mainnet.


**Never put attendee names, phone numbers, emails or IDs on-chain. Opaque references or hashes only.** The privacy page documents what is and is not stored on-chain.

**Pilot honesty:** no `eventbadges` pilot has happened. The docs never describe a deployment, a user or a result that does not exist, and never claim safety for real funds.

## Source of truth

1. `README.md` — honest status.
2. `ROADMAP.md` — what v0 is and what is deliberately unimplemented.
3. The code repos — the book describes only what the code does, with every claim pointing at a file, function or test. Where the book and the code disagree, the code wins and the book is wrong.

## Commit rule

- One logical change per commit. Subject: `type: imperative summary`, 72 characters or fewer. Stage by explicit file name and read the staged diff before committing. NO Codebuff or co-author trailers. No history rewrites. No filler, empty or backdated commits. Commit counts are never a goal.

## Docs rules (once the book exists)

- mdBook pages live inside `src/`; `src/SUMMARY.md` is the table of contents.
- A dependency-free link checker (`scripts/check-links.mjs`) with its own tests verifies every relative link and SUMMARY entry; it runs in CI and locally.
- Never invent numbers, users, quotes or outcomes. A page that cannot be traced to real code or real events does not belong here.
- Write `TODO(verify)` next to anything that cannot be checked, and record it in the book's todo page in the same commit.

## Collaboration rules

- Lead with the result or the next action; detail comes after.
- Call out incorrect assumptions plainly, in one sentence, and continue with what is true.
- Ask before anything destructive, legal or security-related; record high-stakes questions under "Decisions needed from Tim" in `ROADMAP.md` and carry on with the rest.
- Honest completion report: what was checked, what was not, any defect found.
- Do not invent requirements, and do not add scope beyond the task.
