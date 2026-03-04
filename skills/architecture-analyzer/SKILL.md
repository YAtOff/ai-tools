---
name: architecture-analyzer
description: Analyzes a software project's structure, components, and relationships to produce a detailed architecture document. Use when asked to document, analyze, or visualize a project's architecture. Creates docs/architecture.md with Overview, Main Components, Component Relationships, and Mermaid diagrams.
---

# Architecture Analyzer

Analyze the structure, main components, and relationships in this software project. Create a detailed `docs/architecture.md` file in Markdown with the following sections:

1. **Overview**: A brief summary of the software architecture and its main goals.
2. **Main Components**: A list and description of the primary system components (e.g., user interface, business logic, data access, database, middleware/services, infrastructure), including their responsibilities and interactions.
3. **Component Relationships**: Explain how components interact (data flow, control flow, APIs, communications), referencing architectural patterns if applicable.
4. **Mermaid Diagrams**: For each major section, include a valid Mermaid diagram (flowcharts or component diagrams) that visualizes the relationships and structure. All Mermaid code blocks must begin with ` ```mermaid ` and follow correct syntax for rendering.
5. **File Structure & Data Flow** (optional): If possible, include diagrams depicting file/folder structure and main data flows for context.

Do not include implementation details — keep the documentation focused on architecture.

Finish by writing the generated content directly to `docs/architecture.md`, creating the `docs/` directory if it doesn't exist.
