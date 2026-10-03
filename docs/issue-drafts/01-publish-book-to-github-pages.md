# Publish the built book to GitHub Pages
**Difficulty:** easy
**Labels:** good first issue | area:docs

## Problem
The book builds in CI (`docs.yml` runs `mdbook build`) but nobody can read
the result without cloning the repo and installing mdBook. The docs exist to
be read by pilot users before they touch anything technical.

## Scope
- Add a `publish` job (or extend the existing one) to `.github/workflows/docs.yml`
  that uploads the built `book/` directory with `actions/upload-pages-artifact`
  and deploys with `actions/deploy-pages`, gated to `push` on `main` only
  (not pull requests).
- Enable GitHub Pages for the repository (Source: GitHub Actions) — a
  maintainer settings step, list it in the PR description.
- Link the published URL from this repo's README and from the contracts
  repo's README once live.

Out of scope: custom domains, versioned books.

## Acceptance criteria
- [ ] The workflow deploys the book on pushes to `main` and not on PRs.
- [ ] The published page renders all SUMMARY entries and in-page anchors.
- [ ] READMEs link the live URL (a real URL — never an invented one).

## Where to start
`.github/workflows/docs.yml`, the GitHub Pages docs for Actions-based
deployment. The schoolfees docs repo has the identical draft.

## How to test
```
node scripts/check-links.mjs
node --test
```
then watch the workflow run after merge.
