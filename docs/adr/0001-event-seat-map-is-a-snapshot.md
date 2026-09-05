# Event Seat Map is a snapshot, not a reference

A Venue owns a reusable Seat Map, but publishing an Event copies that map into an
immutable Event Seat Map rather than referencing it. We did this because a sold Ticket
must still refer to a seat that exists and means the same thing months later, even if
the venue has since renumbered the hall or removed a row.

## Considered Options

Referencing the Venue's live Seat Map is simpler and keeps one copy of the truth, but it
lets an edit to a Venue silently corrupt tickets already sold for past and future events.
Giving each Event its own map from scratch preserves correctness but throws away reuse,
which is the main reason organizers tolerate a seat-map editor at all.

## Consequences

Editing a Venue's Seat Map affects only Events published afterwards. An Event in Draft
tracks the Venue map; publishing freezes it. Seat identity is therefore scoped to an
Event, not to a Venue, and any query joining tickets to seats must go through the Event.
