# 008 — Refunds and cancellation

A refund is a second money flow with its own provider lifecycle. It was deferred so that the
first flow could be proven before the payment surface doubled; that flow is now built and
tested end to end, including the confirmation that arrives after a Seat Hold has lapsed and
leaves money we should not keep.

Two things brought it forward. `refund_required` already exists on an Order and nothing acts
on it, so v1 can already reach a state it cannot leave. And KB invariant 22 - cancelling an
Event voids every Ticket and refunds every paid Order - has no implementation at all, which
means an Event cannot presently be cancelled: the status exists and no code path sets it.

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
