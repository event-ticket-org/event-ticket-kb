# The knowledge base repository is normative, and the API contract is hand-written

This repository's `main` branch is the single source of truth for domain language,
requirements and the API contract. The backend and frontend repositories pin a merged
revision of it; a local or unmerged contract commit never authorises dependent work.
`contracts/openapi.yaml` is hand-written and the server is built to match it.

## Considered Options

Generating the OpenAPI spec from Spring annotations is less work and cannot drift. We
rejected it because it makes the code normative: a generated spec only ever describes what
was already built, and can never constrain it or be agreed before implementation. That
inverts the purpose of this repository.

## Consequences

CI in this repository must validate that `openapi.yaml` parses and lints, or the contract
becomes a document that lies slowly. Changing the contract is a pull request here first,
merged before either implementation repository consumes it.
