# 009 — Event discovery

A public page listing events across Organizations, so that a visitor who arrives without a
link can still find something to go to.

Deliberately a filtered list, not a marketplace. Ranking, relevance scoring, a category
taxonomy, personalization and SEO work are what make discovery expensive, and none of them
do anything a filter list does not. If discovery ever becomes a reason people arrive, revisit.

Listing an Event on our own page is an implicit endorsement of it, which is what makes the
Organization approval gate in [001](001-organization-onboarding.md) load-bearing rather than
merely prudent.

## Acceptance criteria

1. Anyone may view the listing without an account.
2. The listing shows only Events that are `Published`, belong to an `Approved` Organization,
   are marked listed, and have not yet started.
3. Each entry shows the Event title, the Organization's name, the Venue and city, the start
   time in the Venue's timezone, the cover image, and the lowest ticket price as a "from"
   price.
4. A visitor may filter by city and by date range, and results are ordered by start time,
   soonest first.
5. There is no ranking, relevance scoring, category taxonomy or personalization. Ordering is
   by start time and nothing else.
6. The listing is paginated and stays responsive as the number of Events grows.
7. A Manager may mark an Event unlisted. An unlisted Event does not appear in the listing and
   remains fully reachable and purchasable by its direct link.
8. Events default to listed. Unlisting is a deliberate act, recorded in the audit log.
9. An Event's public page is reachable by link whatever its listed status, so an existing
   link never breaks because someone changed a setting.
10. `SalesClosed` and `Cancelled` Events do not appear in the listing.
