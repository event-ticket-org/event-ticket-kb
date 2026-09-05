# 008 — Refunds and cancellation

**Deferred to v1.1.** Recorded here because the decisions were made and should not be
re-litigated, and because ADR-0002's refund lifecycle depends on them.

A refund is a second money flow with its own provider lifecycle. It is deferred so that the
first flow is proven before the payment surface doubles.

## Acceptance criteria

1. Only an Owner or Manager may initiate a refund. Buyers cannot refund themselves in v1.1.
2. A refund moves through `RefundPending` to `Refunded` or `RefundFailed`, confirmed by the
   provider. It is never modelled as an instant boolean.
3. A Ticket that has been redeemed can never be refunded.
4. Refunding an Order voids its Tickets, which then fail at scan with a distinct reason.
5. Voided Tickets release their seats back to available if the Event is still on sale.
6. Cancelling an Event voids every Ticket and refunds every paid Order.
7. Event cancellation is a tracked bulk operation showing per-Order status, because it will
   partially fail and someone must see which Orders did.
8. Every ticket holder is notified by email when an Event is cancelled.
9. Refunds and cancellations are written to the audit log with actor and instant.
