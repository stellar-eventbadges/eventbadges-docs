# Roadmap

What is next for `eventbadges-docs`, in order. Anything not listed as done is
**not implemented**.

## Status

- [x] Repository governance: AGENTS.md, CONTRIBUTING.md, ROADMAP.md, LICENSE,
      .gitignore, .gitattributes (2026-10-01).
- [x] v0 book, written from the real v0 contract code (2026-10-03): 12 pages,
      every architecture/privacy/threat claim pointing at a file, function or
      test; link checker green (53 links / 18 files), 13 checker tests pass.

## Next

- [x] mdBook configuration (`book.toml`) and `src/SUMMARY.md` (2026-10-03).
- [x] Pages written from the real contract code (2026-10-03): architecture,
      limitations, threat model (STRIDE with honest out-of-scope rows), pilot
      playbook, PRD, privacy page, worked example, troubleshooting, quickstart,
      FAQ, glossary, pilots placeholder.
- [x] Dependency-free link checker (`scripts/check-links.mjs`) with tests
      (2026-10-03).
- [x] CI (`docs.yml`): link check, checker tests, mdBook build (2026-10-03);
      proves itself on GitHub on the next push.
- [ ] Publish the built book so it can be read in a browser — see
      [draft 01](docs/issue-drafts/01-publish-book-to-github-pages.md).

## Contract scope the book describes

Set by `STELLAR-BUILD-PLAYBOOK-v3.md` section 9 (in
`~/Desktop/Drips/_reference/playbooks/`, confirmed 2026-10-02): organizers
create events, attendees claim non-transferable attendance badges, transfers
always fail, hashes only on-chain. The book is still written from the real
code once it exists, not from the playbook alone.

Deliberately unimplemented in the v0 contract (section 9): unique per-attendee
claim codes via Merkle proofs, badge metadata and images, batch awarding,
event series and streak badges, pagination for `badges_of`.

## Blocked on a real pilot

- [ ] The first `pilots/{name}.md`, written after a real pilot from what
      actually happened, including what did not work. No draft: it cannot be
      written from anything but a real pilot.
- [ ] Update `limitations.md` and `threat-model.md` with anything the pilot
      revealed. No draft, for the same reason.
- [ ] Participant-facing app walkthrough pages — blocked on `eventbadges-app`
      existing (Day 5 of the program plan); see
      [draft 02](docs/issue-drafts/02-app-and-pilot-documentation.md).

## Decisions needed from Tim

1. **Build standard — decided (2026-10-02).** v3 section 9 scopes what the
   book documents; v4's doc set plus the schoolfees docs are the standard
   for how it is built (templates, AGENTS.md, CI, checkers).
2. **Legal review of the privacy page** (`TODO(legal review)`): whether a
   real pilot needs a privacy notice, who is the controller for attendee
   data handling around claim codes and attendance lists, and what the
   pilot record may name and at what level of detail. Recorded in
   [src/privacy.md](src/privacy.md); a checklist question, not legal
   advice.

## Explicitly out of scope

Mainnet deployment, investor or fundraising material, and any page that
describes a feature the code does not have.
