# Add accessibility and translation review of the book
**Difficulty:** easy
**Labels:** good first issue | area:docs

## Problem
The book's stated audience includes non-technical pilot users, but nothing
has checked reading level, table readability on a phone, or whether the
plain-language pages actually avoid jargon (the writing rules say explain
every term on first use — no automated or human check has verified that).

## Scope
- A pass over `src/` pages aimed at pilot users (introduction, worked
  example, what-can-go-wrong, FAQ, glossary): shorten sentences, replace
  unexplained jargon, verify every first-use term links the glossary.
- Check tables render readably at phone width in the built book (mdBook
  default theme) and note any that need restructuring.
- Produce a short findings note; apply the mechanical fixes in the same PR.

Out of scope: full translation work (this draft covers the review only);
WCAG auditing of the mdBook theme itself.

## Acceptance criteria
- [ ] Every first-use technical term in the pilot-user pages links
      [Glossary](../../src/glossary.md) or defines itself inline.
- [ ] Findings note exists (even if the finding is "all clear").
- [ ] Link checker and tests pass after the edits.

## Where to start
`src/introduction.md`, `src/worked-example.md`, `src/what-can-go-wrong.md`,
`src/faq.md`.

## How to test
```
node scripts/check-links.mjs
node --test
```
