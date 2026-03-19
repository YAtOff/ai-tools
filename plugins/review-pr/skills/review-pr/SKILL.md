---
name: review-pr
description: Comprehensive PR review using specialized agents
---

# PR Review

You are an expert code reviewer. Run comprehensive pull request reviews using multiple specialized agents, each focusing on different aspects of code quality.

**Announce at start:** "I'm using the review-pr skill to review this PR."

## Core Principles

- **Sequential reviews**: Run agents one at a time for clarity
- **Focus on changes**: Review git diff, not entire codebase
- **Actionable feedback**: Provide file:line references for all issues
- **Prioritize**: Critical > Important > Suggestions

## Review Workflow

### 1. Determine Scope

```bash
git status                    # Changed files
git diff --name-only          # Modified files list
gh pr view 2>/dev/null || true  # Check if PR exists
```

Parse user arguments:
- No args or `all`: Run all applicable reviews
- Specific args: Run only requested aspects

### 2. Available Review Aspects

| Aspect | Agent | When to Use |
|--------|-------|-------------|
| `comments` | comment-analyzer | Comments/docs added |
| `tests` | pr-test-analyzer | Test files changed |
| `errors` | silent-failure-hunter | Error handling changed |
| `types` | type-design-analyzer | Types added/modified |
| `code` | code-reviewer | Always applicable |
| `simplify` | code-simplifier | After passing review |
| `react` | `react-review` (via subagent) | React files (`.jsx`, `.tsx`) changed |
| `django` | `django-rest-api-review` (via subagent) | Django Python files changed |
| `all` | *all applicable* | Default |

### 3. Determine Applicable Reviews

Based on changes:
- **Always**: `code-reviewer` (general quality)
- **Test files changed**: `pr-test-analyzer`
- **Comments/docs added**: `comment-analyzer`
- **Error handling changed**: `silent-failure-hunter`
- **Types added/modified**: `type-design-analyzer`
- **After passing review**: `code-simplifier` (polish)
- **React files changed**: If `.jsx`, `.tsx`, or React components are modified, check if the `react-review` skill is available. If yes, add to applicable reviews.
- **Django files changed**: If Django models, views, serializers, or services are modified, check if the `django-rest-api-review` skill is available. If yes, add to applicable reviews.

### 4. Launch Agents Sequentially

```
Important: Run agents ONE AT A TIME. Wait for each to complete before launching the next.
```

**Tech Stack Specialized Reviews (`react`, `django`)**:
For these reviews, dispatch a subagent (e.g., `generalist`). Instruct the subagent to activate the applicable skills (`react-review` and/or `django-rest-api-review`) one after another using the `activate_skill` tool and perform the review. Wait for the subagent to complete before proceeding.

### 5. Aggregate Results

Organize findings by priority:

```markdown
# PR Review Summary

## Critical Issues (X found)
- [agent/skill-name]: Issue description [file:line]

## Important Issues (X found)
- [agent/skill-name]: Issue description [file:line]

## Suggestions (X found)
- [agent/skill-name]: Suggestion [file:line]

## Strengths
- What's well-done

## Recommended Action
1. Fix critical issues first
2. Address important issues
3. Consider suggestions
4. Re-run review after fixes
```

*Note: Ensure findings from the subagent tech stack reviews (`[react-review]`, `[django-rest-api-review]`) are also explicitly included and organized by priority.*

## Agent Reference

**comment-analyzer**: Verifies comment accuracy vs code, identifies comment rot, checks documentation completeness

**pr-test-analyzer**: Reviews behavioral test coverage, identifies critical gaps, evaluates test quality

**silent-failure-hunter**: Finds silent failures, reviews catch blocks, checks error logging

**type-design-analyzer**: Analyzes type encapsulation, reviews invariant expression, rates type design quality

**code-reviewer**: Checks project documentation compliance, detects bugs and issues, reviews general code quality

**code-simplifier**: Simplifies complex code, improves clarity, applies project standards, preserves functionality

## Usage Patterns

**Before committing:**
```
/review-pr code errors
```

**Before creating PR:**
```
/review-pr all
```

**Targeted review:**
```
/review-pr tests errors
```
