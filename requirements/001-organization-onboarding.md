# 001 — Organization onboarding

Anyone may register an account and create an Organization. An Organization may build events
freely, but may not put tickets on sale until a platform administrator has approved it. This
is the fraud control: it costs one flag and one screen now, and prevents the platform being
used to sell tickets to events that do not exist.

This document also owns the other end of an account's life: getting back into one. Criteria
17 to 21 are here rather than in a document of their own because recovery is not a separate
feature, it is what registration and verification look like when the person has lost the
first two — and the mistake to avoid is a second mechanism sitting beside them with its own
rules.

That makes approval the most privileged action in the system, so criterion 13 says where the
privilege comes from. Left unsaid, the obvious implementation is an endpoint, and an endpoint
that can make its caller an administrator is worth more to an attacker than any other request
here.

## Acceptance criteria

1. A visitor may register with an email address and password, and must verify the email
   address before taking any action that creates a Seat Hold.
2. A verified User may create an Organization and becomes its Owner.
3. A newly created Organization is `PendingApproval`.
4. An Organization that is not `Approved` may create Venues, seat maps and Events, and may
   not publish an Event.
5. Attempting to publish while unapproved fails with an explanation of why and what happens
   next, not a generic error.
6. A platform administrator can approve or reject an Organization; both outcomes notify the
   Owner by email. A rejection carries a reason, and that reason is readable afterwards by the
   administrator as well as sent to the Owner — a queue of rejected Organizations that does not
   say why any of them was rejected cannot be reviewed, only re-read.
7. An Owner may invite a User to the Organization by email address, assigning one Role:
   Owner, Manager or Gate Staff.
8. An invited User who has no account is prompted to register; the Membership activates on
   verification.
9. An Owner may change a Member's Role or remove them. Removal takes effect immediately for
   scanning, and within the access-token lifetime elsewhere.
10. An Organization must retain at least one Owner; removing the last Owner is refused.
11. A User may hold Memberships in several Organizations and switches between them
    explicitly. The active Organization is never taken from a URL parameter.
12. Membership changes are written to the audit log with actor and instant.
13. A platform administrator is designated by deployment configuration and by nothing else.
    No request grants or revokes the privilege, because the endpoint it unlocks decides who
    may sell tickets on the platform at all. An address named in the configuration becomes
    an administrator whether it is named before or after that account is created.
14. Deciding on an Organization shows who is accountable for it: the Owners, their addresses,
    and whether those addresses have been verified. Approving decides who may sell tickets to
    the public on a page this platform endorses, and a name and a date is not enough to decide
    that on.
15. A decision may be revisited. An Organization rejected in error is approved by approving it,
    and one approved in error is stopped by rejecting it — there is no separate appeal, and a
    decision that could only be made once would make a mistaken rejection permanent.
16. A Member can see where their Organization stands — approved, waiting, or rejected and why —
    without attempting something that will be refused. Criterion 5 makes the refusal at publish
    time explain itself, and that is necessary but late: by then somebody has built a Venue, a
    Seat Map, an Event and its prices believing they were about to sell tickets. Waiting is a
    legitimate state that can last days, and a person who cannot see they are in it reads it as
    the product being broken. The rejection reason of criterion 6 is shown here as well as
    emailed, because the email is the copy that gets lost.
17. A User who has forgotten their password may ask for a reset by email address, and the
    answer is the same whether or not that address has an account. The form is public and
    unauthenticated, so an answer that differed would make it a tool for finding out who has
    an account here — and a list of this platform's buyers is worth stealing on its own.
18. Following a reset link sets the new password and marks the address verified. Reading the
    mailbox is the same proof registration asks for, and this closes the one door with no way
    through it: a User whose verification email was lost or expired currently has no route
    back into their own account at all. Recovering the password recovers the account, rather
    than a second mechanism existing to recover the verification.
19. Setting a new password ends every other session immediately. The common reason to reset
    is that somebody else has the old password, and a reset that leaves their session alive
    fixes nothing. The person resetting stays signed in — they have just proved who they are —
    and is not asked to type the new password again.
20. A completed reset is reported to the address itself. That notice is the only thing that
    tells a real owner a reset happened when it was not them, and it is worth sending even
    though the account is by then already changed: it is what makes the next step possible.
21. A reset link expires, may be used once, and asking for a new one ends any still
    outstanding. A link sitting in a mailbox is a live credential to the whole account, so the
    window in which a forwarded or a shoulder-read message is still usable is deliberately
    short.
