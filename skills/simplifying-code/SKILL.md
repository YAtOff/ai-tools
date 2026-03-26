---
name: simplifying-code
description: Use when working code needs cleanup for clarity, consistency, or maintainability without changing behavior, especially in recently modified files after implementation or review.
---

# Simplifying Code

## Overview

Simplify code without changing behavior. Prefer readable, explicit code over clever compression, and keep changes tightly scoped to the code the user asked about or the recently modified diff.

## When to Use

Use this skill when:
- a change works but the code is harder to read than it needs to be
- review feedback asks for cleanup, consistency, or smaller responsibilities
- recently modified code has duplication, unnecessary nesting, vague names, or redundant comments

Do not use this skill for:
- intentional behavior changes
- broad rewrites outside the requested scope
- speculative refactors without verification

## Required Setup

1. **Read repository instructions first.** When multiple repository instruction files apply, precedence is: file-local or subdirectory instructions, then repo-root `AGENTS.md`, then repo-root `CLAUDE.md`, then the applicable Copilot or Copilog instruction file under `.github/`, such as `.github/copilot-instructions.md`. If repository instructions conflict with this skill or a language-specific styleguide, the more specific repository instruction wins.
2. **Use a dedicated subagent when available.** Run simplification in a dedicated subagent instead of mixing it into the main implementation thread, and pass the applicable repository instructions into that subagent. If delegation is unavailable, keep the simplification work isolated and tightly scoped in the current thread.
3. **Load the matching styleguide skill.** Use the language-specific styleguide skill for the code you are simplifying when one exists, such as `python-styleguide` for Python. Apply it after the repository instructions so language conventions do not override repo rules. If the change spans multiple languages, apply the matching guide per file.
4. **Stay local.** Simplify only the user-specified scope or the recently modified code unless the user asks for a broader pass. If the user has not identified files and there is no recent diff or changed-region context, ask for the target files or diff before simplifying.

## Simplification Workflow

1. Identify the exact files and changed regions in scope.
2. Read nearby tests, types, helpers, and call sites needed to preserve behavior.
3. Simplify for clarity:
   - reduce nesting
   - remove duplication and dead indirection
   - improve names
   - separate mixed responsibilities
   - delete redundant inline comments that only restate the code, but keep required docstrings and comments mandated by repo or language-specific styleguide rules
4. Preserve or improve reliability:
   - keep existing behavior and public interfaces intact unless told otherwise
   - do not add silent fallbacks or broad error swallowing just to make code shorter
   - keep helpful abstractions; do not collapse unrelated concerns into one function
5. Validate with the existing verification path for the touched code.

## Core Pattern

```python
# Before
def active_user_names(users):
    names = []
    for user in users:
        if user is None:
            continue
        if user.is_active:
            if user.name:
                names.append(user.name.strip())
    return names

# After
def active_user_names(users):
    active_names: list[str] = []
    for user in users:
        if user is None or not user.is_active or not user.name:
            continue
        active_names.append(user.name.strip())
    return active_names
```

Same behavior, less nesting, clearer intent. Apply the active language's styleguide before making the change.

## Common Mistakes

- Changing behavior while "cleaning up"
- Applying one framework's rules to unrelated code
- Expanding the refactor far beyond the changed area
- Making code shorter but harder to debug
- Skipping the repo instruction files and local conventions

## Quick Reference

- **Primary goal:** clarity without behavior change
- **Default scope:** recently modified code
- **First checks:** the most specific repository instruction file, then `AGENTS.md`, then `CLAUDE.md`, then the relevant `.github` instruction file, then the matching styleguide skill
- **Execution mode:** dedicated subagent when available
- **Avoid:** framework-specific assumptions unless the code in scope and repo guidance require them
