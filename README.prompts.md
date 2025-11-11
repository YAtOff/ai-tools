# 🎯 Reusable Prompts

Ready-to-use prompt templates for specific development scenarios and tasks, defining prompt text with a specific mode, model, and available set of tools.

### How to Use Reusable Prompts

**To Install:**
- Click the VS Code or VS Code Insiders install button for the prompt you want to use
- Download the `*.prompt.md` file and manually add it to your prompt collection

**To Run/Execute:**
- Use `/prompt-name` in VS Code chat after installation
- Run the `Chat: Run Prompt` command from the Command Palette
- Hit the run button while you have a prompt file open in VS Code

---

## Available Prompts

| Prompt | Description |
|--------|-------------|
| **Architecture Analyzer** [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Farchitecture-analyzer.prompt.md)<br />[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode-insiders%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Farchitecture-analyzer.prompt.md) | Analyze the structure, main components, and relationships in this software project. Create a detailed `docs/architecture.md` file in Markdown that explains the project's architecture with Mermaid diagrams. |
| **Architecture Blueprint Generator** [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Farchitecture-blueprint-generator.prompt.md)<br />[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode-insiders%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Farchitecture-blueprint-generator.prompt.md) | Generate comprehensive architectural blueprints across multiple focused documents. Auto-detects technology stacks and patterns, creates visual diagrams, documents implementation patterns, and provides extensible blueprints for maintaining architectural consistency. |
| **Review** [![Install in VS Code](https://img.shields.io/badge/VS_Code-Install-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Freview.prompt.md)<br />[![Install in VS Code Insiders](https://img.shields.io/badge/VS_Code_Insiders-Install-24bfa5?style=flat-square&logo=visualstudiocode&logoColor=white)](https://aka.ms/awesome-copilot/install/prompt?url=vscode-insiders%3Achat-prompt%2Finstall%3Furl%3Dhttps%3A%2F%2Fraw.githubusercontent.com%2FYAtOff%2Fai-tools%2Frefs%2Fheads%2Fmain%2Fprompts%2Freview.prompt.md) | Perform a comprehensive, actionable code review as a senior expert software engineer. Focus on performance, security, code quality, testing, documentation, and architecture. |

---

## Prompt Details

### Architecture Analyzer

**Mode:** `agent`

Analyzes the structure, main components, and relationships in your software project to create comprehensive architectural documentation.

**What it does:**
1. **Overview**: Creates a brief summary of the software architecture and its main goals
2. **Main Components**: Lists and describes primary system components (UI, business logic, data access, database, etc.)
3. **Component Relationships**: Explains how components interact (data flow, control flow, APIs)
4. **Mermaid Diagrams**: Includes visual diagrams for each major section
5. **File Structure & Data Flow**: Optional diagrams depicting file/folder structure and main data flows

**Output:** Generates `docs/architecture.md` file with complete architectural documentation and Mermaid diagrams.

---

### Architecture Blueprint Generator

**Mode:** `agent`

Generates comprehensive architectural blueprints across multiple focused documents to thoroughly analyze and document the codebase architecture.

**Configuration Variables:**
- `PROJECT_TYPE`: Auto-detect, .NET, Java, React, Angular, Python, Node.js, Flutter, or Other
- `ARCHITECTURE_PATTERN`: Auto-detect, Clean Architecture, Microservices, Layered, MVVM, MVC, Hexagonal, Event-Driven, Serverless, Monolithic, or Other
- `DIAGRAM_TYPE`: C4, UML, Flow, Component, or None
- `DETAIL_LEVEL`: High-level, Detailed, Comprehensive, or Implementation-Ready
- `INCLUDES_CODE_EXAMPLES`: Include sample code to illustrate patterns
- `INCLUDES_IMPLEMENTATION_PATTERNS`: Include detailed implementation patterns
- `INCLUDES_DECISION_RECORDS`: Include architectural decision records
- `FOCUS_ON_EXTENSIBILITY`: Emphasize extension points and patterns

**What it does:**
1. **Architecture Detection**: Auto-detects technology stacks and architectural patterns by analyzing project structure, dependencies, and code organization
2. **Multi-Document Generation**: Creates focused documentation files in `architecture-docs/` directory:
   - `00-Architecture-Overview.md`: High-level architecture summary and diagrams
   - `01-Core-Components.md`: Detailed component documentation
   - `02-Implementation-Patterns.md`: Code patterns and examples
   - `03-Extension-Guide.md`: Guide for extending the architecture
   - `04-Decision-Records.md`: Architectural decision records (optional)

3. **Comprehensive Analysis** covers:
   - Architectural layers and dependencies
   - Component structure and relationships
   - Data architecture and models
   - Cross-cutting concerns (auth, logging, validation, etc.)
   - Service communication patterns
   - Testing architecture
   - Deployment architecture
   - Technology-specific patterns for detected frameworks

4. **Visual Diagrams**: Creates C4, UML, Flow, or Component diagrams showing:
   - High-level architectural overview
   - Component interactions and dependencies
   - Data flow through the system

5. **Extension Guidance**: Provides practical guides for:
   - Adding new features while preserving architectural integrity
   - Common development scenarios (new API endpoints, database entities, external services)
   - Implementation templates and validation checklists
   - Development workflows by component type

**Output:** Generates complete architecture documentation suite in `architecture-docs/` with cross-referenced files, visual diagrams, code examples, and extension guides.

---

### Review

**Mode:** `agent`

**Role:** Senior expert software engineer with extensive experience in maintaining projects over a long time and ensuring clean code and best practices.

**Input:** Specify what code to review (e.g., "current PR", "last commit", "changed files", specific file paths, or folder paths)

**What it does:**
1. **Determines Code Source Type**: Automatically detects whether you want to review a PR, commit, changed files, or specific paths
2. **Retrieves Code Content**: Extracts the relevant code for analysis
3. **Performs Comprehensive Review** across six key areas:
   - **Performance**: Unnecessary allocations, sync-over-async, repeated network calls
   - **Security**: Input validation, error handling, sensitive data exposure, HTTP usage
   - **Code Quality**: Readability, maintainability, coding standards, naming conventions, modularity
   - **Testing**: Test coverage, meaningful test cases, mocks/stubs
   - **Documentation**: Comments, README updates, API documentation
   - **Architecture**: Scalability, design patterns, separation of concerns

**Output Format:**
- Summary with high-level assessment
- Critical Issues (must fix) with file locations, issues, proposed fixes, and rationale
- Suggestions (nice to have) with improvements and proposed changes
- Good Practices highlighting positive aspects
- Metadata including review date, files reviewed count, and focus area

**Review saved as:** `docs/YYYY-MM-DDTHH:MM:SS-review.md`

**Additional Feature:** Optional focus area parameter for targeted reviews (e.g., "HTTP error handling")

---

## Contributing

To add a new prompt to this collection:

1. Create a new `.prompt.md` file in the `prompts/` directory
2. Follow the prompt file format with frontmatter:
   ```markdown
   ---
   mode: agent
   description: 'Brief description of what the prompt does'
   ---

   [Your prompt content here]
   ```
3. Update this README.prompts.md file with the new prompt entry in the table and details section

---

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [Awesome GitHub Copilot](https://github.com/github/awesome-copilot)
