# Requirements

Use cases in prose with numbered acceptance criteria. Not Gherkin: `Given/When/Then` earns
its ceremony only when it binds to executable tests, and it does not here.

Each criterion is numbered so it can be cited in review and in a pull request description.

## v1

| # | Use case | Status |
|---|---|---|
| 001 | [Organization onboarding](001-organization-onboarding.md) | v1 |
| 002 | [Venue and seat map](002-venue-and-seat-map.md) | v1 |
| 003 | [Event lifecycle](003-event-lifecycle.md) | v1 |
| 004 | [Buying tickets](004-buying-tickets.md) | v1 |
| 005 | [Payment](005-payment.md) | v1 |
| 006 | [Ticket delivery](006-ticket-delivery.md) | v1 |
| 007 | [Admission and scanning](007-admission-scanning.md) | v1 |
| 009 | [Event discovery](009-event-discovery.md) | v1 |
| 008 | [Refunds and cancellation](008-refunds-and-cancellation.md) | **v1.1** |

v1 is the slice that lets an Organization run one real event end to end, plus a public
listing so that visitors arriving without a link can find one. Refunds are
deferred because they are a second money flow with their own provider lifecycle, and adding
them doubles the payment surface before the first half is proven.

Organization approval and row-level security are *not* deferred despite being invisible in a
demo. Both are painful to retrofit.
