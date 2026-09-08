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

## Account recovery

A password reset link is a bearer credential to a whole account, so it is held to the Ticket
Code's rules and not to a link's: cryptographically random, at least 128 bits of entropy,
nothing derived from a database identifier, and only its hash stored — a leaked table must not
be a set of working links.

| Property | Target |
|---|---|
| Reset link valid for | 1 hour |
| Uses per link | 1 |
| Outstanding links per User | 1; a new request ends the previous |
| Requests accepted per address | rate-limited |
| Reset emails sent per hour, all addresses | capped |

The lifetime is short where email verification's is a day, and the difference is what the link
does. A verification link makes a new account usable and the person is usually waiting for it;
a reset link opens an account that already exists, already holds orders and tickets, and may
already be the subject of whatever prompted the reset.

Requests are rate-limited per address, because the endpoint is unauthenticated and sends mail
to an address the caller names. Without a limit it is a way to have this system deliver
unbounded mail to a stranger, and the reputational cost of that lands on the sending domain.

The second limit is a cap on reset mail in total rather than per caller, and that is a
correction of something this document said first. Per caller cannot be enforced where it would
have to be: the application is reached through a tunnel and a reverse proxy, so every request
arrives from the proxy's address, and a limit keyed on it is a single global limit that one
attacker trips to lock every User out of recovery. The alternative is to trust a forwarded
header, which is to key the limit on a value the attacker writes.

A total cap has neither problem and bounds the thing actually worth bounding — how much mail
this system can be made to send to people who did not ask for any. It is set high enough that
reaching it means abuse rather than a busy evening, and per-caller limiting belongs at the edge,
where the caller's address is known.

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

## What the server checks

Every constraint this contract states is enforced by the server, whatever a client does. A
client checks the same things because a person should be told about a mistake while they are
still looking at the field, not after a round trip - but that is a courtesy, and courtesy is
not a control. The two are not alternatives and the client's is never the one relied upon.

This is the same rule the cover images already follow: the accepted types are decided by the
file's own leading bytes, not by what a client declared. A request is a claim made by whoever
sent it, and the whole reason to check is that they might be wrong or lying.

Two consequences worth stating, because both have been got wrong here:

- A constraint written in the contract and not enforced by the server is decoration. It is
  worse than no constraint, because everyone reading the contract believes it holds.
- A request the server refuses is a **4xx with which field was wrong**. A refusal that arrives
  as 500 tells the caller the server broke when it understood perfectly, and buries a real
  defect in the log at the moment the log matters.

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

| | |
|---|---|
| Smallest chargeable price | **20.000 ₫** |
| Free | allowed, and distinct from cheap |

A price is free or it is one somebody can actually be charged. Providers refuse amounts below
a floor of their own — Stripe answers `amount_too_small` with "must convert to at least 50
cents", which against the dong is roughly 12.500 ₫ and moves with the exchange rate. So the
number here is not that floor; it is comfortably above it, because a limit that tracks a
foreign currency will eventually cross a limit that does not, and the failure lands on a buyer
who has already chosen a seat.

It is configuration rather than a constant. The floor a provider applies is theirs, this
market's idea of a real ticket price is not ours, and a deployment that changes provider or
currency should not need a release to change a number.

## Personal data

The system deliberately holds one identifier per User — an email address — plus a display
name. Tickets are anonymous; no attendee identity is recorded. Any change that widens this
surface is a decision to be made explicitly, not a schema convenience.

## Availability

No high-availability requirement. A single application instance and a single Postgres
instance are sufficient. Scheduled downtime outside event hours is acceptable.
