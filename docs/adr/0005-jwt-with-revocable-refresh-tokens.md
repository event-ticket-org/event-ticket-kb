# Short-lived access JWTs with server-stored refresh tokens

Authentication uses a 15-minute access JWT verified statelessly, paired with a refresh token
held server-side and revocable. Plain long-lived JWTs were rejected because this system has a
hard revocation requirement — Gate Staff are volunteers added for one night and removed
afterwards, and a removed volunteer must not keep scanning.

## Considered Options

Server-side sessions solve revocation directly and would be entirely adequate at this
system's scale, where a session lookup is invisible. JWT was chosen deliberately over it.
A revocation denylist on long-lived tokens was rejected as reintroducing a per-request store
lookup — sessions with extra steps.

## Consequences

Revocation takes effect at refresh, so a Role change has a window of up to 15 minutes on
ordinary endpoints. The scan endpoint closes that window to zero by checking live Membership
at scan time; it is already reading the database for the Ticket, so the check costs one join.
Any future endpoint where stale authorization would be damaging must do the same rather than
trusting the token's claims.
