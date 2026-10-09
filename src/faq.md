# FAQ

Short answers. Longer ones are linked.

**Is this a token? Can I sell my badge?**
No, and that is the point. There is no transfer, approve or operator
function in the contract, so a badge cannot leave the address it was issued
to ([architecture](architecture.md#6-trust-boundaries), and the contracts
repo's decision doc `0001-nft-approach.md`).

**Do I need cryptocurrency to claim a badge?**
You need a Soroban wallet and a little XLM on the **testnet** for
transaction fees — testnet XLM is free and has no real value. You never send
money to anyone to get a badge.

**Where is my badge stored?**
Inside the contract, as a record bound to your address — not "in" your
wallet. Your wallet's private key is what lets *you* prove the record is
yours by signing `claim`. Losing the key means losing the ability to prove
anything about a new claim from that address. A badge already recorded stays
readable while its entry is alive — the contract keeps it to about 30 days
past the event's deadline and tops it up whenever anyone reads it — but an
event nobody touches for months can be archived by the network, and this app
cannot restore it yet.

**Who can see that I claimed a badge?**
Everyone. Blockchain data is public: your address, the event's
(obscured) name hash, and timing are readable and linkable
([privacy](privacy.md)).

**Can the organizer take my badge away?**
Yes — `revoke` exists for badges issued in error, works at any time, and
the removal event stays on-chain
([threat model](threat-model.md#stride), Repudiation row).

**What if the claim code leaks?**
Each attendee has their own code, and a successful claim spends it, so a
leaked code can take **one** place — its owner's — and only if the holder gets
there first. A second use of the same code fails (`ClaimCodeUsed`). The
stored root cannot be turned back into any code, and it cannot be changed
after creation; the remedy for a stolen place is procedural: the organizer
revokes the badge that used the code and awards one to the attendee who was
shut out ([what can go
wrong](what-can-go-wrong.md#problems-no-error-code-will-save-you-from)).

**Is this deployed? Can I try it?**
The contract has a synthetic testnet deployment, and the frontend is hosted at
https://eventbadges-testnet-xteesamz.vercel.app. No pilot has happened, and the
browser-wallet business flow has not been validated. See the
[deployment record](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/docs/TESTNET_DEMONSTRATION.md)
and [quickstart](quickstart.md); do not use real funds.

**There is an app. Why can't I use it?**
The frontend is hosted, and the contract has a synthetic testnet deployment.
However, **the browser-wallet business flow has not been validated**: no
organizer or attendee flow has been confirmed end to end through the app. A
hosted interface and contract deployment do not establish that the product is
ready for a pilot or production.

**Is this audited? Who reviewed it?**
No audit and no second reviewer. The contract is one person's work, locally
verified (35 tests, strict lints) and honestly limited
([limitations](limitations.md)). Testnet only, and treat it accordingly.

**Why hashes instead of the event name?**
A hash keeps names off the ledger, but be clear-eyed: human-chosen names
have a small enough space that the hash is "obscured", not "secret"
([privacy](privacy.md)).

**What comes next?**
A manually verified browser-wallet flow and a real pilot. After that,
docs-queryable pages for
verifiers. The full list lives in the ROADMAPs of the three repos. Two status
markers are used throughout, and they are not interchangeable:
**Not implemented yet** (planned, no code) and **Built, never run** (written
and checked, never executed against the real thing). Never "coming soon" with
dates.
