# AI Tools Collection

A curated collection of reusable prompts, skills and agents for GitHub Copilot, designed to enhance your development workflow with AI-powered assistance across various software engineering tasks.

## Overview

This repository provides ready-to-use tools that extend GitHub Copilot's capabilities through:

- **Reusable Prompts**: Task-specific prompt templates with predefined modes, models, and tools
- **Skills**: Specialized instruction sets that teach Copilot to perform tasks in a specific, repeatable way
- **Plugins**: Installable packages that extend GitHub Copilot CLI with reusable agents, skills, hooks, and integrations

Whether you're reviewing code, analyzing architecture, planning implementations, or debugging issues, these tools help you work more efficiently with GitHub Copilot.

## Quick Start

### Install GitHub Copilot Extension

Ensure you have the [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) installed in VS Code.

### Install GitHub Copilot CLI

GitHub Copilot CLI brings agentic AI capabilities directly to your terminal. Install it using one of the following methods:

**npm (all platforms)** — requires Node.js 22 or later:

```sh
npm install -g @github/copilot
```

**Homebrew (macOS and Linux):**

```sh
brew install copilot-cli
```

**WinGet (Windows):**

```powershell
winget install GitHub.Copilot
```

**Install script (macOS and Linux):**

```sh
curl -fsSL https://gh.io/copilot-install | bash
```

After installation, start the CLI with `copilot` and run `/login` to authenticate with your GitHub account.

For more details, see the [official installation guide](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli).

### Install Skills

[Agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills) are folders of instructions, scripts, and resources that Copilot can load to improve its performance in specialized tasks.

To install skills from this repository, copy the desired skill folder from `skills/` into one of the supported locations:

- **Project-level** (available in a specific repo): `.github/skills/` or `.claude/skills/` in your repository
- **Personal** (shared across all projects, Copilot CLI only): `~/.copilot/skills/` or `~/.claude/skills/`

For example, to install the `git-commit` skill for personal use:

```sh
cp -r skills/git-commit ~/.copilot/skills/
```

Or to add it to a specific project:

```sh
cp -r skills/git-commit /path/to/your/repo/.github/skills/
```

**Available skills in this repository:**

| Skill | Description |
|-------|-------------|
| `architecture-analyzer` | Analyze project structure and generate architecture docs |
| `django-rest-api-review` | Review Django REST API code for patterns and best practices |
| `git-commit` | Create well-formed git commits following Conventional Commits |
| `pr-description-writer` | Generate and improve GitHub pull request descriptions |
| `react-review-skill` | Review React code for patterns and best practices |
| `skill-creator` | Guide for creating new skills |

For more information, see [About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills).

### Install Plugins (Copilot CLI)

[Plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing) are installable packages that extend GitHub Copilot CLI with reusable agents, skills, hooks, and integrations.

To install a plugin from this repository directly:

```sh
copilot plugin install YAtOff/ai-tools:plugins/review-pr
```

You can also install from a local clone:

```sh
copilot plugin install ./plugins/review-pr
```

**Available plugins in this repository:**

| Plugin | Description |
|--------|-------------|
| `review-pr` | Review pull requests and provide feedback with specialized agents |

To manage installed plugins:

```sh
copilot plugin list                    # View installed plugins
copilot plugin update PLUGIN-NAME      # Update plugin to latest version
copilot plugin uninstall PLUGIN-NAME   # Remove a plugin
```

For more details, see [Finding and installing plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing).

### Install Prompts

- Click the install buttons in the documentation below for one-click installation
- Or download `.prompt.md` files and add them to your prompt collection manually

## Usage

**For Reusable Prompts:**
- Use `/prompt-name` in VS Code Chat after installation
- Run `Chat: Run Prompt` from the Command Palette
- Click the run button while viewing a prompt file in VS Code

## Available Tools

### Reusable Prompts

| Prompt | Description |
|--------|-------------|
| [Architecture Analyzer](prompts/architecture-analyzer.prompt.md) | Analyze project structure and generate comprehensive `docs/architecture.md` with Mermaid diagrams |
| [Review](prompts/review.prompt.md) | Perform comprehensive code reviews focusing on performance, security, quality, testing, documentation, and architecture |

## Project Structure

```
ai-tools/
├── prompts/                    # Reusable prompt templates
│   ├── architecture-analyzer.prompt.md
│   └── review.prompt.md
├── skills/                     # Agent skills
│   ├── architecture-analyzer/
│   ├── django-rest-api-review/
│   ├── git-commit/
│   ├── pr-description-writer/
│   ├── react-review-skill/
│   └── skill-creator/
├── plugins/                    # Copilot CLI plugins
│   └── review-pr/
└── README.prompts.md           # Detailed prompt documentation
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
- [Installing Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
- [About Agent Skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [Finding and Installing Plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing)

## Best Practices

> [!TIP]
> Start with the Architecture Analyzer prompt when working with a new codebase to understand its structure before making changes.

> [!NOTE]
> The Review prompt can be focused on specific areas by providing an optional focus parameter (e.g., "HTTP error handling").

> [!IMPORTANT]
> Always review AI-generated code suggestions for correctness, security, and alignment with your project's standards.
