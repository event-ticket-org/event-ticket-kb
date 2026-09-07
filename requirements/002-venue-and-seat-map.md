# 002 — Venue and seat map

A Manager describes the rooms their Organization uses and draws the seats in them. The seat
map is the reusable asset: drawn once for a room, used by every Event held there.

The editor is a grid canvas with a row generator as a shortcut. Not a freeform CAD tool, and
not a generator alone — a generator cannot express a real room, and freeform is a project in
its own right.

## Acceptance criteria

1. A Manager may create a Venue with a name, a street address, a **city** as a distinct
   field, and a timezone. City is separate from the address because the listing in
   [009](009-event-discovery.md) filters on it, and a free-text address cannot be filtered.
2. A Venue has exactly one Seat Map, created empty.
3. A Manager may add seats by generating a block — a section name, a number of rows and a
   number of seats per row — which places and labels them automatically.
4. A Manager may add, move and remove individual seats on the grid after generating.
5. Every seat carries a human-readable label unique within the Seat Map, and a position
   measured in seat pitches — one unit is roughly one seat, so neighbours in a row are 1 apart
   and an aisle is the extra unit between them. The origin and the units are otherwise
   arbitrary, because a Seat Map has no real-world dimensions and is fitted to whatever it is
   drawn in; the *scale* is not arbitrary, because a seat is drawn at a fixed size in these
   units and a map spaced twenty units apart is valid, unremarkable to every check we have,
   and unreadable.
6. A Manager may relabel a seat; the system refuses a label that duplicates another.
7. A Manager may place non-sellable elements — stage, entrance, aisle, bar — which render on
   the map and can never be ticketed.
8. A Manager may assign each seat a Pricing Tier name. Prices are not set here.
9. A Seat Map may be edited at any time. Edits never affect an Event that is already
   published.
10. The editor remains usable at 2,000 seats: selection, panning and zooming stay responsive.
11. A Venue that is referenced by a published Event cannot be deleted.
