# Non-Functional Requirements

The numbers this system is designed against. These are design targets, not measurements.

## Scale

| Property | Target |
|---|---|
| Seats per Event | up to 2,000 |
| Concurrent buyers during an on-sale spike | 500 |
| Arrival window at the door | 45 minutes |
| Simultaneous scanning devices per Event | up to 4 |
| Scan submitted to result displayed | under 500 ms on venue wifi |

These are deliberately modest. They are large enough to make seat-hold concurrency and
redemption atomicity genuinely hard, and small enough that no queueing system, CDN or
read-replica is warranted.

## Admission

Verification is **online only**. The scanner is a browser application; there is no offline
mode and no local ticket cache. The server is the sole authority on whether a Ticket is
redeemed, which makes double-admission across simultaneous devices impossible by
construction rather than by reconciliation.

## Ticket Codes

A Ticket Code is the only thing standing between a stranger and free entry, because
verification is online and a valid code is sufficient.

- Cryptographically random, minimum 128 bits of entropy.
- Unpredictable and unorderable. No sequence, no counter, no timestamp, and nothing an
  attacker holding one code can use to reach another.
- **A leaked database is not a set of working tickets.** What is stored must not be
  sufficient to walk through a door: a code is reconstructed for display from stored data
  plus a key held outside the schema, so reading the table is not enough to forge one.
- Scan attempts are rate-limited per device.
- A Ticket Code is never written to a log. It arrives in the body of a scan request, which
  makes the request log the easiest place to leak every code presented at a door.

## Cover images

One image per Event, uploaded to object storage the system owns (ADR-0006).

| Property | Target |
|---|---|
| Maximum upload | 5 MB |
| Accepted types | JPEG, PNG, WebP, AVIF |
| Upload authorisation valid for | 10 minutes |
| Unconfirmed uploads expire after | 24 hours |

Five megabytes is generous for a poster and small enough that no resizing pipeline is
warranted — which is the point of naming it. Images are served as uploaded; there are no
derived sizes, no CDN and no transformation, for the same reason the scale targets above
warrant none.

The accepted types are decided by the file's own leading bytes, not by what a client
declared. A declared content type is a claim made by whoever is uploading, and the whole
reason to check is that they might be wrong or lying.

## Time

All instants are stored in UTC. All times shown to a human are rendered in the **Venue's**
timezone, never the browser's — a buyer in Da Nang looking at a Hanoi event must see
Hanoi's local start time. Vietnam is ICT (UTC+7) with no daylight saving, which makes this
easy to get wrong and never notice.

## Money

Single currency (VND) in v1, but Money is modelled as amount plus currency throughout.

**VND has no minor unit** — ISO 4217 exponent 0, no cents. The usual "store money as
integer minor units" advice therefore means the minor unit *is* the dong: `100000` is one
hundred thousand dong. Payment providers expect amounts in the smallest unit and treat
zero-decimal currencies differently. Getting this backwards is a factor-of-100 error.

## Personal data

The system deliberately holds one identifier per User — an email address — plus a display
name. Tickets are anonymous; no attendee identity is recorded. Any change that widens this
surface is a decision to be made explicitly, not a schema convenience.

## Availability

No high-availability requirement. A single application instance and a single Postgres
instance are sufficient. Scheduled downtime outside event hours is acceptable.
