# Tenant isolation uses Postgres row-level security, not application filtering

Every table carries an `organization_id` in a shared schema, and Postgres row-level security
enforces the boundary rather than the application's `WHERE` clauses. We chose this because
the classic multi-tenant breach is one forgotten predicate on one endpoint, and
application-layer filtering fails silently — it returns another tenant's rows and looks like
a successful request.

## Considered Options

Schema-per-tenant and database-per-tenant both isolate more strongly, and both were rejected
as operational burdens disproportionate to this system: migrations must then run across N
schemas, and neither teaches anything the shared-schema approach does not.

## Consequences

Every connection must set the tenant context before querying, and any code path that bypasses
it (migrations, background jobs, admin tooling) needs a deliberate, reviewed exemption. This
is the cost of the guarantee: the database refusing is only useful if nothing routinely asks
it not to.
