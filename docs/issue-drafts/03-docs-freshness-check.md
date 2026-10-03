# Add a docs freshness check against the code repos
**Difficulty:** medium
**Labels:** help wanted | area:docs

## Problem
This book cites files, functions and tests in `eventbadges-contracts`
(architecture.md alone cites a dozen). Nothing verifies those citations
still exist; the first silent contract rename makes the book quietly wrong.
The repo rules say the code wins — this check is how that stays enforceable.

## Scope
- Extend the Node tooling (still zero-dependency) with a script that
  extracts backticked `file/function/test` citations per page and verifies
  each against a checkout of the contracts repo (a sibling clone or a
  fetched tarball — decide in the PR; do not add network calls to the
  checker itself).
- Report missing names with page and line, in the same style as
  `check-links.mjs`; wire into `docs.yml` after the link check.
- Keep the failure mode honest: a citation the checker cannot resolve is a
  failure, not a warning.

Out of scope: prose-fact checking (only names and paths); checking the app
repo before it exists.

## Acceptance criteria
- [ ] The checker verifies every code citation in `src/` and fails on a
      deliberately-broken citation (add a test fixture).
- [ ] Runs in CI after the link check.
- [ ] A deliberately renamed function in a scratch checkout makes CI red.

## Where to start
`scripts/check-links.mjs` for the parsing/report style; `src/architecture.md`
for the citation density to expect.

## How to test
```
node scripts/check-links.mjs
node --test
```
plus the fixture test for the new script.
