# 002 — Venue and seat map

A Manager describes the rooms their Organization uses and draws the seats in them. The seat
map is the reusable asset: drawn once for a room, used by every Event held there.

The editor is a grid canvas with a row generator as a shortcut. Not a freeform CAD tool, and
not a generator alone — a generator cannot express a real room, and freeform is a project in
its own right.

## Acceptance criteria

1. A Manager may create a Venue with a name, address and timezone.
2. A Venue has exactly one Seat Map, created empty.
3. A Manager may add seats by generating a block — a section name, a number of rows and a
   number of seats per row — which places and labels them automatically.
4. A Manager may add, move and remove individual seats on the grid after generating.
5. Every seat carries a human-readable label unique within the Seat Map, and a position.
6. A Manager may relabel a seat; the system refuses a label that duplicates another.
7. A Manager may place non-sellable elements — stage, entrance, aisle, bar — which render on
   the map and can never be ticketed.
8. A Manager may assign each seat a Pricing Tier name. Prices are not set here.
9. A Seat Map may be edited at any time. Edits never affect an Event that is already
   published.
10. The editor remains usable at 2,000 seats: selection, panning and zooming stay responsive.
11. A Venue that is referenced by a published Event cannot be deleted.
