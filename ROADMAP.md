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
- [ ] Participant-facing app walkthrough pages — no longer blocked on
      `eventbadges-app` existing (it does, as of 2026-10-03), but on it having
      *run*: the screens exist and none has ever been exercised against a
      wallet, so there is nothing yet to walk anyone through; see
      [draft 02](docs/issue-drafts/02-app-and-pilot-documentation.md).

## Decisions needed from Tim

1. **Build standard — decided (2026-10-02).** v3 section 9 scopes what the
   book documents; v4's doc set plus the schoolfees docs are the standard
   for how it is built (templates, AGENTS.md, CI, checkers).

### Legal review of the on-chain privacy model — needed before any pilot with real attendees

The privacy page's honest caveats raise questions only a human can answer.
They are collected here as a checklist so the decision is concrete. Working
through it produces a written position, not a compliance certificate, and
is not legal advice. Source: [Privacy: what is on-chain](src/privacy.md);
the same checklist is recorded in the ROADMAPs of all three eventbadges
repos, so they stay in sync.

- [ ] **Applicable law.** Which regimes apply to a pilot — GDPR (any EU/EEA
      attendee?), NDPR/NDPA (Nigeria), other local law — and does the answer
      change when the pilot group crosses a border?
- [ ] **Who is the controller?** For an attendance record on a public
      ledger: the organizer (they choose the event and who gets a badge),
      the project, both, or neither? Write the position down before
      recruiting anyone.
- [ ] **Lawful basis.** What basis covers putting a wallet address and an
      attendance fact on an immutable public ledger — consent, legitimate
      interest, something else? Can consent be freely given when nothing can
      ever be deleted, and what must the claim flow say before the wallet
      signs?
- [ ] **Erasure vs. immutability.** `revoke` removes the badge, but the
      `badge_claimed` event stays on-chain forever. Is that defensible under
      erasure and objection rights? If not, is the mitigation — no personal
      data on-chain, fresh-address guidance, declining unsuitable pilots —
      enough, and who signs off?
- [ ] **Are the hashes personal data?** `name_hash` is low-entropy and
      brute-forceable; an address becomes identifying the moment it is
      linked off-chain. Does "it is only a hash" or "pseudonymous" actually
      hold, or must both be treated as personal data?
- [ ] **Children.** The privacy page says events involving minors must never
      be pointed at this system without the organizer fully understanding
      everything is public. Make it operational: is "no under-18 events in a
      pilot" a hard rule, who checks, and what does the organizer attest to?
- [ ] **What attendees are told.** What must a person be told before they
      claim: that their address, the timing and the obscured event name are
      public and linkable, that nothing is deletable, and that anyone
      worldwide can verify? Who delivers that notice — the app, the
      organizer, both — and is a missing notice a blocker for the first
      pilot?
- [ ] **Third parties in the path.** The app sends addresses — and the claim
      code inside the public `claim` transaction — to the Stellar RPC
      endpoint, and explorers index events. How are RPC operators and
      explorers characterised (processor, independent controller), and can a
      pilot simply accept the public testnet RPC?
- [ ] **Off-chain handling by organizers.** Claim codes, attendee lists and
      check-in spreadsheets never touch the chain but stay with the
      organizer. Does the project owe organizers written data-handling
      guidance (what to keep, what to delete, how to share codes), and is
      that guidance a precondition for the first pilot?
- [ ] **The pilot's own records.** Pilot notes name participants only at
      their chosen level of detail and link real transactions. What consent
      does that require, and how long are pilot notes kept?

## Explicitly out of scope

Mainnet deployment, investor or fundraising material, and any page that
describes a feature the code does not have.
