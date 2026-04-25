# Software Design Review Reference

Rubric details delegated from [`SKILL.md`](./SKILL.md). Use this file to evaluate the required seven dimensions and to keep the review evidence-based.

## Review Discipline

- **No generic architecture narration** — every finding should name repo evidence, not a generic best practice.
- **No unsupported certainty** — docs, prompts, and comments are secondary evidence until code, config, or tests confirm them.
- **No style-only feedback** — mention naming or formatting only when it affects boundaries, cohesion, ownership, or discoverability.
- **No checklist drift** — stay inside the seven dimensions from `SKILL.md`; do not invent extra categories or prescribe new patterns without evidence.
- Set scope first; every claim, evidence point, and recommendation must stay focused on that repo/path. Recommendations may reference minimal directly connected boundary context when that seam is the actual source of a scoped issue.
- For path-scoped reviews, use evidence only from the requested path unless you need minimal directly connected boundary context (such as the containing package, direct imports/exports, composition root, direct package/plugin manifest, or boundary tests). Label any such outside-path evidence as boundary context. Do not pull in root READMEs, sibling packages, or unrelated manifests to fill gaps or to create comparison-based findings.
- If direct evidence is missing, say so in the affected section instead of filling the gap with assumptions.
- Thread incomplete evidence through the relevant dimension section instead of hiding it in a closing disclaimer.

## Package and module architecture

### What to look for
- Top-level structure that communicates business domains or stable capabilities
- Dependency direction toward core logic rather than outward toward infrastructure details
- Frameworks, transports, and storage isolated at the edges
- Explicit boundaries between contexts, plugins, or subsystems

### Positive structural signals
- Domain-oriented or capability-oriented top-level folders
- Adapters/plugins separated from core modules
- Clear boundary rules for cross-module imports
- Translators between contexts instead of shared internals

### Structural smells
- Generic dumping grounds such as `utils`, `helpers`, or `services` that span unrelated concerns
- Core modules importing framework, ORM, UI, or transport details
- Shared models leaking across context boundaries
- Stable central modules depending on many volatile packages

### Evidence sources inside a repo
- Top-level directories, folder/package names, and workspace/package layout
- Import edges, dependency rules, and composition roots
- Adapter/plugin folders, framework bootstrap code, and boundary tests

### Rubric details
- **Screaming intent** — the top-level structure should reveal business domains or stable capabilities.
- **Dependency rule** — source-code dependencies should point inward toward the core/domain.
- **Stability-dependency alignment** — stable modules with many dependents should stay dependency-light.
- **Framework isolation** — frameworks should appear as peripheral plugins/adapters, not as the center of the design.
- **Bounded context separation** — different domain areas should stay explicit, with translators rather than shared internals.

## Class and interface structure

### What to look for
- Types or modules with one clear responsibility
- Type names that describe responsibility instead of generic roles
- Extension through interfaces, strategies, or composition instead of type-switch branching
- Honest inheritance and small client-focused interfaces
- High-level orchestration depending on abstractions rather than concrete implementations

### Positive structural signals
- Focused classes/modules with narrow public surfaces
- Type names that match the responsibility exposed by the public API
- Constructor or parameter injection for collaborators
- Ports/interfaces that isolate adapters and infrastructure
- Composition favored over broad inheritance trees

### Structural smells
- God classes, multi-purpose managers, or modules with several reasons to change
- Generic names such as `Manager`, `Processor`, `Data`, or `Info` hiding unclear ownership
- Large switch/if chains that branch on type or mode instead of delegating behavior
- Fat interfaces that force unrelated clients to depend on unused methods
- High-level services instantiating concrete collaborators directly

### Evidence sources inside a repo
- Class/type names, definitions, constructors, factories, and public methods
- Interface or protocol files, adapters, and dependency wiring
- Switch statements, inheritance hierarchies, and tests around substitution/extension points

### Rubric details
- **SRP adherence** — each class/module should have one well-defined reason to change.
- **OCP mechanisms** — new behavior should arrive through new implementations more often than edits to central branching logic.
- **LSP integrity** — inheritance should preserve invariants and substitutability.
- **ISP granularity** — interfaces should stay small and client-focused.
- **DIP implementation** — high-level orchestration should depend on abstractions or ports.

If the repo has little direct class/interface structure, say so and review module contracts instead of inventing OO abstractions.

## Domain integrity and tactical DDD

### What to look for
- Clear consistency boundaries around aggregates or equivalent domain clusters
- Domain terms that match the business vocabulary used across modules and tests
- Value objects or equivalent models for identity-less concepts
- Repository boundaries aligned to domain objects rather than storage tables

### Positive structural signals
- Aggregate roots or domain entry points guarding state changes
- Consistent domain names across folders, types, and tests
- Immutable value objects for concepts like money, dates, or status bundles
- Repository interfaces located near domain/application logic

### Structural smells
- Multiple services mutating the same domain cluster without a clear owner
- Generic CRUD or table-shaped naming that hides domain meaning
- Primitive obsession for important domain concepts
- Persistence types leaking into domain APIs

### Evidence sources inside a repo
- Domain/application folders, entities, value objects, repositories, and transaction scripts
- Tests that describe invariants, state transitions, or domain language
- Migrations, schemas, and ADRs only when they are backed by code boundaries

### Rubric details
- **Aggregate boundaries** — aggregate roots should be clear entry points for modifying a cluster of objects.
- **Transactional consistency** — transactional boundaries should align with aggregates; cross-aggregate work should use coordination, not shared writes.
- **Value object usage** — identity-less concepts should be modeled explicitly and preferably immutably.
- **Ubiquitous language** — names in code should match the problem-space vocabulary.
- **Repository focus** — repositories should map to aggregate roots, with interfaces in domain/application layers.

If the repo does not expose direct aggregate or repository evidence, report that the evidence is incomplete. Do not require DDD labels, event sourcing, or bounded-context diagrams just because the topic mentions domain design.

## Paradigm synergy and flow of control

### What to look for
- Pure business logic separated from I/O and side effects where the codebase supports that split
- Thin orchestration layers that coordinate rather than own business rules
- Explicit dependency injection and visible control flow
- Encapsulated mutable state in OO sections and simple data flow in functional sections

### Positive structural signals
- Pure functions or domain methods that run without network/database access
- Thin handlers/controllers/services that call into deeper logic
- Dependencies injected through constructors, parameters, or composition roots
- State changes routed through explicit behaviors rather than global mutation

### Structural smells
- Business rules mixed directly with HTTP, database, UI, or CLI side effects
- Thick controllers/handlers/services that own both orchestration and domain decisions
- Hidden singletons, hard-coded instantiation, or global state
- Unclear handoffs between data transformation code and stateful object behavior

### Evidence sources inside a repo
- Controllers, handlers, services, jobs, commands, and composition roots
- Pure utility/domain modules, adapters, and stateful models
- Constructor parameters, factories, and calls to external systems

### Rubric details
- **Functional core purity** — core logic should be executable without side effects where practical.
- **Imperative shell thickness** — shells should stay thin and focused on orchestration/I/O.
- **Inversion of control** — dependencies should be injected from the outside rather than hard-coded.
- **Separation of data and behavior** — functional areas should keep transformations explicit and simple.
- **State encapsulation** — OO areas should protect mutable state behind clear behaviors.

Do not invent orchestration DAGs, event flows, or layered patterns that the repo does not actually show.

## Testability and maintainability

### What to look for
- Behavior-focused tests around public interfaces
- Structural isolation that reduces heavy mocking and speeds feedback
- Module surfaces and names that are understandable without reading every implementation detail
- Low change surface for simple features or extensions

### Positive structural signals
- Core logic tested with state-based assertions and little infrastructure setup
- Tests targeting public APIs, domain behaviors, or module contracts
- Small module surfaces and obvious extension seams
- Simple features added by extending one area instead of editing many unrelated files

### Structural smells
- Widespread interaction-mock tests for ordinary business logic
- Tests coupled to private methods or call order
- Slow, entangled test setup that mirrors production wiring
- Simple changes requiring scattered edits across many modules

### Evidence sources inside a repo
- Test directories, fixtures, mocks, public APIs, and test configuration
- Module boundaries, dependency wiring, and recent change surfaces when available
- Naming and folder layout that affect navigability

### Rubric details
- **Mocking frequency** — most behavior should be testable without heavy interaction mocking.
- **Feedback velocity** — structural isolation should support fast local feedback.
- **Redundant coverage** — tests should focus on public APIs and behaviors rather than private implementation details.
- **Cognitive load** — a module's role should be understandable from its interfaces, naming, and placement.
- **Extensibility cost** — small features should not require many scattered structural edits.

Avoid generic “add more tests” feedback. Tie every testability concern to a structural cause.

## Security boundaries and observability

### What to look for
- Explicit trust boundaries between components, processes, or external integrations
- Secure data flows that show where untrusted input is validated, authenticated, and authorized
- Centralized, structured logging instead of ad-hoc print/console statements scattered across modules
- Correlation IDs (request, trace, or span identifiers) propagated across module or service boundaries
- Structural seams that make the system auditable and debuggable at scale

### Positive structural signals
- Dedicated modules/middleware for authn, authz, input validation, or sanitization at edges
- Clear separation between trusted core logic and untrusted transport/IO layers
- A shared logging module/abstraction with structured fields, log levels, and consistent context
- Request/trace IDs created at entry points and threaded through handlers, services, and outbound calls
- Configuration that centralizes secrets, transport security, or audit hooks instead of scattering them

### Structural smells
- Validation, authn, or authz logic duplicated inline across handlers with no shared seam
- Trust boundaries that are implicit — domain code reading raw request bodies, env vars, or filesystem paths directly
- No shared logging surface — modules instantiate their own loggers or write to stdout directly, with no central seam owning fields, levels, or context
- No correlation/trace ID concept across services, jobs, or async handoffs
- Sensitive data flowing into logs or crossing boundaries without an explicit redaction or transport seam

### Evidence sources inside a repo
- Middleware, interceptors, guards, edge handlers, and transport/adapter folders
- Logging modules, logger configuration, structured-log helpers, and tracing setup
- Auth modules, policy/permission code, secret/config loaders, and validation schemas
- Cross-service or cross-process call sites where IDs would be propagated (HTTP clients, message publishers, job runners)

### Rubric details
- **Trust boundary clarity** — the design should make it obvious where untrusted input becomes trusted and where authorization is enforced.
- **Secure data flow** — sensitive data paths (auth tokens, PII, secrets) should travel through explicit, narrow seams rather than diffuse through the codebase.
- **Logging centralization** — logging should go through a shared, structured surface with consistent fields and levels.
- **Correlation propagation** — request/trace identifiers should be created at entry points and passed through internal calls, async work, and outbound integrations.
- **Auditability** — the structure should support reconstructing what happened and who triggered it without bolt-on instrumentation.

If the repo does not expose direct evidence of trust boundaries, logging seams, or correlation IDs, report that the evidence is incomplete instead of prescribing a specific framework or vendor.

## Resilience and fault tolerance

### What to look for
- Architectural patterns that contain failure: circuit breakers, bulkheads, timeouts, and backpressure
- Retry mechanisms for transient failures, with explicit policies (limits, backoff, idempotency)
- Redundancy and automatic failover at the structural level rather than ad-hoc try/except wrapping
- Identification and removal of single points of failure in the composition (shared singletons, single-instance dependencies, unguarded external calls)
- Graceful degradation paths when a dependency is unavailable

### Positive structural signals
- Dedicated client/adapter modules that own timeouts, retries, and circuit-breaker state per dependency
- Idempotency keys, deduplication, or compensation seams around retried operations
- Composition that allows multiple instances/replicas, health checks, or failover targets
- Explicit fallback or degraded-mode code paths for non-critical dependencies
- Bulkhead-style isolation between subsystems so one failing area cannot exhaust shared resources

### Structural smells
- Direct, unguarded calls to external services, databases, or queues from domain or handler code
- Retry logic open-coded in many call sites with inconsistent limits, backoff, or no idempotency consideration
- Single global instance for a stateful dependency with no failover or replacement seam
- No timeouts on outbound calls, or timeouts buried inline with business logic
- Cascading failure paths — one failing dependency takes down unrelated features because they share a thread, connection pool, or process

### Evidence sources inside a repo
- HTTP/gRPC/database/queue client wrappers, adapter folders, and resilience helper modules
- Configuration for timeouts, retries, circuit breakers, connection pools, and health checks
- Deployment/composition artifacts (compose files, manifests, infra modules) only when they are backed by code structure
- Tests around failure modes, timeouts, retries, or fallback behavior

### Rubric details
- **Failure containment** — failures in one dependency or subsystem should be structurally prevented from cascading into unrelated areas.
- **Retry discipline** — retries should be centralized, bounded, and paired with idempotency or deduplication where it matters.
- **Timeouts and backpressure** — outbound calls and queues should have explicit limits enforced at a shared seam.
- **Redundancy and failover** — the composition should support multiple instances or alternate paths instead of relying on a single critical node.
- **Single-point-of-failure visibility** — the design should make critical, non-redundant dependencies obvious so they can be hardened.

If the repo does not show direct resilience seams, report that the evidence is incomplete instead of prescribing a specific library or platform feature.
