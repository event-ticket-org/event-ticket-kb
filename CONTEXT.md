# Event Ticketing

A multi-tenant platform where organizations host events, sell seated tickets, and admit
attendees by scanning a QR code at the door.

This file is the glossary: the words we agree to use, and the ones we agree not to.
It contains no implementation detail. Schemas, APIs and technology choices live elsewhere.

## Language

### People

**User**:
A person with an account. One identity for everyone: buying tickets and working for an
Organization are things a User does, not different kinds of account.
_Avoid_: Customer, Member (means something else here), Buyer as an entity

**Buyer**:
The User who placed a given Order. A description of a role in context, not a separate record.
_Avoid_: Purchaser, Client, Customer

**Attendee**:
The person a Ticket admits. Deliberately not recorded — Tickets are anonymous, and whoever
presents a valid Ticket Code is admitted.
_Avoid_: Guest, Holder, Participant

### Tenancy

**Organization**:
The tenant. Owns venues, events, members, and payment configuration. Every piece of
data in the system belongs to exactly one Organization.
_Avoid_: Tenant, Company, Host, Promoter, Account

**Membership**:
The link between a person and an Organization, carrying exactly one Role. A person may
hold memberships in several Organizations.
_Avoid_: Staff, Seat (overloaded), Association

**Role**:
What a Membership permits. One of Owner (billing and payment configuration), Manager
(creates and runs events), or Gate Staff (scans tickets, cannot see sales or revenue).
_Avoid_: Permission, Access level

### Places

**Venue**:
A physical place an Organization holds events in. Owns a reusable Seat Map.
_Avoid_: Location, Place, Hall

**Seat Map**:
The arrangement of seats in a Venue. Reusable across events, and editable at any time —
editing it never affects an event that has already been published.
_Avoid_: Layout, Floor plan, Seating chart, Seat plan

**Event Seat Map**:
The copy of a Venue's Seat Map taken when an Event is published. Frozen from that moment,
so that a sold Ticket always refers to a seat that still exists and still means the same thing.
_Avoid_: Snapshot, Frozen map, Event layout

**Seat**:
A single admittable position in a Seat Map, identified by a label that a human can find in
the room (for example `H-14`).
_Avoid_: Spot, Position, Place

### Selling

**Event**:
Something an Organization sells admission to, held at a Venue at a point in time. Moves
through Draft, Published, SalesClosed and Completed, and may be Cancelled from any of them.
_Avoid_: Show, Concert, Session, Performance

**Publish**:
The act that makes an Event visible and puts its tickets on sale. Freezes the Event Seat Map
and the Venue. The governing rule thereafter: nothing a sold Ticket depends on may change under it.
_Avoid_: Go live, Release, Open sales

**Seat Hold**:
A time-limited claim on a Seat, taken when a buyer begins checkout and released automatically
when it expires. Prevents two buyers from paying for the same Seat.
_Avoid_: Reservation, Lock, Temporary booking, Cart

**Order**:
A buyer's purchase of admission to one Event, which produces one Ticket per Seat bought.
_Avoid_: Purchase, Transaction, Booking, Sale

**Pricing Tier**:
A named price band within an Event (for example VIP, Standard, Balcony). Every sellable
Seat belongs to exactly one Tier, and the Tier carries the price.
_Avoid_: Category, Class, Price level, Zone

**Payment Session**:
One attempt to pay for an Order. Moves through AwaitingPayment to Paid, Failed or Expired,
and is confirmed by the payment provider rather than by the buyer returning to the site.
An Order may have several over its life; at most one succeeds.
_Avoid_: Payment, Charge, Transaction, Checkout

**Refund**:
The return of money for a paid Order, initiated by the Organization and confirmed by the
payment provider. Moves through RefundPending to Refunded or RefundFailed. A redeemed
Ticket can never be refunded.
_Avoid_: Reversal, Cancellation (means something else here), Chargeback

### Admission

**Ticket**:
The right of one person to be admitted to one Event, usually at a specific Seat. Exists
independently of any QR code that represents it, so it can be reissued, transferred or
revoked without ceasing to be the same Ticket.
_Avoid_: QR, Pass, Entry, Admission

**Ticket Code**:
The credential printed as a QR code, which identifies a Ticket and carries no other
information. Several codes may be issued for one Ticket over its life; only the current
one is accepted.
_Avoid_: QR, Barcode, Ticket ID, Token

**Scan**:
The act of reading a Ticket Code at the door. Every Scan is recorded, including the ones
that are refused.
_Avoid_: Check, Validation, Verification

**Redemption**:
The state change a successful Scan causes: a Ticket becomes used and cannot admit anyone
again. A Ticket is redeemed once, by one Scan.
_Avoid_: Check-in, Use, Consumption, Entry
