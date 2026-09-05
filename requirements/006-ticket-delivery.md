# 006 — Ticket delivery

The buyer receives their Tickets. Email carries a link to the ticket page rather than
attached images, so that the QR shown is always the current one — an attached image lives in
an inbox forever and cannot be withdrawn.

## Acceptance criteria

1. On payment confirmation, the buyer receives an email confirming the Order and linking to
   its ticket page.
2. The ticket page is behind the buyer's login and lists every Ticket in the Order with its
   seat label, Pricing Tier, Event name, Venue and start time in the Venue's timezone.
3. Each Ticket renders its current Ticket Code as a QR code.
4. A buyer may download an individual Ticket's QR image to send to whoever will use it.
5. A buyer may see all their Orders across Organizations in one place.
6. Reissuing a Ticket Code invalidates the previous one; the ticket page shows the new one
   without the buyer taking any action.
7. Ticket emails are sent through an interface with a local-development implementation and a
   real one, so that neither the test suite nor local development depends on a mail provider.
8. Email delivery failure is recorded and retried, and never silently swallowed — a buyer who
   did not receive their ticket is the failure this system exists to prevent.
9. A buyer may re-send the Order email to themselves from the ticket page.
