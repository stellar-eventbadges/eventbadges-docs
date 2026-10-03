# A worked example

A plain-language walk through the full life of an event, using the real
entrypoints of the contract. The values are obviously fake placeholders — no
real event, person or address is involved.

## 1. The organizer creates an event

Ada runs a monthly robotics meetup. Before the event she:

1. Picks a random **claim code** — a long secret string — and keeps it
   offline. She never types it into the contract. (How codes are generated
   and shared: [the claim-codes doc](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/claim-codes.md).)
2. Computes the SHA-256 hash of that code. Only the hash goes on-chain, so
   the chain never holds the secret itself.
3. Calls the contract:

```
create_event(
    organizer:      Ada's wallet address,
    name_hash:      SHA-256 of "robotics meetup" (opaque bytes on-chain),
    claim_code_hash: the hash from step 2,
    max_claims:     100,
    closes_at:      a deadline one week after the event,
)
```

The contract stores the event and returns an **event id** (1, 2, 3, …). The
`organizer` address must sign this call — that is what stops anyone else from
creating events in Ada's name.

## 2. An attendee claims a badge

Ben attended the meetup. Ada gave him the claim code in person (a printout,
a QR code on the door — out-of-band, never on-chain). Ben opens any Soroban
wallet — or the app's claim screen — and calls:

```
claim(event_id: 1, attendee: Ben's wallet address, claim_code: <the secret>)
```

The contract checks, in order:

- the event exists;
- the cap (100 badges) is not reached;
- the deadline has not passed;
- Ben does not already hold a badge for this event;
- SHA-256 of the code Ben presented matches the stored hash.

All five pass → Ben's address is recorded as a badge holder for event 1.
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

- **Nobody has been through these steps.** The app has a screen for each of
  them (`eventbadges-app`: home, organizer, claim, verify) and they call
  exactly the entrypoints above — but it is **built, never run**. No wallet has
  connected, signed or submitted through it, and no contract is deployed, so
  not one of the calls above has ever executed. Read the screens as written
  and unexercised, not as a transcript of something that happened.
- Nothing has been deployed to any network, so no real event id, address or
  transaction exists. Every value above is a placeholder.
