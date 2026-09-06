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
   provider. It is never modelled as an instant boolean, and `Refunded` is **not** terminal —
   see criterion 11.
3. A Ticket that has been redeemed can never be refunded.
4. Refunding an Order voids its Tickets, which then fail at scan with a distinct reason.
5. Voided Tickets release their seats back to available if the Event is still on sale.
6. Cancelling an Event voids every Ticket and refunds every paid Order.
7. Event cancellation is a tracked bulk operation showing per-Order status, because it will
   partially fail and someone must see which Orders did.
8. Every ticket holder is notified by email when an Event is cancelled.
9. Refunds and cancellations are written to the audit log with actor and instant.
10. An Owner or Manager can see an Event's Orders and narrow them to the ones needing a
    refund. `refund_required` is set by the system, and a flag nobody can find is the same as
    no flag: criterion 1 puts refunding in a person's hands, so that person has to be able to
    reach the Order.
11. **A provider may report a refund settled and then reverse that.** When it does, the Refund
    returns to `RefundFailed` and the Order is marked as needing a refund again. Both, because
    they answer different questions: the Refund records what actually happened to the money,
    and `refund_required` is what puts the Order in front of a person — and by this point a
    person is needed, because the buyer has already been told they were refunded.

    This is not hypothetical and it is not a provider being unusual. Stripe answers
    `refund.created` with status `succeeded` and, once the issuer rejects the card, follows it
    with `refund.failed` — five events, the first three saying it worked. A system that treats
    the first settlement as final tells a buyer their money is back, releases their seats, and
    never learns otherwise; ours did exactly that until a card whose refunds always fail was
    put through it.

    A reversal is therefore the one transition that runs backwards, and everything downstream
    has to accept that `Refunded` was true when it was written and is not true now. The audit
    log keeps both (criterion 9), because "we said it worked and it did not" is the sentence
    somebody will need.
