# Glossary

Plain definitions, as used in this book and this project.

**Address** — a public identifier on Stellar (starts with `G…` on
testnet/mainnet). Controls by its matching secret key. Pseudonymous: it
identifies the key, not the person.

**Badge** — in this project: a record inside the contract that says "this
address attended this event at this time". Not a token; it cannot move.

**Claim code** — a random secret string the organizer generates and shares
out-of-band, one per attendee. Proves attendance when its SHA-256 leaf is
shown to be one of the event's committed codes. Only hashes touch the chain.

**Contract** — the deployed program on Soroban (`eventbadges`). Holds the
rules and the records; holds no funds.

**Entrypoint** — a public function of the contract. There are seven:
`create_event`, `claim`, `award`, `revoke`, `get_event`, `has_badge` and
`badges_of` (see [architecture](architecture.md#3-core-protocol-objects)).

**Event (blockchain sense)** — a signed-off announcement the contract
publishes for indexers and apps to watch. Not to be confused with a
meetup-style "event", which this book always calls an *event* in the
organizer sense; the four contract events are named in
`docs/events.md` of the contracts repo.

**Hash (SHA-256)** — a 32-byte fingerprint of data: same input, same
output; infeasible to reverse *if the input was random and long*.

**Ledger** — one block of the Stellar chain, closed roughly every 5
seconds. Timestamps and TTLs are measured against it.

**mdBook** — the tool that builds this book from `src/*.md`.

**Merkle tree** — commits many secrets with one 32-byte value: each code
becomes a *leaf*, leaf hashes are combined pairwise into a *root*, and a
claim carries one leaf plus its *proof* (the sibling hashes up to the root).
The root is public; no code can be recovered from it.

**Persistent entry** — a piece of contract storage that survives between
calls until its TTL (below) lapses. Each event, badge and attendee-list
entry is one.

**require_auth** — the contract's demand that a specific address signed the
call. The mechanism behind every "only the organizer can…" on this book.

**SEP-41** — Stellar's token interface standard. This contract implements
*none* of it, deliberately — that is what makes badges non-transferable.

**Testnet** — Stellar's practice network. Free tokens, no real value, data
can be reset. Everything in this project is testnet-only.

**TTL (time-to-live)** — how many more ledgers a storage entry stays
readable before it archives. Extended automatically here from each event's
real deadline plus a 30-day margin, floored at 7 days
(`src/storage.rs` in the contracts repo).

**Wallet** — an app that holds keys and signs calls (e.g. a browser
extension or mobile wallet). The wallet signs; it never shares the key.
