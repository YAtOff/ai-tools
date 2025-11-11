---
description: 'Comprehensive project architecture blueprint generator that analyzes codebases to create detailed architectural documentation. Automatically detects technology stacks and architectural patterns, generates visual diagrams, documents implementation patterns, and provides extensible blueprints for maintaining architectural consistency and guiding new development.'
mode: 'agent'
---

# Comprehensive Project Architecture Blueprint Generator

## Configuration Variables
${PROJECT_TYPE="Auto-detect|.NET|Java|React|Angular|Python|Node.js|Flutter|Other"} <!-- Primary technology -->
${ARCHITECTURE_PATTERN="Auto-detect|Clean Architecture|Microservices|Layered|MVVM|MVC|Hexagonal|Event-Driven|Serverless|Monolithic|Other"} <!-- Primary architectural pattern -->
${DIAGRAM_TYPE="C4|UML|Flow|Component|None"} <!-- Architecture diagram type -->
${DETAIL_LEVEL="High-level|Detailed|Comprehensive|Implementation-Ready"} <!-- Level of detail to include -->
${INCLUDES_CODE_EXAMPLES=true|false} <!-- Include sample code to illustrate patterns -->
${INCLUDES_IMPLEMENTATION_PATTERNS=true|false} <!-- Include detailed implementation patterns -->
${INCLUDES_DECISION_RECORDS=true|false} <!-- Include architectural decision records -->
${FOCUS_ON_EXTENSIBILITY=true|false} <!-- Emphasize extension points and patterns -->

## Generated Prompt

"Create a comprehensive architecture blueprint split across multiple focused documents to thoroughly analyze the architectural patterns in the codebase. Generate the following files in an 'architecture-docs/' directory:

**File Structure:**
- `00-Architecture-Overview.md` - High-level architecture summary and diagrams
- `01-Core-Components.md` - Detailed component documentation
- `02-Implementation-Patterns.md` - Code patterns and examples
- `03-Extension-Guide.md` - Guide for extending the architecture
- `04-Decision-Records.md` - Architectural decision records (if ${INCLUDES_DECISION_RECORDS})

### Phase 1: Architecture Detection and Analysis

Before generating any files, perform a comprehensive analysis:

- ${PROJECT_TYPE == "Auto-detect" ? "Analyze the project structure to identify all technology stacks and frameworks in use by examining:
  - Project and configuration files
  - Package dependencies and import statements
  - Framework-specific patterns and conventions
  - Build and deployment configurations" : "Focus on ${PROJECT_TYPE} specific patterns and practices"}

- ${ARCHITECTURE_PATTERN == "Auto-detect" ? "Determine the architectural pattern(s) by analyzing:
  - Folder organization and namespacing
  - Dependency flow and component boundaries
  - Interface segregation and abstraction patterns
  - Communication mechanisms between components" : "Document how the ${ARCHITECTURE_PATTERN} architecture is implemented"}

---

## File 1: 00-Architecture-Overview.md

This file provides the high-level architectural view and serves as the entry point for understanding the system.

**Include:**

#### 1. Executive Summary
- One-paragraph overview of the architectural approach
- Key architectural patterns identified
- Primary technology stacks detected
- Links to other architecture documentation files

#### 2. Architectural Overview
- Clear, concise explanation of the overall architectural approach
- Guiding principles evident in the architectural choices
- Architectural boundaries and how they're enforced
- Hybrid architectural patterns or adaptations of standard patterns

#### 3. Architecture Visualization
${DIAGRAM_TYPE != "None" ? `Create ${DIAGRAM_TYPE} diagrams at multiple levels:
- High-level architectural overview showing major subsystems
- Component interaction diagrams showing relationships and dependencies
- Data flow diagrams showing how information moves through the system
- Ensure diagrams accurately reflect actual implementation` : "Describe component relationships based on actual code dependencies:
- Subsystem organization and boundaries
- Dependency directions and component interactions
- Data flow and process sequences"}

#### 4. Architectural Layers and Dependencies
- Layer structure as implemented in the codebase
- Dependency rules between layers
- Abstraction mechanisms that enable layer separation
- Circular dependencies or layer violations (if any)
- Dependency injection patterns used to maintain separation

#### 5. Technology Stack Summary
${PROJECT_TYPE == "Auto-detect" ? "For each detected technology stack, provide brief overview of architectural patterns used" : `Brief overview of ${PROJECT_TYPE}-specific architectural patterns`}

#### 6. Cross-Reference Guide
- Quick index to components (detailed in 01-Core-Components.md)
- Quick index to patterns (detailed in 02-Implementation-Patterns.md)
- Quick index to extension points (detailed in 03-Extension-Guide.md)
${INCLUDES_DECISION_RECORDS ? "- Quick index to key decisions (detailed in 04-Decision-Records.md)" : ""}

---

## File 2: 01-Core-Components.md

This file documents all major architectural components in detail.

**Include:**

#### 1. Component Overview
- List of all major components with one-line descriptions
- Component dependency graph
- Component categorization by architectural layer

#### 2. Detailed Component Documentation
For each architectural component discovered in the codebase:

**[Component Name]**

- **Purpose and Responsibility**:
  - Primary function within the architecture
  - Business domains or technical concerns addressed
  - Boundaries and scope limitations

- **Internal Structure**:
  - Organization of classes/modules within the component
  - Key abstractions and their implementations
  - Design patterns utilized

- **Dependencies**:
  - Components this depends on
  - Components that depend on this
  - External dependencies

- **Interaction Patterns**:
  - How the component communicates with others
  - Interfaces exposed and consumed
  - Dependency injection patterns
  - Event publishing/subscription mechanisms

- **Data Model** (if applicable):
  - Key entities managed
  - Data access patterns
  - Caching strategies

#### 3. Data Architecture
- Domain model structure and organization
- Entity relationships and aggregation patterns
- Data access patterns (repositories, data mappers, etc.)
- Data transformation and mapping approaches
- Caching strategies and implementations
- Data validation patterns

#### 4. Cross-Cutting Concerns Implementation

- **Authentication & Authorization**:
  - Security model implementation
  - Permission enforcement patterns
  - Identity management approach

- **Error Handling & Resilience**:
  - Exception handling patterns
  - Retry and circuit breaker implementations
  - Fallback and graceful degradation strategies

- **Logging & Monitoring**:
  - Instrumentation patterns
  - Observability implementation
  - Performance monitoring approach

- **Validation**:
  - Input validation strategies
  - Business rule validation implementation
  - Validation responsibility distribution

- **Configuration Management**:
  - Configuration source patterns
  - Environment-specific configuration strategies
  - Secret management approach

#### 5. Service Communication Patterns
- Service boundary definitions
- Communication protocols and formats
- Synchronous vs. asynchronous communication patterns
- API versioning strategies
- Service discovery mechanisms
- Resilience patterns in service communication

#### 6. Testing Architecture
- Testing strategies aligned with the architecture
- Test boundary patterns (unit, integration, system)
- Test doubles and mocking approaches
- Test data strategies
- Testing tools and frameworks integration

#### 7. Deployment Architecture
- Deployment topology derived from configuration
- Environment-specific architectural adaptations
- Runtime dependency resolution patterns
- Configuration management across environments
- Containerization and orchestration approaches
- Cloud service integration patterns

---

## File 3: 02-Implementation-Patterns.md

This file documents concrete implementation patterns and ${INCLUDES_CODE_EXAMPLES ? "includes code examples" : "pattern descriptions"}.

**Include:**

#### 1. Pattern Index
- List of all patterns documented in this file
- Pattern categorization (by layer, by concern, etc.)

${INCLUDES_IMPLEMENTATION_PATTERNS ?
"#### 2. Core Implementation Patterns

**Interface Design Patterns**:
- Interface segregation approaches
- Abstraction level decisions
- Generic vs. specific interface patterns
- Default implementation patterns

**Service Implementation Patterns**:
- Service lifetime management
- Service composition patterns
- Operation implementation templates
- Error handling within services

**Repository Implementation Patterns**:
- Query pattern implementations
- Transaction management
- Concurrency handling
- Bulk operation patterns

**Controller/API Implementation Patterns**:
- Request handling patterns
- Response formatting approaches
- Parameter validation
- API versioning implementation

**Domain Model Implementation**:
- Entity implementation patterns
- Value object patterns
- Domain event implementation
- Business rule enforcement" : "#### 2. Implementation Pattern Summary
Brief description that detailed implementation patterns vary across the codebase with references to key examples."}

#### 3. Technology-Specific Patterns
${PROJECT_TYPE == "Auto-detect" ? "For each detected technology stack, document specific architectural patterns:" : `Document ${PROJECT_TYPE}-specific architectural patterns:`}

${(PROJECT_TYPE == ".NET" || PROJECT_TYPE == "Auto-detect") ?
"**-.NET Patterns** (if detected):
- Host and application model implementation
- Middleware pipeline organization
- Framework service integration patterns
- ORM and data access approaches
- API implementation patterns (controllers, minimal APIs, etc.)
- Dependency injection container configuration" : ""}

${(PROJECT_TYPE == "Java" || PROJECT_TYPE == "Auto-detect") ?
"**Java Patterns** (if detected):
- Application container and bootstrap process
- Dependency injection framework usage (Spring, CDI, etc.)
- AOP implementation patterns
- Transaction boundary management
- ORM configuration and usage patterns
- Service implementation patterns" : ""}

${(PROJECT_TYPE == "React" || PROJECT_TYPE == "Auto-detect") ?
"**React Patterns** (if detected):
- Component composition and reuse strategies
- State management architecture
- Side effect handling patterns
- Routing and navigation approach
- Data fetching and caching patterns
- Rendering optimization strategies" : ""}

${(PROJECT_TYPE == "Angular" || PROJECT_TYPE == "Auto-detect") ?
"**Angular Patterns** (if detected):
- Module organization strategy
- Component hierarchy design
- Service and dependency injection patterns
- State management approach
- Reactive programming patterns
- Route guard implementation" : ""}

${(PROJECT_TYPE == "Python" || PROJECT_TYPE == "Auto-detect") ?
"**Python Patterns** (if detected):
- Module organization approach
- Dependency management strategy
- OOP vs. functional implementation patterns
- Framework integration patterns
- Asynchronous programming approach" : ""}

${INCLUDES_CODE_EXAMPLES ?
"#### 4. Code Examples

Extract representative code examples that illustrate key architectural patterns:

**Layer Separation Examples**:
- Interface definition and implementation separation
- Cross-layer communication patterns
- Dependency injection examples

**Component Communication Examples**:
- Service invocation patterns
- Event publication and handling
- Message passing implementation

**Extension Point Examples**:
- Plugin registration and discovery
- Extension interface implementations
- Configuration-driven extension patterns

Include enough context with each example to show the pattern clearly, but keep examples concise and focused on architectural concepts." : ""}

#### ${INCLUDES_CODE_EXAMPLES ? "5" : "4"}. Anti-Patterns and Pitfalls
- Common architectural mistakes to avoid
- Known anti-patterns found or avoided in the codebase
- Performance pitfalls and their solutions
- Testing blind spots

---

## File 4: 03-Extension-Guide.md

This file provides practical guidance for extending the architecture and implementing new features.

**Include:**

#### 1. Extension Philosophy
- Overview of how the architecture supports extension
- Key extensibility principles applied
- Balance between flexibility and simplicity

${FOCUS_ON_EXTENSIBILITY ?
"#### 2. Feature Addition Patterns

**Adding New Features**:
- How to add new features while preserving architectural integrity
- Where to place new components by type
- Dependency introduction guidelines
- Configuration extension patterns
- Step-by-step workflow for common feature types

**Modification Patterns**:
- How to safely modify existing components
- Strategies for maintaining backward compatibility
- Deprecation patterns
- Migration approaches

**Integration Patterns**:
- How to integrate new external systems
- Adapter implementation patterns
- Anti-corruption layer patterns
- Service facade implementation" : "#### 2. Extension Points
Document key extension points in the architecture with brief guidance on how to use them."}

#### 3. Evolution Patterns
For key component types, document:
- How the component can be extended
- Variation points and plugin mechanisms
- Configuration and customization approaches

#### 4. Development Workflow
- Starting points for different feature types
- Component creation sequence
- Integration steps with existing architecture
- Testing approach by architectural layer

#### 5. Implementation Templates
- Base class/interface templates for key architectural components
- Standard file organization for new components
- Dependency declaration patterns
- Documentation requirements
- Naming conventions and organizational standards

#### 6. Validation Checklist
- Architecture compliance checklist for new code
- Code review focus areas for architectural integrity
- Automated checks for architectural compliance (if any)
- Documentation requirements for new components

#### 7. Common Scenarios
Provide step-by-step guides for common development scenarios:
- Adding a new API endpoint
- Adding a new database entity
- Integrating a new external service
- Adding a new background job/task
- Implementing a new feature flag

---

${INCLUDES_DECISION_RECORDS ?
`## File 5: 04-Decision-Records.md

This file documents key architectural decisions evident in the codebase.

**Include:**

#### 1. Decision Log Overview
- How to read and interpret these decision records
- How decisions are categorized
- When these decisions were documented

#### 2. Architectural Style Decisions

For each major architectural style decision:

**Decision**: [What was decided]
**Context**: [Situation and constraints]
**Alternatives Considered**: [What other options were evaluated]
**Rationale**: [Why this option was chosen]
**Consequences**: [Positive and negative impacts]
**Trade-offs**: [What was gained vs. what was sacrificed]
**Review Date**: [When this should be reconsidered]

Include decisions about:
- Overall architectural pattern choice
- Layer organization approach
- Component boundary definitions

#### 3. Technology Selection Decisions

Document key technology choices:
- Framework selections and their architectural impact
- Library and tool selections
- Custom vs. off-the-shelf component decisions
- Language and platform choices

#### 4. Implementation Approach Decisions

Document significant implementation pattern decisions:
- Specific implementation patterns chosen
- Standard pattern adaptations
- Performance vs. maintainability trade-offs
- Error handling approaches
- Testing strategies

#### 5. Data Architecture Decisions
- Database technology choices
- Data modeling approaches
- Caching strategies
- Data access pattern decisions

#### 6. Security and Compliance Decisions
- Authentication/authorization approach
- Data protection strategies
- Compliance requirement impacts

#### 7. Deployment and Operations Decisions
- Deployment model choices
- Scaling strategies
- Monitoring and observability approaches

---` : ""}

## Final Instructions

1. **Generate files in order**: Start with 00-Architecture-Overview.md and proceed sequentially
2. **Maintain consistency**: Use consistent terminology and cross-reference between files
3. **Include metadata**: Add generation date and version information to each file
4. **Keep focused**: Each file should be self-contained but reference others where appropriate
5. **Update index**: Ensure 00-Architecture-Overview.md has accurate links to all other files

**At the end of each file**, include:
- Document generation timestamp
- Recommended update frequency
- Links to related architecture documents

**After generating all files**, create a brief summary confirming:
- All files generated successfully
- Total size of documentation suite
- Recommended reading order for different audiences (new developers, architects, etc.)
"
