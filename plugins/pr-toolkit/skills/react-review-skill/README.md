# React Code Review Skill

A comprehensive skill for conducting systematic React.js code reviews based on modern best practices.

## Files

### SKILL.md
Main skill file containing:
- Skill metadata (name, description, license)
- Usage instructions
- Foundational principles (Composition, SoC, SRP, **Domain Modeling**)
- **Three-layer architecture** (Presentation, Domain, Data Access)
- **Domain object patterns** and business logic extraction
- **Strategy pattern** for handling variability
- **Anti-corruption layer** for external service integration
- Component architecture checks
- State management analysis
- Performance optimization checks
- Error handling review
- Review process workflow
- Output format guidelines

### patterns-reference.md
Detailed reference documentation containing:
- **24+ React design patterns** with examples
- **Architectural patterns** (Layered Architecture)
- Core patterns (Composition, Custom Hooks, Control Props, Provider)
- Structural patterns (Container/Presentational, Compound Components, Headless, Atomic Design, Portals, **Strategy Pattern**, **Anti-Corruption Layer**)
- Advanced patterns (Render Props, Props Getters, HOC)
- General principles (DRY, SOLID, SoC, MVVM, Dependency Injection, KISS)
- Modern React 18+ patterns (Server Components, Suspense, Concurrent features)
- Anti-patterns to avoid
- Pattern selection guide
- Complete code examples for every pattern

## How It Works

The skill keeps the main review guidance organized in SKILL.md while providing detailed pattern information in a separate reference file (patterns-reference.md). This allows:

1. **Efficient context usage**: Core review principles in main skill file
2. **On-demand detail**: Reference the patterns guide when needed
3. **Comprehensive coverage**: 24+ patterns with full examples available
4. **Flexible scope**: Works with files, git diffs, PRs, commits, or directories

## Usage Examples

```bash
# Review specific files
"Review src/components/Header.jsx using react-review skill"

# Review git changes
"Review my uncommitted changes"
"Review staged files"

# Review pull requests
"Review PR #123"

# Review commits
"Review commit abc123"
"Compare feature-branch with main"

# Review directories
"Review all files in src/components/"
```

## Review Process

1. User specifies scope (files, diff, PR, commit, etc.)
2. Skill identifies React files in scope
3. Systematic analysis across 4 categories:
   - **Foundational Principles** (including Domain Modeling & Layered Architecture)
   - Component Architecture
   - State Management
   - Performance & Error Handling
4. Generates detailed report with:
   - Executive Summary
   - Detailed Findings (with file locations and code examples)
   - Refactoring Roadmap

## Pattern Coverage

### Architectural Patterns
- Layered Architecture (Presentation, Domain, Data Access)

### Core Patterns
- Component Composition
- Custom Hooks
- Control Props (Controlled/Uncontrolled)
- Provider Pattern (Context API)

### Structural Patterns
- Container/Presentational
- Compound Components
- Headless Components
- Atomic Design
- Portals
- Props Getters
- Strategy Pattern
- Anti-Corruption Layer

### Legacy Patterns
- Higher Order Components (HOC) - migrate to hooks
- Render Props - migrate to hooks

### Principles
- DRY (Don't Repeat Yourself)
- SOLID (SRP, OCP, LSP, ISP, DIP)
- Separation of Concerns
- MVVM
- Dependency Injection
- Stable Dependency Principle
- KISS

### Modern React 18+
- Server Components
- Suspense for Data Fetching
- Concurrent Features (useTransition, useDeferredValue)

## Installation

To use this skill with Claude Code:

### Option 1: Project-Level (Testing)
```bash
mkdir -p .skills
cp -r react-review-skill .skills/
```

### Option 2: Global Plugin
Create a plugin structure and install globally (see Claude Code plugin documentation).

### Option 3: Direct Reference
Point to the skill file when requesting reviews.

## References

This skill is based on:
- Official React documentation and best practices
- "Thinking in React" methodology
- Modern React patterns (React 18+)
- Industry-standard architectural principles
- Martin Fowler's ["Modularizing React Applications with Established UI Patterns"](https://martinfowler.com/articles/modularizing-react-apps.html)
- [21 Fantastic React Design Patterns](https://www.perssondennis.com/articles/21-fantastic-react-design-patterns-and-when-to-use-them) by Dennis Persson

## License

MIT
