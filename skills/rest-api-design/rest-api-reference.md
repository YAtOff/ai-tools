# REST API Design Reference

Detailed guidance on REST principles, DDD-to-HTTP mapping, worked example, and final checklist.
Used by [`SKILL.md`](./SKILL.md).

---

## Resource Modeling

- **Use nouns, not verbs**: Resources represent things (users, orders, products), not actions.
- **Prefer plural nouns** for collections: `/orders`, `/customers`.
- **Use singular nouns** for singleton resources: `/profile`, `/configuration`.
- **Organize hierarchically** when representing relationships: `/customers/{customer_id}/orders/{order_id}`.
- **Keep URIs lowercase** with hyphens for readability: `/product-categories`.
- **Map aggregates to resources**: Each aggregate root typically becomes a primary resource.

---

## HTTP Methods Mapping

### Map Commands to HTTP Verbs

| Method   | Use For                                              | Returns                                      |
|----------|------------------------------------------------------|----------------------------------------------|
| POST     | Create new resources or non-idempotent operations    | `201 Created` + `Location` header            |
| PUT      | Replace entire resource (idempotent)                 | `200 OK` or `204 No Content`                 |
| PATCH    | Partial resource updates                             | `200 OK` or `204 No Content`                 |
| DELETE   | Remove resources (idempotent)                        | `204 No Content` or `200 OK` with metadata   |
| GET      | Retrieve resources (safe and idempotent)             | `200 OK` — never change state with GET       |

### When to Use Action Resources (Pragmatic REST)

Some business operations don't map cleanly to CRUD. Create **action resources** using POST:

- Complex workflows: `POST /orders/{id}/approve`
- Multi-step processes: `POST /orders/{id}/ship`
- Calculations: `POST /pricing/calculate`

**Use action resources when**:
- The operation is inherently non-idempotent and requires a verb to express intent.
- The operation spans multiple aggregates.
- The operation represents a significant business event.
- Using PUT/PATCH would obscure the business meaning.

---

## Status Codes

**Success (2xx)**
- `200 OK`: Successful GET, PUT, PATCH, or DELETE with response body.
- `201 Created`: Successful POST creating a resource (include `Location` header).
- `202 Accepted`: Request accepted for async processing.
- `204 No Content`: Successful operation with no response body.

**Client Errors (4xx)**
- `400 Bad Request`: Malformed request or validation failure.
- `401 Unauthorized`: Authentication required or failed.
- `403 Forbidden`: Authenticated but lacks permissions.
- `404 Not Found`: Resource doesn't exist.
- `409 Conflict`: Business rule violation, concurrent modification.
- `422 Unprocessable Entity`: Semantic validation failure.
- `429 Too Many Requests`: Rate limit exceeded.

**Server Errors (5xx)**
- `500 Internal Server Error`: Unexpected server failure.
- `503 Service Unavailable`: Temporary unavailability.

---

## Request and Response Design

### Request Bodies (POST, PUT, PATCH)

- Use **JSON** as default format.
- Structure requests to match **command intent**, not just data structures.
- Include all data required to execute the command.
- Validate against business rules before processing.

### Response Bodies

- Return **meaningful resource representations**.
- For commands: Return the created/updated resource or relevant identifiers.
- For queries: Map directly from read models.
- Include **timestamps** (`created_at`, `updated_at`) when relevant.
- Consider **field projection**: Allow clients to request specific fields via query parameters.

### Problem Details (RFC 7807)

All error responses must follow the RFC 7807 Problem Details format:

```json
{
  "type": "https://api.example.com/docs/api/errors/validation-error",
  "title": "Validation Error",
  "status": 400,
  "detail": "Order total must be greater than zero",
  "instance": "/orders/123",
  "errors": [
    {
      "field": "total",
      "message": "Must be greater than zero"
    }
  ]
}
```

---

## Query Parameters

When mapping read models to GET endpoints, support the following patterns.

### Filtering

```
GET /orders?status=pending&customer_id=123
GET /orders?created_after=2024-01-01
```

- Use equality operators by default.
- Support comparison operators when needed: `created_after`, `created_before`.

### Sorting

```
GET /orders?order_by=created_at&direction=desc
GET /orders?order_by=-created_at,status
```

- Allow sorting by multiple fields.
- Use `asc`/`desc` or prefix with `-` for descending.
- **Note**: Cursor pagination requires a stable sort order.

### Pagination

Always paginate collections. Prefer cursor-based pagination for scalability:

```
GET /orders?cursor=abc123&limit=20
```

Response shape:
```json
{
  "data": [...],
  "pagination": {
    "limit": 20,
    "cursor": "abc123",
    "has_more": true
  },
  "links": {
    "self": "/orders?cursor=abc123&limit=20",
    "next": "/orders?cursor=def456&limit=20",
    "prev": "/orders?cursor=xyz789&limit=20"
  }
}
```

### Field Selection

```
GET /orders?fields=id,status,total
```

Reduces payload size for performance-sensitive clients.

---

## API Versioning

Version APIs from day one to enable evolution without breaking clients.

**URI Path Versioning (Recommended)**:
```
/api/v1/orders
```
Simple, visible, cacheable, and widely adopted.

**When to increment versions**:
- Breaking changes: Removing fields, changing response structure, altering behavior.
- Non-breaking changes (new optional fields, new endpoints) do **not** require a version bump.

**Version support strategy**:
- Support at least two versions simultaneously.
- Announce deprecation 6–12 months in advance.
- Provide migration guides.
- Monitor usage to identify migration opportunities.

---

## Content Negotiation

```http
# Client specifies acceptable formats
Accept: application/json
Accept: application/json, application/xml;q=0.9

# Server indicates content type
Content-Type: application/json
```

Default to JSON when no `Accept` header is provided.

---

## Caching

**Cache-Control headers**:
```
Cache-Control: public, max-age=3600
Cache-Control: private, no-cache
```

**ETags** (version identifiers):
```
ETag: "abc123xyz"                       # Server sends
If-None-Match: "abc123xyz"              # Client sends on retry
# → 304 Not Modified if unchanged
```

**Last-Modified** (timestamp-based):
```
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT   # Server
If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT  # Client
```

Apply caching to GET operations for read models, relatively stable data, and resources with predictable change patterns.

---

## Security Best Practices

**Authentication**:
- Use token-based authentication (JWT, OAuth 2.0) for stateless REST.
- Include tokens in `Authorization` header: `Authorization: Bearer {token}`.

**Authorization**:
- Implement RBAC or ABAC; enforce principle of least privilege.
- Validate permissions on every request.
- Check authorization at the business rule level, not just the endpoint level.

**Transport Security**:
- Always use HTTPS/TLS.
- Redirect HTTP → HTTPS.
- Use HSTS: `Strict-Transport-Security: max-age=31536000`.

**Input Validation**:
- Validate all inputs against business rules.
- Sanitize inputs to prevent injection attacks.
- Return specific validation errors without exposing system internals.

**Rate Limiting**:
- Implement per-client rate limits.
- Return `429 Too Many Requests` when exceeded.
- Include rate limit headers:

```
X-Rate-Limit-Limit: 1000
X-Rate-Limit-Remaining: 456
X-Rate-Limit-Reset: 1640995200
```

---

## Idempotency and Safety

**Safe methods** (don't modify state): `GET`, `HEAD`, `OPTIONS`
- Can be cached, prefetched, retried automatically.

**Idempotent methods** (same result regardless of how many times called): `GET`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`
- Safe to retry on network failures.
- PUT replaces the entire resource (same payload → same result).
- DELETE removes the resource (subsequent calls return 404, but state is the same).

**Non-idempotent methods**: `POST`, `PATCH`
- POST creates new resources (multiple calls = multiple resources).
- For critical operations, implement **idempotency keys**:

```http
POST /orders
Idempotency-Key: a1b2c3d4-e5f6-7890
```

The server stores the key and returns the same response for duplicate requests.

---

## HATEOAS — Pragmatic Approach

**When to include links**:
- To guide workflows: show available next actions based on resource state.
- For related resources: link to associated entities.
- For pagination: include first, last, next, prev links.

**Example**:
```json
{
  "id": "123",
  "status": "pending",
  "total": 150.00,
  "links": {
    "self": "/orders/123",
    "approve": "/orders/123/approve",
    "cancel": "/orders/123/cancel",
    "customer": "/customers/456"
  }
}
```

**Skip HATEOAS when**:
- Building internal APIs with tight coupling.
- Performance is critical (links add payload size).
- Clients expect fixed endpoints (most mobile/SPA scenarios).

---

## Domain Events in REST APIs

**Synchronous response** — return event data after the command succeeds:
```json
{
  "order_id": "123",
  "event": {
    "type": "OrderCreated",
    "occurred_at": "2024-01-15T10:30:00Z",
    "data": {}
  }
}
```

**Asynchronous processing** — return `202 Accepted` immediately:
- Provide a status endpoint: `GET /orders/123/status`.
- Use webhooks or message queues for event notifications.

**Webhook notifications** — push events to registered callbacks:
- Clients register: `POST /webhooks` with a callback URL.
- Server pushes events to registered endpoints.

**Event streams** — expose events via dedicated endpoints:
```
GET /events?since=2024-01-15T00:00:00Z
GET /events?event_type=OrderCreated
```

---

## Pragmatic REST Guidelines

**When to be strict**:
- Use proper HTTP methods and status codes (non-negotiable).
- Keep APIs stateless.
- Use resource-oriented URLs for CRUD operations.
- Follow standard conventions for discoverability.

**When to be pragmatic**:
- **Action endpoints**: `POST /resource/{id}/action` for complex business operations.
- **Bulk operations**: `POST /orders/bulk-create` for performance.
- **Search/filter complexity**: `POST /search` with a filter object in the body when query strings become unwieldy.
- **Orchestration operations**: composite endpoints when UI workflows require multiple related actions.

The goal is APIs that are **intuitive**, **maintainable**, **aligned with business concepts**, and **performant**.

---

## Mapping DDD Artifacts to REST APIs

### Commands → HTTP POST/PUT/PATCH/DELETE

| Intent                            | HTTP mapping                             |
|-----------------------------------|------------------------------------------|
| Non-idempotent creation           | `POST` to collection                     |
| Idempotent replacement            | `PUT` to specific resource               |
| Partial update                    | `PATCH` to specific resource             |
| Removal                           | `DELETE` to specific resource            |
| Complex business operation        | `POST` to action resource                |

**Examples**:
- `CreateOrderCommand` → `POST /orders`
- `UpdateOrderStatusCommand` → `PATCH /orders/{id}` with `{ "status": "confirmed" }`
- `ApproveOrderCommand` → `POST /orders/{id}/approve`
- `CancelOrderCommand` → `DELETE /orders/{id}` or `POST /orders/{id}/cancel`

### Read Models → HTTP GET

1. Map each read model to a GET endpoint.
2. Structure URL to reflect data hierarchy.
3. Add query parameters for filtering, sorting, pagination.
4. Consider multiple read models for the same aggregate (different projections).

**Examples**:
- `OrderSummaryReadModel` → `GET /orders?fields=id,status,total`
- `OrderDetailsReadModel` → `GET /orders/{id}`
- `CustomerOrderHistoryReadModel` → `GET /customers/{id}/orders`

### Domain Events → Response Metadata or Event Endpoints

1. Include relevant event data in command responses.
2. Expose event streams via dedicated endpoints for integration.
3. Consider webhook registration for push notifications.

**Examples**:
- After `POST /orders`: return the created order with event metadata.
- Event stream: `GET /events?aggregate_type=Order&since=2024-01-15T00:00:00Z`

### Business Rules → Validation and Status Codes

1. Validate at the API boundary before processing.
2. Return appropriate status codes: `400`/`422` for violations, `409` for conflicts.
3. Provide clear error messages referencing the violated rule.

**Example**:
Business Rule: "Order total must be greater than zero"
```
POST /orders
Request: { "items": [], "total": 0 }

Response: 400 Bad Request
{
  "type": "https://api.example.com/docs/api/problems/business-rule-violation",
  "title": "Business Rule Violation",
  "status": 400,
  "detail": "Order total must be greater than zero",
  "rule": "MinimumOrderTotal",
  "instance": "/orders"
}
```

### Aggregates → Resource Boundaries

1. Map aggregate roots to primary resources.
2. Nest child entities under the aggregate root: `/orders/{orderId}/items/{itemId}`.
3. Don't expose entities outside their aggregate.
4. Enforce aggregate boundaries: operations must respect consistency boundaries.

---

## Structuring `business-rules.md`

`business-rules.md` is a standalone reference for validation constraints, preconditions, and invariants. It must be readable without consulting `resources.md` or `schemas.md`.

**Organize by operation** — group rules under the endpoint that triggers them. Within each group, list rules as table rows:

| Rule | Endpoint | HTTP Status | Problem Type |
|------|----------|-------------|--------------|
| Order must have at least one item | `POST /orders` | `400 Bad Request` | `validation-error` |
| Order total must be positive | `POST /orders`, `PATCH /orders/{id}` | `422 Unprocessable Entity` | `business-rule-violation` |
| Only pending orders can be confirmed | `POST /orders/{id}/confirm` | `409 Conflict` | `state-conflict` |

**Connecting rules to HTTP status and RFC 7807:**

- Pick the status code based on the nature of the violation:
  - `400` — malformed input or missing required field.
  - `409` — valid input but current resource state prevents the operation.
  - `422` — semantically invalid input (correct type, invalid value or constraint).
- Assign a `type` URI suffix that identifies the rule category (e.g., `validation-error`, `state-conflict`, `business-rule-violation`); these should match the problem types used in error response examples in `examples.md`.
- Write state and status values as plain prose in rule descriptions (e.g., "pending state", not `` `pending` `` state); field names and other literal identifiers may still use backticks.

---

## Example Transformation

**Input DDD Artifacts**:
```
Aggregate: Order
Entities: Order (root), OrderItem
Commands: CreateOrder, AddItemToOrder, ConfirmOrder, CancelOrder
Read Models: OrderSummary, OrderDetails
Events: OrderCreated, ItemAdded, OrderConfirmed, OrderCancelled
Business Rules:
  - Order must have at least one item
  - Order total must match sum of items
  - Only pending orders can be confirmed
  - Only pending or confirmed orders can be cancelled
```

**Output REST API**:

### POST /orders
Create a new order.

**Request**:
```json
{
  "customer_id": "456",
  "items": [
    { "product_id": "789", "quantity": 2, "price": 25.00 }
  ]
}
```

**Response**: `201 Created`
```
Location: /orders/123
```
```json
{
  "id": "123",
  "customer_id": "456",
  "status": "pending",
  "total": 50.00,
  "created_at": "2024-01-15T10:30:00Z",
  "items": [
    { "id": "1", "product_id": "789", "quantity": 2, "price": 25.00, "subtotal": 50.00 }
  ]
}
```

**Business Rules**: Order must have at least one item; total must match sum of items.  
**Idempotency**: No — use `Idempotency-Key` header for safety.

---

### POST /orders/{id}/items
Add item to an existing order.

**Request**:
```json
{ "product_id": "790", "quantity": 1, "price": 15.00 }
```

**Response**: `200 OK`
```json
{ "id": "123", "status": "pending", "total": 65.00, "items": [...] }
```

---

### POST /orders/{id}/confirm
Confirm an order for processing.

**Response**: `200 OK`
```json
{ "id": "123", "status": "confirmed", "confirmed_at": "2024-01-15T11:00:00Z" }
```

**Business Rules**: Only pending orders can be confirmed.

---

### DELETE /orders/{id}
Cancel an order.

**Response**: `204 No Content`  
**Business Rules**: Only pending or confirmed orders can be cancelled.  
**Idempotency**: Yes.

---

### GET /orders
List orders with filtering and pagination.

**Query Parameters**:
- `status`: Filter by status (`pending`, `confirmed`, `shipped`, `cancelled`).
- `customer_id`: Filter by customer.
- `cursor`: Pagination cursor (optional).
- `limit`: Items per page (default: 20, max: 100).
- `order_by`: Sort field (default: `created_at`).

**Response**: `200 OK`
```json
{
  "data": [
    { "id": "123", "status": "confirmed", "total": 65.00, "created_at": "..." },
    { "id": "124", "status": "pending", "total": 120.00, "created_at": "..." }
  ],
  "pagination": {
    "limit": 20,
    "cursor": "eyJpZCI6IjEyNCJ9",
    "has_more": true
  },
  "links": {
    "self": "/orders?limit=20",
    "next": "/orders?cursor=eyJpZCI6IjEyNCJ9&limit=20"
  }
}
```

---

### GET /orders/{id}
Get detailed order information.

**Response**: `200 OK`
```json
{
  "id": "123",
  "customer_id": "456",
  "status": "confirmed",
  "total": 65.00,
  "created_at": "2024-01-15T10:30:00Z",
  "confirmed_at": "2024-01-15T11:00:00Z",
  "items": [
    { "id": "1", "product_id": "789", "quantity": 2, "price": 25.00, "subtotal": 50.00 },
    { "id": "2", "product_id": "790", "quantity": 1, "price": 15.00, "subtotal": 15.00 }
  ],
  "links": {
    "self": "/orders/123",
    "customer": "/customers/456",
    "cancel": "/orders/123"
  }
}
```

---

## Final Checklist

Before finalizing an API design, verify:

- ✅ All commands are mapped to appropriate HTTP methods.
- ✅ Read models are exposed via GET endpoints with proper filtering/pagination.
- ✅ Resource URLs use nouns and follow RESTful conventions.
- ✅ HTTP status codes are used correctly and consistently.
- ✅ Error responses follow RFC 7807 Problem Details format.
- ✅ Business rules are validated and violations return clear errors.
- ✅ Idempotent operations are marked; non-idempotent operations have safety mechanisms.
- ✅ API is versioned and includes a migration path.
- ✅ Authentication and authorization patterns are specified.
- ✅ Pagination is implemented for all collection endpoints.
- ✅ Documentation includes request/response examples.
- ✅ Complex business operations use action resources when CRUD doesn't fit.
- ✅ Aggregate boundaries are respected in resource design.
- ✅ Domain events are exposed (in responses, streams, or webhooks).
- ✅ All 5 required files have been created in `docs/api/[domain-context]/`.
- ✅ Cross-references between files use correct relative paths.
