---
name: rest-api-design
description: Use when translating domain models, commands, read models, events, and business rules into pragmatic REST API documentation, especially when the result should be written as split docs/api/[domain-context] Markdown files.
---

# REST API Design

## Overview

This skill translates DDD artifacts (domain models, commands, read models, domain events, and business rules) into a set of structured REST API documentation files. It writes five Markdown files to disk under `docs/api/[domain-context]/` and keeps chat output minimal. `[domain-context]` is a placeholder for the inferred or user-supplied context name (e.g., `resources`, `profiles`).

## When to Use

- You have domain model definitions, commands, read models, events, or business rules and need to produce REST API docs.
- The output must live in `docs/api/[domain-context]/` as split Markdown files.
- You are expanding or updating API docs for an existing bounded context.

## Required Inputs

Provide as many of the following as are available:

- **Domain model** – aggregates, entities, value objects, and their fields
- **Commands** – write operations triggered by the user or system
- **Read models** – query shapes and filtering/sorting requirements
- **Domain events** – outcomes emitted after successful state changes
- **Business rules** – validation constraints, preconditions, invariants

## Output Contract

The skill produces **all five files below—no more and no fewer**—written separately and in this order:

| # | File | Contents |
|---|------|----------|
| 1 | `overview.md` | Purpose, scope, base URL, versioning, auth summary |
| 2 | `resources.md` | Resource hierarchy, endpoints, HTTP methods, path params, query parameters (filtering, sorting, pagination) |
| 3 | `schemas.md` | Request/response bodies, field types, required/optional |
| 4 | `examples.md` | Concrete request/response pairs for key operations |
| 5 | `business-rules.md` | Validation rules, preconditions, error codes (RFC 7807) |

**Rules:**
- Only these five filenames may be created or updated. Do **not** create additional files, and do **not** rename, substitute, or split these filenames (e.g. `endpoints.md` or `api-overview.md` are not valid substitutes).
- Create `docs/api/[domain-context]/` on disk before writing files.
- Write each file individually using the available file-creation or edit tools; do not return the full specification as chat text.
- Use relative links between files (e.g., `./schemas.md`, `./business-rules.md`).
- If the target directory already exists, overwrite only the five managed files; leave any other files in that directory untouched.
- End with a concise summary listing the files created or updated.

**File-specific formatting rules:**
- In `resources.md`, use a plain-text heading for each endpoint section in the form `### METHOD /path` — do **not** backtick-wrap the path portion (e.g., write `### POST /orders/{id}/approve`, not `` ### POST `/orders/{id}/approve` ``).
- In `examples.md`, head each example section with the generic endpoint pattern using `{id}` placeholders (e.g., `### POST /orders/{id}/approve`), then show the concrete request/response underneath.
- In `business-rules.md`, write state and status value names as plain prose in rule descriptions and table cells — do **not** wrap state/status values in backtick code spans (e.g., write "open state", not `` `open` state ``). Field names and other literal identifiers may still use backticks where appropriate.

## Workflow

1. **Identify** – Determine the target path: if the user explicitly provides a target domain context or target directory, use it exactly; otherwise infer the domain context name from the input artifacts. If multiple domain contexts are equally plausible, stop and ask the user for clarification before proceeding.
2. **Create directory** – Ensure `docs/api/[domain-context]/` exists.
3. **Write files** – Create or overwrite the five managed files in order (overview → resources → schemas → examples → business-rules).
4. **Cross-link** – Verify that relative links between files are consistent.
5. **Summarize** – Output a short list of the files created or updated and any notable design decisions.

## Common Mistakes

- ❌ Returning the full API spec in chat instead of writing files to disk.
- ❌ Producing non-standard filenames (e.g., `endpoints.md`, `events.md`) instead of the five required files.
- ❌ Skipping a file because the inputs seem incomplete—write a minimal stub instead.
- ❌ Using absolute paths in cross-links instead of relative links.
- ❌ Deleting or modifying unrelated files that already exist in the target directory.
- ❌ Asking the user to name the domain context when it can be inferred from the artifacts.

## Reference

For detailed REST patterns, DDD-to-HTTP mapping, the worked example, and the final checklist, read [`rest-api-reference.md`](./rest-api-reference.md).

Topics covered in the reference:

- REST resource modeling and HTTP method mapping
- Status codes, error design (RFC 7807), idempotency
- Request/response schemas, filtering, sorting, pagination, field selection
- Versioning, content negotiation, caching, security
- DDD artifact → REST mapping (commands, read models, events, aggregates)
- Structuring `business-rules.md` and mapping validation rules to RFC 7807 problem types
- Worked example (Order aggregate) and final design checklist
