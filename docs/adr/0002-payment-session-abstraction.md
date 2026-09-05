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
