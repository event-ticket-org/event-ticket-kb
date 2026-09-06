# Payment providers sit behind a Payment Session, not a charge method

Payment providers in our market differ in flow, not merely in credentials: VNPay redirects
the buyer to a hosted page, PayOS and SePay display a VietQR and confirm by webhook, and
Stripe hosts a checkout session. We model a provider-agnostic Payment Session with an
explicit lifecycle, and each provider returns a "next action" the frontend interprets.

## Consequences

There is deliberately no `charge()` method; a reader looking for one will not find it.
Payment is never synchronous, and the buyer returning to the site is not what confirms an
Order — the provider's webhook is. Every provider normalises its callback into one internal
confirmation event, and that handling must be idempotent, because webhooks arrive more than
once, out of order, and sometimes after the buyer has abandoned the tab.

An abstraction over mechanisms that differ in flow has to model the flow. A fake provider
plus one hosted-checkout provider will not prove this design; a second provider with a
genuinely different flow is what tests it.

## What the second provider found

Stripe was added, and the port held: one class, no contract change, and `verify(rawBytes,
headers)` turned out to fit a signature scheme nobody had in mind when it was written. Three
defects came out with it, and they share a shape worth naming — **the fake never does the
thing that breaks them**, so nothing noticed for eight slices.

- **Verifying a delivery could only answer "here is a confirmation".** A real provider sends
  many events per payment and cares about two; the rest had to be dressed up as confirmations
  of nothing, and arrived indistinguishable from a delivery for a session nobody has, which is
  a warning worth reading. Ten per sale bury it. Verification now has a way to say "genuine,
  and about nothing here".
- **A provider's reference is its own.** Stripe reverses a PaymentIntent while our Payment
  Session holds a Checkout Session, so refunding costs an extra lookup. That is the price of
  the port being right: requiring every provider to hand back the same *kind* of handle would
  put one provider's internals into a port that also has to fit a bank transfer.
- **"Settled" is not final**, which is the serious one and is now
  [008](../../requirements/008-refunds-and-cancellation.md) criterion 11. A refund reported
  succeeded can be reversed by the issuer hours later. The design assumed a one-way lifecycle
  because that is what one-and-a-half providers looked like.

The lesson is not "test more". It is that a fake built from one provider's flow encodes that
provider's *assumptions* as well as its shape, and those assumptions are invisible until
something else disagrees with them.

## Refunds

Refunds follow the same rule and for the same reason: `RefundPending → Refunded |
RefundFailed`, confirmed by the provider, never modelled as an instant boolean. Refunds are
initiated by the Organization, not the buyer, and are forbidden once a Ticket has been
redeemed. Cancelling an Event refunds every paid Order as a tracked bulk operation with
per-order status, because it will partially fail and someone has to see which ones did.
