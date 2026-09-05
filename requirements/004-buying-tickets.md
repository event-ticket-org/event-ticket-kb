# 004 — Buying tickets

A buyer opens an Event's public page, picks seats on the map, and checks out. Seat selection
is free and optimistic; the commitment — and the clock — starts at checkout.

## Acceptance criteria

1. Anyone may view an Event's public page and its seat map without an account.
2. The map shows each seat as available, unavailable or held, coloured by Pricing Tier, with
   the tier's price visible.
3. Availability updates while the page is open, so a buyer is not choosing from a stale map.
4. A visitor may select seats before signing in. Proceeding to checkout requires a signed-in
   User with a verified email address.
5. Beginning checkout creates a Seat Hold on each selected seat, valid for 10 minutes.
6. If any selected seat was taken between selection and checkout, checkout fails naming the
   specific seats, and the remaining selection is preserved.
7. A held seat cannot be held or bought by anyone else.
8. The buyer sees the remaining hold time throughout checkout.
9. When a Seat Hold expires the seats return to available immediately, and a buyer still on
   the checkout page is told clearly rather than failing at payment.
10. Two buyers checking out for the same seat at the same instant: exactly one succeeds. The
    other is refused before any payment is attempted.
11. A buyer may abandon checkout, releasing the holds at once rather than waiting for expiry.
12. An Order is created for one Event and produces one Ticket per seat bought.
13. Tickets are anonymous: no per-attendee name or detail is collected.
