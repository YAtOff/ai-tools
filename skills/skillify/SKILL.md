---
name: skillify
description: Use when the user wants to formalize a repeatable process from the current session into a reusable skill. Triggers on 'extract skill', 'skillify this', 'save as skill', 'automate this process'.
---

# Skillify

Extract a repeatable process from the current session and save it as a new, reusable skill through a structured interview process.

## Overview
This skill guides you through analyzing the current conversation, interviewing the user for detailed preferences, and generating a standardized `SKILL.md` file. It ensures that the resulting skill is actionable, well-structured, and easy to maintain.

## When to Use
- After successfully performing a task that might be needed again.
- When the user says: "extract skill", "skillify this", "save as skill", "automate this process".
- When a complex workflow is discovered and needs to be codified for future automation.

## Workflow

### 1. Analyze the Session
Analyze the session to identify:
- The repeatable process performed.
- Inputs/parameters and distinct steps in order.
- Success artifacts/criteria for each step (e.g., "PR merged", "file written", "test passed").
- Tools, permissions, and agents used.
- Goals and user steering points (where they corrected or guided you).

### 2. Interview the User (Iterative Rounds)
**You MUST use `ask_user` for all questions!** Never ask via plain text.

**Round 1: High-level Confirmation**
- Suggest a name and description. Ask user to confirm/rename.
- Suggest high-level goal(s) and specific success criteria.

**Round 2: Details**
- Present high-level steps as a numbered list.
- Suggest arguments/placeholders based on observation.
- Ask if it should run `inline` (current conversation) or `forked` (sub-agent with its own context).
- Ask for storage location:
  - **This repo** (`.gemini/skills/<name>/SKILL.md`) — for project-specific workflows.
  - **Personal** (`~/.gemini/skills/<name>/SKILL.md`) — for cross-repo personal workflows.

**Round 3: Breaking down each step**
For each major step, clarify:
- Produced data/artifacts/IDs for later steps (e.g., PR number, commit SHA).
- Proof of success (what proves we can move on?).
- Human checkpoints (especially for destructive/irreversible actions).
- Parallel execution or specific agent execution.
- Tools and agents: Which specific tools (and their permissions) or specialized agents should be used?
- Hard rules and constraints (especially from user steering).

**Round 4: Final Questions**
- Confirm trigger phrases (e.g., "Use when... Examples: ...").
- Ask for any other gotchas or specific preferences.

### 3. Write the SKILL.md
Create the skill directory and file. Use the following structure:

```markdown
---
name: {{skill-name}}
description: {{one-line description (TRIGGERING CONDITIONS ONLY)}}
allowed-tools:
  {{list of tool permission patterns}}
when_to_use: {{detailed trigger description and examples}}
argument-hint: "{{hint showing argument placeholders}}"
arguments:
  {{list of argument names}}
context: {{inline or fork -- omit for inline}}
---

# {{Skill Title}}
Description of skill

## Inputs
- `$arg_name`: Description

## Goal
Clearly stated goal and success criteria for completion.

## Steps

### 1. Step Name
Specific, actionable instructions. Include commands where appropriate.

**Success criteria**: REQUIRED. Shows when the step is done.
**Execution**: `Direct`, `Task agent`, `Teammate`, or `[human]`.
**Artifacts**: Data produced for later steps.
**Human checkpoint**: When to pause for user confirmation.
**Rules**: Hard constraints (often from user corrections).
**Tools/Agents**: Specific tools or agents to use for this step.

...
```

### 4. Final Review and Save
1. Output the complete `SKILL.md` content as a YAML code block for review.
2. Use `ask_user` to get final confirmation: "Does this SKILL.md look good to save?".
3. Write the file to the chosen location.
4. Inform the user:
   - Where the skill was saved.
   - How to invoke it: `/{{skill-name}} [arguments]`.
   - They can edit the file directly to refine it.

## Common Mistakes
- **Skipping `ask_user`**: Always use the tool for interviews.
- **Vague Steps**: Steps must be specific and actionable.
- **Missing Success Criteria**: Every step needs a clear "done" state.
- **Ignoring User Steering**: User corrections during the session are critical rules.
- **Summarizing Workflow in Description**: The description should ONLY be the "When to Use" trigger.
- **Incomplete Frontmatter**: Forgeting `allowed-tools` or `context`.
