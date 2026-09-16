# 009 — Event discovery

A public page listing events across Organizations, so that a visitor who arrives without a
link can still find something to go to.

## Why this was revised

This began as a filtered list and deliberately not a marketplace. Ranking, relevance scoring,
a category taxonomy and personalization are what make discovery expensive, and a filter list
already found an Event for anybody who arrived knowing roughly what they wanted. The condition
written down for revisiting was discovery becoming a reason people arrive.

**That condition has not been met, and the listing is being rebuilt as a marketplace anyway.**
The reason is worth recording accurately rather than dressed up as demand: this platform is
built to be learned from, and the parts of discovery the original requirement excluded are the
parts with something to teach. A requirement revised for a reason outside the product should
say so, or the next person to read it infers traffic that never existed and trusts a number
nobody measured.

What it costs is written into the criteria below rather than left implied:

- **Ranking invents a second way for the listing to be wrong.** An Event can now be present and
  unfindable. A list ordered by time cannot do that — everything is somewhere, and the somewhere
  is predictable.
- **A taxonomy is a question every organizer has to answer** and every row has to carry, for the
  life of the schema, including the rows that do not fit any of the answers.
- **Two orderings is two behaviours to keep correct**, and the one that only appears with a
  search term is the one nobody looks at until it is wrong.

Both are accepted. Neither was free.

Listing an Event on our own page is an implicit endorsement of it, which is what makes the
Organization approval gate in [001](001-organization-onboarding.md) load-bearing rather than
merely prudent. **Ranking one Event above another is a stronger endorsement than listing it**,
which is why criterion 15 is narrow about what a ranked row is allowed to measure.

## Acceptance criteria

1. Anyone may view the listing without an account.
2. The listing shows only Events that are `Published`, belong to an `Approved` Organization,
   are marked listed, and have not yet started.
3. Each entry shows the Event title, the Organization's name, the Venue and city, the start
   time in the Venue's timezone, the cover image, the lowest ticket price as a "from" price,
   and how many seats are still on sale out of how many there were. Both numbers, because
   "four seats left" means something different in a room of twenty and a room of two thousand,
   and one number leaves a client to guess which.
4. A visitor may filter by city, by category, by date range, and by a text query, in any
   combination.
5. The listing has **two orderings and no others**: by start time, soonest first; and by
   relevance to a text query. Start time is the default, and is the only ordering available
   when there is no text query — relevance to nothing is not an ordering. A client asks for
   one explicitly rather than inferring which it got.

   **Personalization remains excluded.** No ordering, in either mode, may depend on who is
   asking. Two visitors sending the same request get the same page in the same order, signed in
   or not, and that is a property worth keeping: it is what makes the listing something a
   visitor can be shown, cited and complained about, rather than something only they saw.

   *(This criterion previously excluded ranking and relevance entirely. It is the rule the
   revision above changes, and it keeps its number because a criterion's number is how the other
   repositories cite it — see the note at the foot of this file.)*
6. The listing is paginated and stays responsive as the number of Events grows.
7. A Manager may mark an Event unlisted. An unlisted Event does not appear in the listing and
   remains fully reachable and purchasable by its direct link.
8. Events default to listed. Unlisting is a deliberate act, recorded in the audit log.
9. An Event's public page is reachable by link whatever its listed status, so an existing
   link never breaks because someone changed a setting.
10. `SalesClosed` and `Cancelled` Events do not appear in the listing.
11. A sold-out Event still appears, shown as sold out. Hiding it would make the listing
    disagree with what is on, and somebody who arrives too late is better told so than left
    wondering whether they looked in the wrong place.
12. **Every Event has exactly one Category**, chosen from a set the platform defines. Organizers
    pick from that set and never extend it: a taxonomy anybody may add to stops being one, and
    the rows it groups stop being comparable.

    The set includes a catch-all, and it is not a failure state — an Event that fits nothing
    else belongs there permanently. Categories are required at creation; Events that predate
    this requirement take the catch-all.
13. **A Venue's city is a value the platform defines**, not free text. The listing groups and
    counts by city, and two spellings of one city are two cities to anything that counts.
14. The listing may present **curated rows**: an ordered set of Events chosen by a platform
    administrator, each shown over a stated period. Curation is an editorial act by the
    platform rather than anything an Organization can buy or set, and it is recorded with actor
    and instant like every other such act.
15. A **ranked row** may rank by Tickets sold within a recent window, and by nothing else. Not
    by how often a page was opened, not by any signal derived from who is looking, and not by
    anything an Organization can influence except by selling Tickets.

    **The ranking is published; the figures behind it are not.** A position in a chart says one
    Event outsold another this week; a count says how much an Organization took, across
    Organizations, to anybody who loads the page. Sales figures are restricted even inside an
    Organization (007 criterion 13), and a public listing is not the place they stop being.
16. **A row with too few Events to look ranked is not shown at all.** A chart of two is not a
    chart, and a curated row with one Event in it reads as a fault rather than a selection. This
    applies to every row: the listing degrades by showing fewer kinds of thing, never by showing
    empty ones.
17. Where the listing offers a filter, it may also show **how many Events each choice would
    return**, counted under the filters already applied. A count that ignored the other filters
    would be a number for a page the visitor is not on.
18. The text query matches the Event title, its description, its Venue's name and its
    Organization's name. Matching is case-insensitive and accent-insensitive in both
    directions, because this market writes with diacritics and types without them.
19. There is **no autocomplete and no query suggestion**. A visitor types and submits. Suggesting
    queries means ranking queries, which is a second ranked surface with its own failure modes,
    to save keystrokes on a search box nobody has complained about.
20. **Whatever serves the listing is derived, never the record.** It is rebuildable from the
    Events themselves at any time, and losing it may degrade ordering or drop a filter — it may
    never take the listing down, lose an Event, or leave one stale after the Event itself
    changed. An Event exists in exactly one place, and that place is not the listing.
21. **Availability does not affect ordering.** A sold-out Event ranks exactly where it would
    with seats remaining, and is marked sold out per criterion 11.

    Seats sold and held change several times a second during an onsale. An ordering that moved
    with them would reshuffle under a visitor between one page and the next, and the row nobody
    could find again was the one they were reading. Stable and occasionally unhelpful beats
    correct and unrepeatable.

<!--
Criteria are referenced by number from the other repositories, so a new one is appended rather
than inserted where it reads best. Renumbering is silent: nothing fails, and every citation
elsewhere quietly starts pointing at the wrong rule.

Revising a criterion in place is the exception, and criterion 5 is the only one so far: its
number goes on meaning "the rule about how the listing is ordered", which is what the citations
were pointing at. A citation now reaches a rule that says something different, which is
findable. A renumber would have left them reaching the wrong rule while still reading correctly.
-->
