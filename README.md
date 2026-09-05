# Event Ticketing — Knowledge Base

The single source of truth for this system's domain language, requirements, design and
API contract. This repository's `main` branch is **normative**: where anything here
disagrees with code, a diagram, a chat message or a document elsewhere, this wins.

## Contents

| Path | What it is |
|---|---|
| `CONTEXT.md` | The glossary. The words we use, and the ones we don't. No implementation detail. |
| `design/domain-model.md` | Entities, relationships and the invariants that must always hold. |
| `nfr.md` | The numbers the system is designed against. |
| `requirements/` | Use cases with numbered acceptance criteria. |
| `contracts/openapi.yaml` | The hand-written, normative API contract. |
| `docs/adr/` | Decisions that were hard to reverse, and why we made them. |

## What lives elsewhere

Physical database schema and migrations belong in the backend repository; they are an
*implementation* of `design/domain-model.md`. Wireframes and screen designs belong in the
frontend repository, next to the code that supersedes them.

## Changing anything here

Open a pull request against this repository. The backend and frontend repositories pin a
**merged** revision of this one — a local commit, a passing validator or a local merge is
never authorisation to begin dependent implementation work.

`contracts/openapi.yaml` is hand-written and the server is built to match it, not the other
way round. See [ADR-0003](docs/adr/0003-knowledge-base-is-normative.md).
