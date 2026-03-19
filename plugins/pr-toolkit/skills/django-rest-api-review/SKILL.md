---
name: django-rest-api-review
description: Comprehensive Django REST API code review for DDD projects using HackSoft Django Styleguide. Use when reviewing Django apps with REST APIs for architecture compliance (Service/Selector pattern), database schema (PostgreSQL), REST API design, code quality (N+1, security), and domain alignment (event storming, domain models). Triggers on "review Django", "review API", "review PR", "code review" for Django projects.
---

# Django REST API Review

Review Django REST APIs for DDD compliance, HackSoft Styleguide patterns, and architecture quality.

## Input Methods

1. **PR**: "Review PR #123" or "Review current PR"
2. **Files**: "Review backend/atlas/resources/"
3. **Commits**: "Review last 3 commits"

## Review Workflow

### Phase 0: Scope & Artifacts

1. Identify affected Django apps and files
2. Search for DDD artifacts:
   - `docs/event-storming/{focus-area}/` - domain events, commands, aggregates, policies
   - `docs/domain-models/{context}.json` - entities, invariants, value objects
   - `docs/api/{context}/` - endpoints, schemas, business rules
   - `docs/architecture/` - patterns, components
3. Read relevant artifacts, extract invariants and requirements

### Phase 1: Architecture Compliance

See [references/architecture-patterns.md](references/architecture-patterns.md) for detailed patterns.

**Layer Separation Check**:

| Layer | ✅ Should Have | ❌ Should NOT Have |
|-------|---------------|-------------------|
| Models | Entity fields, TextChoices, `clean()`, constraints | Business logic, queries |
| Services | Write ops, `@transaction.atomic`, `*,` args, type hints | HTTP concerns, query building |
| Selectors | Read ops, QuerySets, `select_related`/`prefetch_related` | Write ops, business logic |
| APIs | Thin orchestration, nested serializers, `@extend_schema` | Business logic, direct ORM |

### Phase 2: Database Schema

See [references/database-patterns.md](references/database-patterns.md) for detailed patterns.

**Check**:
- UUID PKs with `db_index=True`
- TextChoices for enums, ArrayField for lists
- Indexes: GIN for arrays/JSON, partial, BRIN for timestamps
- Constraints: CHECK, UNIQUE, self-reference prevention
- **CRITICAL**: All invariants from domain model enforced (DB constraint OR `clean()`)
- Concurrency: F() expressions, `select_for_update()`, `refresh_from_db()`

### Phase 3: REST API Design

See [references/api-patterns.md](references/api-patterns.md) for detailed patterns.

**Check**:
- RESTful URLs (nouns not verbs), proper HTTP methods/status codes
- Separate Input/Output serializers, nested for relations
- RFC 7807 error format, custom exceptions
- No info leakage (404 for 403 on private resources)

### Phase 4: Testing

See [references/testing-patterns.md](references/testing-patterns.md) for detailed patterns.

**Check**:
- Organized by layer: `test_models.py`, `test_services.py`, `test_selectors.py`, `test_apis.py`
- Test entity builders with fluent interface
- Service tests cover business logic paths and invariants
- Selector tests verify N+1 prevention
- API tests verify permissions and errors

### Phase 5: Code Quality

**N+1 Prevention (CRITICAL)**:
- For each list selector: identify relations in serializer → verify `select_related`/`prefetch_related`

**Other Checks**:
- Type hints on services/selectors
- Docstrings with Args/Returns/Raises
- Structured logging (`extra={}`)
- Security: permissions, validation, no injection, CSRF

### Phase 6: Domain Alignment

If artifacts exist, verify:
- Commands → service functions + POST/PUT/PATCH/DELETE endpoints
- Aggregates → model definitions + transaction boundaries
- Business rules → service validation + tests
- Read models → selectors + GET endpoints
- **ALL invariants enforced** (critical)

## Output Format

```markdown
## 1. Executive Summary
- Overall: ✅ Excellent / ⚠️ Needs Work / ❌ Critical Issues
- Key findings (3-5 bullets)
- Critical issues count

## 2. Critical Issues
❌ CRITICAL: [description]
Location: [file:line]
Issue: [explanation]
Impact: [why critical]
Fix: [code change]

## 3. Architecture Compliance
[✅/⚠️/❌ for: Layer Separation, Service Pattern, Selector Pattern, API Pattern, Model Design]

## 4. Database Schema
[✅/⚠️/❌ for: Model Structure, Indexes, Constraints, Invariants, Concurrency]
Missing invariants: [list]

## 5. REST API
[✅/⚠️/❌ for: RESTful Principles, Schemas, Error Handling, Business Rules, OpenAPI]

## 6. Testing
[✅/⚠️/❌ for: Organization, Builders, Coverage, Edge Cases]
Untested paths: [list]

## 7. Code Quality
[✅/⚠️/❌ for: N+1 Prevention, Type Hints, Docstrings, Logging, Security]

## 8. Domain Alignment
[If artifacts exist: alignment status, mismatches]

## 9. Recommendations
**CRITICAL**: [numbered list]
**HIGH**: [numbered list]
**MEDIUM**: [numbered list]
**LOW**: [numbered list]

## 10. Positive Highlights
[3-5 things done well]
```

## Review Principles

- Read artifacts FIRST before code
- Be specific with file:line references
- Provide code examples for fixes
- Prioritize critical issues (security, correctness, architecture)
- Acknowledge good patterns
