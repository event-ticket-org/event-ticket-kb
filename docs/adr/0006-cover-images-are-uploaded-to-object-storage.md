# Cover images are uploaded to our object storage, not linked from elsewhere

An Event's cover was a URL an organizer pasted, which made the picture somebody else's file
on somebody else's server. Link rot is the ordinary end of that arrangement, not the edge
case: hosts start blocking hotlinking, accounts lapse, paths change, and a listing quietly
loses its covers over a year with nobody having edited anything. A URL is not immutable
either — what we show can change without an Event changing. And an organizer with nowhere to
host a file simply cannot have a cover, which is most organizers.

So the system stores the file. `coverImageUrl` stays as the read model — it is still a URL,
and every consumer of it is unchanged — but it now points into storage we own and is written
only by uploading.

## The upload goes straight to storage, and is checked afterwards

The client asks for an upload, posts the file directly to the store, and then asks the system
to adopt it. Bytes never pass through the application.

That buys throughput at the cost of the obvious place to validate, so the validation moves
rather than disappearing. The authorisation we hand out is narrow: we choose the key, so a
caller cannot write anywhere else; the signed conditions cap the size and require an image
content type; and it expires in minutes. Then adoption is a second, explicit request, and
that is where the file is actually examined — the content type a client declared is a claim,
and the file's own leading bytes are the answer. Nothing is referenced by an Event, or
reachable at a served URL, until it has passed.

## Consequences

An upload that is begun and never confirmed leaves an object nobody asked about. Those land
under a prefix of their own with an expiry rule on it, because the alternative is a bucket
that only grows and a cleanup job somebody has to remember to write.

Replacing a cover deletes the one it replaced, and removing a cover deletes the file. An
Event's cover is one file, and a store full of the ones it used to have is a store nobody can
reason about.

The contract describes the upload rather than naming the provider: a URL, the form fields to
send, and the field the file goes under. This is [ADR-0002](0002-payment-session-abstraction.md)'s
rule applied to a second thing — a client that reads the answer works against any store, and
one that hardcodes a provider's shape has to be rewritten to move.

Locally this is a container beside the database, so the system still runs with no cloud
account and no credentials. Anything else makes "clone it and run it" untrue, which is a
worse price than the container.

Removing a cover is now its own verb, `DELETE /events/{eventId}/cover`. That is a nicer answer
than the one we were reaching for: `EventPatch` has no way to say "clear this field", and
inventing a null convention for one field would have been inventing it for every optional
field in the contract.

Owning the file means owning its size. Because the picture is ours and not a link, the moment
it is confirmed is a moment we hold the bytes and may render smaller copies of them
(requirements/003 criterion 22) — which a linked image could never have offered. That is the
upside of this decision arriving late: the listing draws a 140px band, and without derived
sizes it downloads whatever a designer exported.

**Not every format can be resized, and the ones that cannot are served as they arrived.** The
JVM reads JPEG and PNG; WebP needs a library and AVIF has no decoder worth trusting. Refusing
AVIF would be narrowing the contract to fit an implementation detail, and failing the upload
would turn an optimisation into an outage. So a cover that cannot be decoded offers no smaller
sizes and is served whole — the client falls back to the one URL, and a visitor pays in
bandwidth rather than in a missing picture. It is worth knowing that AVIF is the most efficient
of the four formats, so the file we cannot shrink is the one least likely to need it.
