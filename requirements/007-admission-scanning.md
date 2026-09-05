# 007 — Admission and scanning

Gate Staff scan Ticket Codes at the door. Verification is online only: the server is the sole
authority, which makes double admission across simultaneous devices impossible by
construction.

The scanner is a route in the same application, with its own minimal layout — no navigation,
large touch targets, a result readable at arm's length. It is used one-handed, in the dark,
with a queue waiting.

## Acceptance criteria

1. A signed-in User with Gate Staff, Manager or Owner Membership may open the scanner for an
   Event belonging to their Organization.
2. The scanner requests camera permission once and reads QR codes continuously.
3. A valid, unredeemed Ticket for this Event is redeemed and the result shows admitted, with
   the seat label large enough to direct the person.
4. Every other outcome shows a distinct, human-readable reason: already redeemed, wrong
   event, ticket cancelled or refunded, event not yet open, event ended, unknown code. "Not
   yet open" and "ended" are judged against the Event's admission window
   ([003](003-event-lifecycle.md) criterion 16), never against its start time.
5. Already redeemed shows **when** and **at which device** it was first redeemed, so staff can
   tell "you already went in" from "someone else used your ticket".
6. Every scan is recorded whatever the outcome, with the scanning User, device and instant.
7. There is no override or force-admit. Staff cannot admit a refused Ticket.
8. Authorisation is checked against live Membership on every scan, so a removed Gate Staff
   member stops being able to scan immediately.
9. Two devices scanning the same code simultaneously: exactly one records a redemption, the
   other reports already redeemed.
10. Scan submitted to result displayed completes within 500 ms under the load in `nfr.md`.
11. Losing connectivity shows an explicit failure. The scanner never admits optimistically.
12. Scan attempts are rate-limited per device.
13. Gate Staff see no sales figures, revenue or buyer details anywhere in the scanner.
