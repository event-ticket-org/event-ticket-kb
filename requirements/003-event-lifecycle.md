# 003 — Event lifecycle

A Manager creates an Event, prices it, and publishes it. Publishing is the moment the
system's promises begin: from then on, nothing a sold Ticket depends on may change under it.

States: `Draft → Published → SalesClosed → Completed`, with `Cancelled` reachable from any.

## Acceptance criteria

1. A Manager may create an Event in `Draft` with a title, description, Venue and start time.
2. While `Draft`, the Event shows the Venue's current Seat Map, and reflects edits to it.
3. A Manager sets a price for each Pricing Tier name present in the Seat Map. Publishing is
   refused while any tier is unpriced.
4. A Manager may mark seats as not for sale for this Event; they appear on the map as
   unavailable.
5. Publishing requires an `Approved` Organization, a priced tier for every sellable seat, a
   start time in the future, an admission window (criterion 16), and at least one sellable
   seat.
6. Publishing copies the Venue's Seat Map into an immutable Event Seat Map.
7. After publishing, the Venue and the Event Seat Map cannot be changed by any means.
8. After publishing, title, description and the cover image remain editable.
9. Changing the start time after publishing is permitted and notifies every ticket holder by
   email. The confirmation states how many people will be notified before it happens.

   The new time is still in the future. Criterion 5 requires that to publish, and a published
   Event is not free of the rule afterwards: moving one backwards emails everybody who holds a
   ticket a date that has already been and gone, and leaves a door that will not open because
   its admission window closed before the message arrived. There is no honest reason to do it -
   an Event that has already happened is `Completed`, which is a status rather than an edit.
10. Changing a tier's price after publishing applies only to later sales. Existing Tickets
    and active Seat Holds keep the price they were created with.
11. Sellable capacity may be increased but never reduced below the number of Tickets sold.
12. `SalesClosed` stops new Orders and leaves existing Tickets valid and scannable.
13. Every Event has a public page reachable by a shareable link, whatever its listed status.
14. An Event may be listed or unlisted, defaulting to listed. See
    [009](009-event-discovery.md) for the listing itself.
15. Publishing, price changes, start-time changes, admission-window changes and listing
    changes are written to the audit log.
16. An Event has an **admission window**: doors open at `doorsOpenAt` and admission closes at
    `endsAt`, with `doorsOpenAt` no later than the start time and `endsAt` after it. Both are
    set before publishing, and both remain editable afterwards like the start time.

    They exist because [007](007-admission-scanning.md) criterion 4 has to distinguish "not
    open yet" from "this event is over", and a start time alone cannot answer either question:
    people arrive before an event starts and leave after it does. Without a window those two
    outcomes are opinions rather than answers.
17. An Event may have one **cover image**. It is uploaded to the system's own storage rather
    than linked from elsewhere, so the picture cannot stop existing because somebody else's
    server changed. See [ADR-0006](../docs/adr/0006-cover-images-are-uploaded-to-object-storage.md).
18. Only images are accepted, and only up to a published size. What a client says it is
    sending is not what decides this: the file itself is examined after it arrives, and one
    that is not an image is discarded rather than served.
19. A cover may be replaced or removed at any point the Event is still editable. Replacing
    one deletes the image it replaced.
20. A cover carries alt text written by the organizer, describing what the picture shows. It
    is optional, and an undescribed cover is marked decorative rather than described with the
    Event's own title — which is beside it already, and would be read out twice.
21. An Event without a cover is an ordinary Event. Nothing anywhere shows a placeholder in
    its place.
22. A cover is offered in several sizes, and a client is told which exist so it can fetch the
    one it will actually draw. A listing drawing a thumbnail must not download a poster: the
    file an organizer uploads is sized for the machine they uploaded it from, and the buyer
    this product is built for is on a phone paying for the bytes.

    Only sizes smaller than what was uploaded are produced — enlarging a small image invents
    detail and costs bandwidth to do it — so an Event may offer one size or none, and the
    original is always served when nothing smaller exists. A format the system cannot decode
    is served exactly as uploaded rather than refused; the sizes are an optimisation, and one
    that fails should cost a visitor bandwidth rather than a picture.
23. An Event reports, to the people who run it, how many seats have sold and how much money is
    currently held for it. Both are read constantly and neither can be worked out from what a
    client already has: seats sit in different Pricing Tiers, prices change after publishing
    for later sales only, and a refunded Order stops counting — so any sum a client attempted
    would be a plausible wrong number, which is worse than none.

    Money held, not money ever taken: a refunded Order has given the money back, and a figure
    that still counted it would tell an organizer they hold funds they do not.

    Not for everyone in the Organization. [007](007-admission-scanning.md) criterion 13 keeps
    sales figures and revenue away from Gate Staff, and this is one of the figures it means.
