# Architectural Documentation Templates

This reference documents the expected structure and content for each file in the architecture documentation suite.

## File 1: 00-Architecture-Overview.md
Provides a high-level architectural view and serves as the entry point.

### Content Requirements
1. **Executive Summary**: One-paragraph overview, identified patterns, technology stacks, and links.
2. **Architectural Overview**: Principles, boundaries, and adaptations.
3. **Architecture Visualization**: C4, UML, Flow, or Component diagrams (or text-based descriptions of relationships).
4. **Architectural Layers and Dependencies**: Layer structure, dependency rules, and injection patterns.
5. **Technology Stack Summary**: Overview of architectural patterns for each detected stack.
6. **Cross-Reference Guide**: Index to 01, 02, 03, and 04 (if applicable).

---

## File 2: 01-Core-Components.md
Documents all major architectural components in detail.

### Content Requirements
1. **Component Overview**: List of components, dependency graph, and layers.
2. **Detailed Component Documentation**: For each component:
   - Purpose and Responsibility
   - Internal Structure (classes/modules, abstractions)
   - Dependencies (inbound and outbound)
   - Interaction Patterns (communication, interfaces, DI, events)
   - Data Model (if applicable)
3. **Data Architecture**: Domain model, entity relationships, access patterns, transformation, caching, and validation.
4. **Cross-Cutting Concerns**: Auth, Error Handling, Logging, Validation, Configuration.
5. **Service Communication**: Boundaries, protocols, sync/async patterns, versioning, discovery.
6. **Testing Architecture**: Strategies, boundaries, doubles/mocking, tools.
7. **Deployment Architecture**: Topology, environment adaptations, containerization, cloud integration.

---

## File 3: 02-Implementation-Patterns.md
Documents concrete implementation patterns and code examples.

### Content Requirements
1. **Pattern Index**: Categorized list of documented patterns.
2. **Core Implementation Patterns**:
   - Interface Design (segregation, abstraction levels)
   - Service Implementation (lifetime, composition, templates)
   - Repository Implementation (queries, transactions, concurrency)
   - Controller/API (request handling, formatting, validation)
   - Domain Model (entities, value objects, domain events)
3. **Technology-Specific Patterns**: (See `technology-patterns.md`)
4. **Code Examples**: Representative examples of layer separation, communication, and extension points.
5. **Anti-Patterns and Pitfalls**: Mistakes to avoid, known issues, performance pitfalls.

---

## File 4: 03-Extension-Guide.md
Practical guidance for extending the architecture and implementing new features.

### Content Requirements
1. **Extension Philosophy**: How the architecture supports growth.
2. **Feature Addition Patterns**:
   - Adding New Features (workflow, placement)
   - Modification Patterns (backward compatibility, deprecation)
   - Integration Patterns (adapters, anti-corruption layers)
3. **Evolution Patterns**: Component extension mechanisms and plugin points.
4. **Development Workflow**: Sequence for creating and integrating components.
5. **Implementation Templates**: Base classes, interfaces, file organization.
6. **Validation Checklist**: Compliance checks and review focus areas.
7. **Common Scenarios**: Step-by-step guides (e.g., adding an API endpoint, entity, or service).

---

## File 5: 04-Decision-Records.md (Optional)
Documents key architectural decisions (ADRs).

### Content Requirements
1. **Decision Log Overview**: How to read and interpret.
2. **Decision Format**:
   - **Decision**: [What was decided]
   - **Context**: [Situation and constraints]
   - **Alternatives Considered**: [Other options]
   - **Rationale**: [Why this choice]
   - **Consequences**: [Impacts]
   - **Trade-offs**: [Gains vs. Sacrifices]
   - **Review Date**: [Future reconsiderations]
3. **Categories**: Style, Technology Selection, Implementation Approach, Data Architecture, Security, Deployment.
