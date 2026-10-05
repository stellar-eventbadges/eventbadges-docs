# A worked example

A plain-language walk through the full life of an event, using the real
entrypoints of the contract. The values are obviously fake placeholders — no
real event, person or address is involved.

## 1. The organizer creates an event

Ada runs a monthly robotics meetup. Before the event she:

1. Generates one random **claim code per attendee** — long secret strings —
   and keeps them offline. She never types them into the contract. (How codes
   and their tree are produced: [the claim-codes doc](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/claim-codes.md).)
2. Hashes each code locally and builds a **Merkle tree** over the leaves, so
   each attendee's code can be proven without being revealed. She keeps the
   tree (or the codes) offline. Only the tree's **root** goes on-chain; the
   pieces a claim carries later — one leaf and its sibling hashes — give
   nobody the code behind them.
3. Calls the contract:

```
create_event(
    organizer:       Ada's wallet address,
    name_hash:       SHA-256 of "robotics meetup" (opaque bytes on-chain),
    claim_root:      the Merkle root from step 2,
    max_claims:      100,
    closes_at:       a deadline one week after the event,
)
```

The contract stores the event and returns an **event id** (1, 2, 3, …). The
`organizer` address must sign this call — that is what stops anyone else from
creating events in Ada's name.

## 2. An attendee claims a badge

Ben attended the meetup. Ada gave him the claim code in person (a printout,
a QR code on the door — out-of-band, never on-chain). Ben opens any Soroban
wallet — or the app's claim screen, which hashes the code on his own device
before building the transaction — and calls:

```
claim(event_id: 1, attendee: Ben's wallet address, leaf_hash: <SHA-256 of Ben's code>, proof: <siblings between that leaf and the root>)
```

The contract checks, in order:

- the event exists;
- the cap (100 badges) is not reached;
- the deadline has not passed;
- Ben does not already hold a badge for this event;
- Ben's leaf and proof fold into the stored root, byte for byte;
- that leaf has not been spent by an earlier claim.

All pass → Ben's address is recorded as a badge holder for event 1.

The event stores a Merkle **root**, which anyone can read off the event with
`get_event` — with no wallet and no permission. That root is harmless: Ben's
leaf cannot be derived from it, and the contract accepts nothing that does not
fold into it. Ben's leaf is spent when he claims, so his code can take exactly
one place. If someone else had his code first, they would take that place and
Ben's claim would fail (`ClaimCodeUsed`); the organizer can revoke the badge
that used it and award Ben one directly.

The badge is **not a token in his wallet** — it is a record *inside the
contract* that says address B attended event 1. There is no transfer
function anywhere in the contract, so the badge cannot leave Ben's address.

Ada can also **award** a badge directly (`award`) for attendees who cannot
claim — say the check-in laptop died — and **revoke** (`revoke`) a badge
issued in error, at any time, even after the deadline.

## 3. Anyone verifies attendance

Months later, a recruiter wants to know: does address B hold a badge for
event 1? One read call answers it:

```
has_badge(event_id: 1, attendee: Ben's address)  →  true
```

or, for the full record:

```
badges_of(event_id: 1, attendee: Ben's address)  →  [badge with issue time]
```

Verification needs no permission, no account, and no trust in Ada — the
contract is the record. What the recruiter *cannot* learn from the chain is
who Ben is: the chain holds hashes and addresses, never names (see
[Privacy](privacy.md)).

## 4. The records live on

TTL handling (how long records stay readable — see the [glossary](glossary.md))
is computed from each event's real `closes_at` deadline plus a 30-day margin,
with a 7-day floor. The event and its badges stay verifiable well past the
claim window without anyone paying to store them forever.

## What this example skips

- **Ada's tickets exist only on the success screen.** The app generates her
  100 codes, builds the tree in the browser, and shows one paste-able ticket
  per attendee — but as 100 lines of text to copy, with no QR code, print view,
  or way to see them again (drafts 01 and 02 in the app repo). Distributing
  them stays Ada's manual job.
- **Nobody has been through these steps.** The app has a screen for each of
  them (`eventbadges-app`: home, organizer, claim, verify) and they call
  exactly the entrypoints above, but it is **built, never run**. No wallet has
  connected, signed or submitted through it, and no contract is deployed, so
  not one of the calls above has ever executed. Read the screens as written
  and unexercised, not as a transcript of something that happened.
- Nothing has been deployed to any network, so no real event id, address or
  transaction exists. Every value above is a placeholder.
