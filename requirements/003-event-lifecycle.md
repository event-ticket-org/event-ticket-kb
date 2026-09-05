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
   start time in the future, and at least one sellable seat.
6. Publishing copies the Venue's Seat Map into an immutable Event Seat Map.
7. After publishing, the Venue and the Event Seat Map cannot be changed by any means.
8. After publishing, title, description and images remain editable.
9. Changing the start time after publishing is permitted and notifies every ticket holder by
   email. The confirmation states how many people will be notified before it happens.
10. Changing a tier's price after publishing applies only to later sales. Existing Tickets
    and active Seat Holds keep the price they were created with.
11. Sellable capacity may be increased but never reduced below the number of Tickets sold.
12. `SalesClosed` stops new Orders and leaves existing Tickets valid and scannable.
13. Every Event has a public page reachable by a shareable link, whatever its listed status.
14. An Event may be listed or unlisted, defaulting to listed. See
    [009](009-event-discovery.md) for the listing itself.
15. Publishing, price changes, start-time changes and listing changes are written to the
    audit log.
