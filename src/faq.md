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
anything about a new claim from that address; a badge already recorded
stays readable.

**Who can see that I claimed a badge?**
Everyone. Blockchain data is public: your address, the event's
(obscured) name hash, and timing are readable and linkable
([privacy](privacy.md)).

**Can the organizer take my badge away?**
Yes — `revoke` exists for badges issued in error, works at any time, and
the removal event stays on-chain
([threat model](threat-model.md#stride), Repudiation row).

**What if the claim code leaks?**
Whoever has it can claim, until the cap or the deadline stops them. The hash
cannot be changed; the organizer's options are short windows, tight caps,
and revoking fraudulent badges ([what can go
wrong](what-can-go-wrong.md#problems-no-error-code-will-save-you-from)).

**Is this deployed? Can I try it?**
No — nothing is deployed, and there is no pilot. You can build and test the
contract yourself from the [contracts repo](quickstart.md). Deployment is
gated on a real organizer agreeing to pilot it ([pilot
playbook](pilot-playbook.md)).

**Is this audited? Who reviewed it?**
No audit and no second reviewer. The contract is one person's work, locally
verified (24 tests, strict lints) and honestly limited
([limitations](limitations.md)). Testnet only, and treat it accordingly.

**Why hashes instead of the event name?**
A hash keeps names off the ledger, but be clear-eyed: human-chosen names
have a small enough space that the hash is "obscured", not "secret"
([privacy](privacy.md)).

**What comes next?**
The web app (`eventbadges-app`, planned next), then docs-queryable pages for
verifiers. The full list lives in the ROADMAPs of the three repos. Things
the code does not do are marked **Not implemented yet** everywhere, never
"coming soon" with dates.
