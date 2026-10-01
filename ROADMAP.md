# Roadmap

What is next for `eventbadges-docs`, in order. Anything not listed as done is
**not implemented**.

## Status

- [x] Repository governance: AGENTS.md, CONTRIBUTING.md, ROADMAP.md, LICENSE,
      .gitignore, .gitattributes (2026-10-01).
- [ ] v0 book — **blocked on the playbook file, see below.**

## Next

- [ ] mdBook configuration (`book.toml`) and `src/SUMMARY.md`.
- [ ] Pages written from the real contract code once it exists: architecture
      with every claim pointing at a file, function or test; limitations;
      threat model (STRIDE, with honest "not applicable" entries); pilot
      playbook; PRD; a privacy page on what is and is not stored on-chain.
- [ ] Dependency-free link checker (`scripts/check-links.mjs`) with tests.
- [ ] CI (`docs.yml`): link check, checker tests, mdBook build.

## Blocked on the playbook

The book describes what the code does; the contract does not exist yet, and
its scope is defined by the playbook (section 9), which was not found
on this machine at Session 0. Until Tim supplies it, no scope is invented.

## Decisions needed from Tim

1. **Playbook location.** Provide
   `~/Desktop/Drips/_reference/playbooks/STELLAR-BUILD-PLAYBOOK-v3.md` so the
   docs plan can be written against a real scope.

## Explicitly out of scope

Mainnet deployment, investor or fundraising material, and any page that
describes a feature the code does not have.
