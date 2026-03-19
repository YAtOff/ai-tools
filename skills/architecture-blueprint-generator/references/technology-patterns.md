# Technology-Specific Architectural Patterns

This reference documents common architectural patterns for various technology stacks. Use these as guidance when generating `02-Implementation-Patterns.md`.

## .NET
- **Host and Application Model**: Middleware pipeline organization, host builder configuration.
- **Service Integration**: Dependency injection container configuration, framework service usage.
- **Data Access**: Entity Framework Core patterns, Repository pattern implementations.
- **API Implementation**: Controllers, Minimal APIs, request/response models.

## Java
- **Application Container**: Bootstrapping, configuration (Spring Boot, Jakarta EE).
- **Dependency Injection**: Spring Beans, CDI patterns, context management.
- **AOP and Transactions**: Aspect-oriented patterns for cross-cutting concerns, transaction boundaries.
- **ORM**: JPA, Hibernate mapping strategies, Repository patterns.

## React
- **Component Architecture**: Atomic design, container/presentational patterns.
- **State Management**: Redux, Context API, Zustand, or other state-driven patterns.
- **Side Effect Handling**: Hooks (useEffect, useMemo), custom hooks for logic separation.
- **Data Fetching**: React Query, SWR, or standard fetch-based services.

## Angular
- **Module Organization**: Feature modules, core/shared module strategies.
- **Dependency Injection**: Provider scopes, hierarchical injection.
- **Reactive Programming**: RxJS usage, state management (NgRx, Akita).
- **Navigation**: Route guards, lazy loading, resolver patterns.

## Python
- **Module Structure**: Package organization, dependency management (Poetry, pip).
- **Framework Integration**: FastAPI/Flask/Django-specific architectural choices.
- **Programming Paradigms**: Mix of OOP and functional patterns.
- **Asynchronous Programming**: asyncio patterns, concurrency management.

## Node.js
- **Middleware Architecture**: Express/Koa middleware pipelines.
- **Asynchronous Flow**: Promises, async/await patterns, event loops.
- **Service Layer**: Separation of concerns between controllers and data services.
- **Dependency Management**: Module resolution, inversion of control.

## Flutter
- **State Management**: Provider, Riverpod, BLoC, or GetX patterns.
- **Widget Hierarchy**: Strategy for building reusable and maintainable UI.
- **Service Integration**: Platform channels, API client organization.
- **Routing**: Navigator 1.0 vs 2.0 implementations.
