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
No — nothing is deployed, and there is no pilot. You can build and test the
contract and the app yourself; the commands are in the
[quickstart](quickstart.md). Deployment is gated on a real organizer agreeing
to pilot it ([pilot playbook](pilot-playbook.md)).

**There is an app. Why can't I use it?**
It is written, and nobody has used it. All four v0 screens are built and pass
their checks, but **no wallet has ever connected, signed or submitted through
it**, and no contract call has ever reached a deployed contract — because none
exists. The contract id in the app's configuration is still a placeholder, so
the app shows a configuration notice instead of a working screen. Until a real
pilot happens, "built" and "works" are two different things in this project,
and the app is on the wrong side of that line.

**Is this audited? Who reviewed it?**
No audit and no second reviewer. The contract is one person's work, locally
verified (35 tests, strict lints) and honestly limited
([limitations](limitations.md)). Testnet only, and treat it accordingly.

**Why hashes instead of the event name?**
A hash keeps names off the ledger, but be clear-eyed: human-chosen names
have a small enough space that the hash is "obscured", not "secret"
([privacy](privacy.md)).

**What comes next?**
A real deployment and a real pilot — the app is written, but in this project
"built" has not yet once meant "used". After that, docs-queryable pages for
verifiers. The full list lives in the ROADMAPs of the three repos. Two status
markers are used throughout, and they are not interchangeable:
**Not implemented yet** (planned, no code) and **Built, never run** (written
and checked, never executed against the real thing). Never "coming soon" with
dates.
