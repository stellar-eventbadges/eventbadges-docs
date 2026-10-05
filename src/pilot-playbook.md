# Pilot playbook

## Purpose

Pilots exist to test demand and real usability, not to repeat an internal
demo. Every pilot must use people outside this project, and must end with a
written note of what happened — including, especially, if it didn't work.

## The deployment gate (read first)

**Nothing gets deployed until a real organizer has agreed to try the flow.**
That agreement — a person, a date, an event — is the gate. It is recorded in
the ROADMAP of the contracts repo before any deploy script runs. Deploying
to testnet "to have it deployed" is exactly the failure mode this gate
exists to prevent: a live contract with no one to use it, and a README that
starts to drift from reality.

## Rule

An interested person is not a completed pilot. A completed pilot is someone
who actually ran the flow — created, claimed or verified — on their own
device. Name a pilot participant publicly only at the level of detail they
agreed to (their name, their group's name, or neither).

## Minimum flow for a pilot

1. **Recruit at least 3 real users with a real reason** to use this — an
   organizer with an actual upcoming event, attendees who will actually
   attend. "Interested in crypto" is not a reason.
2. **Give them a task, not a tour.** "Claim your badge for tonight's
   session" — not "let me show you the app". Watch, do not coach. Note
   where they hesitate.
3. **Record**: who (with consent), what they tried, what worked, what
   confused them, what changed afterward. The template in
   [pilots/](pilots/README.md) has the headings.
4. **Save the real transaction links** (testnet explorer URLs). Only real
   ones — invented hashes are forbidden by the repo rules and would poison
   the pilot record.
5. **Write it up while fresh** — same day if possible. A pilot note written
   a week later is a story, not data.

## Before the first pilot, the human maintainer must

- [ ] Have the deployment gate satisfied and recorded.
- [ ] Run the deploy script themselves (never an agent), from a machine and
      key they control.
- [ ] Record the real contract id in the app's `.env` — and nowhere that is
      committed.
- [ ] Confirm every pilot user has a working Soroban wallet on the device
      they will actually use (phone-first is the assumption).
- [ ] Drive one full flow yourself, end to end, before a participant sees it.
      No flow has ever run against a real wallet, so the maintainer — not a
      pilot user — is the first real user, and the first sign that something
      the tests cannot catch is wrong.
- [ ] Re-read [limitations](limitations.md) and be able to say, out loud,
      what this software is not.

## What a pilot is not

It is not evidence for mainnet safety, load behavior, or adversarial
robustness — say that in [limitations](limitations.md), not just here. It is
not a launch. It is not a promise that v1 exists. It is a small, honest
test with real people, written down.
