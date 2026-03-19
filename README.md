# AI Tools Collection

A curated collection of skills and agents for GitHub Copilot, designed to enhance your development workflow with AI-powered assistance across various software engineering tasks.

## Overview

This repository provides ready-to-use tools that extend GitHub Copilot's capabilities through:

- **Skills**: Specialized instruction sets that teach Copilot to perform tasks in a specific, repeatable way.
- **Plugins**: Installable packages that extend GitHub Copilot CLI with reusable agents, skills, hooks, and integrations.

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

For example, to install the `architecture-analyzer` skill for personal use:

```sh
cp -r skills/architecture-analyzer ~/.copilot/skills/
```

Or to add it to a specific project:

```sh
cp -r skills/architecture-analyzer /path/to/your/repo/.github/skills/
```

**Available skills in this repository:**

| Skill | Description |
|-------|-------------|
| `architecture-analyzer` | Analyze project structure and generate architecture docs |
| `architecture-blueprint-generator` | Generate a comprehensive suite of architectural documentation |

For more information, see [About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills).

### Install Plugins (Copilot CLI)

[Plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing) are installable packages that extend GitHub Copilot CLI with reusable agents, skills, hooks, and integrations.

To install the PR Toolkit plugin from this repository directly:

```sh
copilot plugin install YAtOff/ai-tools:plugins/pr-toolkit
```

You can also install from a local clone:

```sh
copilot plugin install ./plugins/pr-toolkit
```

**Available plugins in this repository:**

| Plugin | Description |
|--------|-------------|
| `pr-toolkit` | A comprehensive toolkit for creating and reviewing pull requests with specialized agents |

**Agents included in `pr-toolkit`:**

- **Code Reviewer**: Performs high-level code analysis and logic verification.
- **Code Simplifier**: Focuses on reducing complexity and improving readability.
- **Comment Analyzer**: Reviews code comments for accuracy and clarity.
- **PR Test Analyzer**: Evaluates test coverage and quality in pull requests.
- **Silent Failure Hunter**: Identifies potential unhandled errors and silent failures.
- **Type Design Analyzer**: Analyzes type definitions and data structures.

To manage installed plugins:

```sh
copilot plugin list                    # View installed plugins
copilot plugin update PLUGIN-NAME      # Update plugin to latest version
copilot plugin uninstall PLUGIN-NAME   # Remove a plugin
```

For more details, see [Finding and installing plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing).

## Project Structure

```
ai-tools/
├── plugins/                    # Copilot CLI plugins
│   └── pr-toolkit/             # PR creation and review toolkit
│       ├── agents/             # Specialized agents for PR analysis
│       └── skills/             # Skills included with the toolkit
└── skills/                     # Standalone agent skills
    ├── architecture-analyzer/  # Analyze project architecture
    └── architecture-blueprint-generator/ # Generate deep architectural blueprints
```

## Contributing

Contributions are welcome! To add a new skill or agent:

1. Create a new directory in `skills/` or within the `pr-toolkit` plugin
2. Provide a clear `SKILL.md` or `.agent.md` file
3. Update relevant README files with your tool details
4. Submit a pull request with a clear description

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [VS Code Copilot Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [Installing Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)
- [About Agent Skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)
- [Finding and Installing Plugins](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-finding-installing)

## Best Practices

> [!TIP]
> Use the `architecture-blueprint-generator` when you need a deep, multi-document analysis of an existing project's architecture.

> [!NOTE]
> The `pr-toolkit` agents can be used together or individually to focus on specific aspects of a code review.

> [!IMPORTANT]
> Always review AI-generated code suggestions for correctness, security, and alignment with your project's standards.
