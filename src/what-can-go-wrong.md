# What can go wrong

Plain-language versions of every failure the contract can report, for the
non-technical reader. The authoritative table — with the exact wording the
app displays, and must never reword — is
[`ERRORS.md`](https://github.com/stellar-eventbadges/eventbadges-contracts/blob/main/ERRORS.md)
in the contracts repo. Each row below links the error's code and variant.

## When claiming or receiving a badge

| What you see | What it means | What to do |
|---|---|---|
| "We couldn't find that event." (`EventNotFound`, 1) | The event id is wrong, or the event was never created. | Check the id with the organizer. |
| "That claim code is not valid for this event." (`ClaimCodeMismatch`, 11) | The code's SHA-256 does not match the event's stored value — a typo, or the wrong event's code. | Check the code character by character with the organizer; codes are long on purpose. |
| "This address already holds a badge for this event." (`AlreadyHeld`, 13) | This wallet claimed (or was awarded) already. | Nothing to do — open the existing badge. |
| "This event has no badges left to issue." (`CapReached`, 12) | Every slot the organizer set is taken. | Ask whether another run is planned. |
| "The claim window for this event has closed." (`EventClosed`, 10) | The deadline passed. Claims cannot reopen. | Ask the organizer whether another proof of attendance exists. |

## When organizing

| What you see | What it means | What to do |
|---|---|---|
| "The badge cap must be between 1 and 10,000." (`MaxClaimsTooLarge`, 30) | The cap was 0 or above the contract's limit. | Recreate the event with a cap in range. |
| "The claim deadline must be in the future." (`ClosesAtInPast`, 31) | The deadline chosen already passed (clocks drift; so does deliberation). | Recreate with a later deadline. |
| "That address has no badge for this event." (`BadgeNotFound`, 2) | Tried to revoke a badge the attendee does not hold. | Check the address. Revoking is not repeatable: once the badge is gone, the next revoke fails the same way. |

## Problems no error code will save you from

- **You lost the claim code.** The hash is on-chain; the secret is not
  recoverable from it. Attendees who were not told the code can still be
  served with `award` — that is what it exists for.
- **The code leaked before the event.** Anyone with it can claim while the
  window is open and the cap allows. The contract cannot rotate the hash.
  Mitigation is procedural: short window, tight cap, share out-of-band, and
  `revoke` abusive badges (the count frees up again).
- **The attendee's wallet was compromised.** A badge follows the address,
  and the contract cannot know who *should* hold it. `revoke` removes the
  badge; the `badge_claimed` event stays on-chain (see
  [privacy](privacy.md), deletion is not a thing).
- **Transaction failed but the wallet says "submitted".** A rejected call
  changes nothing on-chain — re-reading `has_badge` is always the ground
  truth. (Built, never run: the app shows the transaction hash and an explorer
  link when a write succeeds, and the claim flow re-reads the badge list after
  a successful claim. It does **not** re-read after a failed one — press "Show
  badges" to check. None of this has yet met a real failure.)
