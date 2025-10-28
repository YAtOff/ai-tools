# AI Tools Collection

A curated collection of reusable prompts and custom chat modes for GitHub Copilot, designed to enhance your development workflow with AI-powered assistance across various software engineering tasks.

## Overview

This repository provides ready-to-use tools that extend GitHub Copilot's capabilities through:

- **Reusable Prompts**: Task-specific prompt templates with predefined modes, models, and tools
- **Custom Chat Modes**: Specialized behaviors for context-aware assistance in particular workflows

Whether you're reviewing code, analyzing architecture, planning implementations, or debugging issues, these tools help you work more efficiently with GitHub Copilot.

## Quick Start

### Installation

1. **Install GitHub Copilot**
   - Ensure you have the [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) installed in VS Code

2. **Install Prompts/Chat Modes**
   - Click the install buttons in the documentation below for one-click installation
   - Or download `.prompt.md` or `.chatmode.md` files and add them to your prompt collection manually

### Usage

**For Reusable Prompts:**
- Use `/prompt-name` in VS Code Chat after installation
- Run `Chat: Run Prompt` from the Command Palette
- Click the run button while viewing a prompt file in VS Code

**For Custom Chat Modes:**
- Select the desired chat mode from the VS Code Chat interface
- Activate through the Command Palette or chat mode selector

## Available Tools

### Reusable Prompts

| Prompt | Description |
|--------|-------------|
| [Architecture Analyzer](prompts/architecture-analyzer.prompt.md) | Analyze project structure and generate comprehensive `docs/architecture.md` with Mermaid diagrams |
| [Review](prompts/review.prompt.md) | Perform comprehensive code reviews focusing on performance, security, quality, testing, documentation, and architecture |

For a complete list of available prompts from the broader ecosystem, see [README.awesome-prompts.md](README.awesome-prompts.md).

### Custom Chat Modes

For a comprehensive list of available chat modes, including specialized modes for API architecture, debugging, TDD workflows, and more, see [README.awesome-chatmodes.md](README.awesome-chatmodes.md).

## Project Structure

```
ai-tools/
├── prompts/                    # Reusable prompt templates
│   ├── architecture-analyzer.prompt.md
│   └── review.prompt.md
├── README.awesome-chatmodes.md # Comprehensive chat modes catalog
├── README.awesome-prompts.md   # Comprehensive prompts catalog
└── README.prompts.md          # Detailed prompt documentation
```

## Prompt Details

### Architecture Analyzer

**Mode:** `agent`

Analyzes your software project to create detailed architectural documentation with visual diagrams.

**Features:**
- Overview of architecture and goals
- Component descriptions and responsibilities
- Relationship mapping (data flow, control flow, APIs)
- Mermaid diagrams for visualization
- File structure and data flow analysis

**Output:** `docs/architecture.md`

### Review

**Mode:** `agent`

Comprehensive code review as a senior software engineer, providing actionable feedback with specific improvement suggestions.

**Input Types:**
- `"current PR"` or `"active PR"`
- `"last commit"` or `"latest commit"`
- `"changed files"` or `"uncommitted changes"`
- Specific file paths or folders

**Review Areas:**
- **Performance**: Allocations, async patterns, network efficiency
- **Security**: Input validation, error handling, data exposure
- **Code Quality**: Readability, maintainability, standards adherence
- **Testing**: Coverage, test quality, mocking strategies
- **Documentation**: Comments, README updates, API docs
- **Architecture**: Scalability, design patterns, separation of concerns

**Output Format:**
- Summary with assessment
- Critical issues (must fix)
- Suggestions (nice to have)
- Good practices identified
- Review metadata

**Output File:** `docs/YYYY-MM-DDTHH:MM:SS-review.md`

## Contributing

Contributions are welcome! To add a new prompt:

1. Create a `.prompt.md` file in the `prompts/` directory following the established format
2. Include proper frontmatter with mode and description
3. Update relevant README files with your prompt details
4. Submit a pull request with a clear description

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [GitHub Awesome Copilot](https://github.com/github/awesome-copilot)

## Best Practices

> [!TIP]
> Start with the Architecture Analyzer prompt when working with a new codebase to understand its structure before making changes.

> [!NOTE]
> The Review prompt can be focused on specific areas by providing an optional focus parameter (e.g., "HTTP error handling").

> [!IMPORTANT]
> Always review AI-generated code suggestions for correctness, security, and alignment with your project's standards.
