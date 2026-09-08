# 005 — Payment

The buyer pays for an Order. Providers differ in flow, not merely in credentials, so the
system models a Payment Session with a provider-agnostic lifecycle and asks the provider what
should happen next. See [ADR-0002](../docs/adr/0002-payment-session-abstraction.md).

v1 ships a fake provider and Stripe's sandbox. The fake is what makes the test suite run
without a network; Stripe is what proves the interface is not a fantasy. A second provider
with a genuinely different flow — VNPay's redirect or PayOS's VietQR — is what will actually
test the abstraction, and is not in v1.

## Acceptance criteria

1. Confirming an Order creates a Payment Session in `AwaitingPayment` and returns a next
   action: a redirect, a QR payload to display, or a hosted checkout URL.
2. The frontend renders whichever next action it is given without knowing which provider
   produced it.
3. An Order transitions to paid only on receipt of a provider confirmation. The buyer
   returning to the site never confirms an Order.
4. Confirmation handling is idempotent. The same webhook delivered twice produces one paid
   Order and one set of Tickets.
5. Confirmations arriving out of order, or after the buyer abandoned the tab, are handled
   correctly.
6. A confirmation for an unknown or already-settled session is acknowledged and ignored, not
   an error.
7. Webhook authenticity is verified before the payload is trusted.
8. Tickets are issued only after confirmation, and every seat's Seat Hold converts to a
   Ticket atomically — never some seats of an Order and not others.
9. If a Seat Hold expired before confirmation arrives, the Order fails and the payment is
   flagged for refund rather than silently keeping the money.
10. A Payment Session expires if unconfirmed, releasing the holds.
11. A buyer may retry payment on an unpaid Order while its holds are alive. An Order has many
    attempts and at most one success.
12. Amounts are handled in the currency's smallest unit, and VND's absence of a minor unit is
    respected on every provider boundary.
13. A provider that answers is not a provider that is down. A refusal it will give again for
    the same request — an amount below its floor, a currency it will not present, an
    unconfigured account — is permanent, and telling a buyer to try again in a moment invites
    them to keep pressing a button that will never work while the organizer hears nothing.

    Only a provider that could not be reached is worth retrying. Anything it actually said is
    reported as what it is, in words the buyer can act on, and logged with what the provider
    said so that the organizer's problem is diagnosable rather than merely reported.
