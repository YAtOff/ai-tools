---
name: architecture-blueprint-generator
description: Analyzes codebases to generate a comprehensive suite of architectural documentation. Use when the user needs a deep architectural analysis of an existing project, including components, patterns, and extension guides.
---

# Architecture Blueprint Generator

This skill guides you through a comprehensive analysis of a codebase to generate detailed architectural documentation in a new `architecture-docs/` directory.

## Workflow

Follow these steps sequentially to ensure a high-quality blueprint:

### 1. Architecture Detection and Analysis

Before generating any files, perform a comprehensive analysis of the project. If `PROJECT_TYPE` or `ARCHITECTURE_PATTERN` are not specified, auto-detect them by examining:
- Project/configuration files (`package.json`, `pom.xml`, `*.csproj`, etc.).
- Package dependencies and import statements.
- Folder organization and namespacing.
- Dependency flow and component boundaries.
- Interface segregation and abstraction patterns.

### 2. Implementation Phase

Generate the following files in the `architecture-docs/` directory. Refer to [file-templates.md](references/file-templates.md) for detailed content requirements for each file.

1. **`00-Architecture-Overview.md`**: High-level summary, diagrams, and entry point.
2. **`01-Core-Components.md`**: Detailed documentation of components and cross-cutting concerns.
3. **`02-Implementation-Patterns.md`**: Code patterns and examples. Use [technology-patterns.md](references/technology-patterns.md) for stack-specific guidance.
4. **`03-Extension-Guide.md`**: Practical guide for extending the architecture.
5. **`04-Decision-Records.md`**: (Optional) Architectural decision records if requested or relevant.

### 3. Finalization

- **Maintain Consistency**: Ensure terminology is consistent across all files and that they cross-reference each other correctly.
- **Include Metadata**: Add a generation timestamp and version information to each file.
- **Summary**: Provide a brief summary of the generated documentation suite and recommend a reading order.

## Configuration Options

When executing this skill, consider the following parameters (if provided by the user):

- **PROJECT_TYPE**: (e.g., .NET, Java, React, Python, Node.js, Flutter)
- **ARCHITECTURE_PATTERN**: (e.g., Clean Architecture, Microservices, Layered, Hexagonal)
- **DIAGRAM_TYPE**: (e.g., C4, UML, Flow, Component)
- **DETAIL_LEVEL**: (High-level, Detailed, Comprehensive, Implementation-Ready)
- **INCLUDES_CODE_EXAMPLES**: (boolean)
- **INCLUDES_IMPLEMENTATION_PATTERNS**: (boolean)
- **INCLUDES_DECISION_RECORDS**: (boolean)
- **FOCUS_ON_EXTENSIBILITY**: (boolean)

## Resources

- [file-templates.md](references/file-templates.md) - Detailed structure for each document.
- [technology-patterns.md](references/technology-patterns.md) - Common patterns by tech stack.
