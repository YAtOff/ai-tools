---
name: software-design-review
description: Use when reviewing a project's high-level software design, architecture boundaries, dependency direction, domain alignment, structural testability, security/trust boundaries and observability seams, or resilience and fault-tolerance structure.
---

# Software Design Review

## Overview

Review the repository's structure, not its implementation trivia. Core principle: every structural claim must be backed by concrete repo evidence, and incomplete evidence must stay explicit.

## When to Use

- High-level review of a whole repo or a scoped path
- Questions about boundaries, dependency direction, module shape, domain alignment, flow of control, or structural testability
- Architecture feedback that should stay concise and evidence-based

Do not use this for style cleanup, low-level bug hunting, or framework migration planning.

## Required Review Workflow

1. Set scope from the user request; if the user does not narrow it, default to the current repository root. Keep every claim, evidence point, and recommendation focused on that scope. For path-scoped reviews, treat unrelated files outside the requested path as unavailable evidence. Only use minimal directly connected boundary context when needed to explain the scoped path's structure (for example its containing package, direct imports/exports, composition root, direct package manifest, or boundary tests), and label that context explicitly instead of widening the review.
2. Read [`design-review-reference.md`](./design-review-reference.md) before dispatching dimension reviews; it contains the rubric details for each dimension. Skim the scope's top-level layout (root listing, package manifests, composition root) yourself so the dispatched sub-agents share a consistent view of the scope.
3. **Dispatch one sub-agent per dimension, in parallel.** Use the `task` tool with `agent_type: "explore"` and `mode: "background"`, launching all seven sub-agents in a single response. Each sub-agent owns exactly one dimension and produces only that dimension's section of the final report.
4. Each sub-agent prompt must include, verbatim or by faithful summary:
   - The agreed scope (repo root or specific path) and the path-scoping rules from step 1.
   - The dimension name and its rubric excerpt from `design-review-reference.md` (read the relevant section and inline it; sub-agents are stateless and will not re-read the file unless told to).
   - The Output Contract subsection format (the four bullets) and the rule that the sub-agent must return exactly that section, nothing else.
   - The Evidence Rules and Common Mistakes from this file.
   - Any shared scope-orientation notes you gathered in step 2 (e.g., "top-level dirs are X, Y, Z; composition root is `cmd/app/main.go`").
5. Wait for all sub-agents to finish, then assemble their outputs into the final report in the order listed under Output Contract. Do not rewrite their findings; only fix ordering, deduplicate cross-dimension evidence citations if needed, and add the closing **Structural priorities** section yourself by synthesizing across the seven returned sections.
6. In every section (as produced by the sub-agent), confirmed findings must stay separate from inference. Use `Concrete repo evidence` to note evidence completeness when direct proof is missing, instead of guessing. If the scoped path does not provide enough evidence for a dimension, the sub-agent must say so instead of backfilling from elsewhere in the repo.
7. Respond in chat with the assembled concise report. Do **not** generate a file unless the user explicitly asks for one.

## Output Contract

Use one section per dimension, in this order:
1. Package and module architecture
2. Class and interface structure
3. Domain integrity and tactical DDD
4. Paradigm synergy and flow of control
5. Testability and maintainability
6. Security boundaries and observability
7. Resilience and fault tolerance

Inside **every** section include:
- **Confirmed strengths**
- **Confirmed risks**
- **Concrete repo evidence**
- **Highest-value structural recommendation** (or `No recommendation warranted from current evidence.`)

End with a short **Structural priorities** section.

Rules for the report:
- Use the exact seven section titles above. Do not add extra review categories.
- Keep the report concise and architectural.
- Prefer a few high-signal findings over exhaustive narration.
- For path-scoped requests, every file or directory cited in `Concrete repo evidence` should live inside the requested path unless it is minimal directly connected boundary context that you label explicitly.
- If a subsection has no supported finding, write `None confirmed from current evidence.`
- If the evidence does not support a change recommendation, write `No recommendation warranted from current evidence.`
- Keep `Confirmed strengths` and `Confirmed risks` limited to supported findings; put partial or missing-evidence notes inside `Concrete repo evidence`.

## Evidence Rules

- Ground every strength, risk, and any recommendation in concrete repo evidence such as files, directories, imports, interfaces, tests, dependency wiring, or naming.
- Keep every claim and recommendation focused on the scoped repo/path. Recommendations may reference minimal directly connected boundary context when that seam is the actual source of a scoped issue.
- For path-scoped reviews, cite evidence only from files and directories inside that path unless the user explicitly expands the scope or you need minimal directly connected boundary context. Do not compare the scoped path against sibling areas or repo-root docs to manufacture findings.
- Treat docs, prompts, and comments as supporting evidence until code, config, or tests confirm them.
- Do not force DDD, clean architecture, hexagonal architecture, eventing, or orchestration claims without direct evidence.
- Do not invent classes, interfaces, inheritance hierarchies, shared libraries, or tools that are not present in the repo.
- Treat folder, package, and public type names as structural evidence when they reveal domain boundaries or cognitive load.
- Ignore style-only feedback unless naming or layout hides a structural boundary or responsibility problem.

## Common Mistakes

- **Generic architecture narration** — summarizing what a well-designed system should look like instead of reporting repo-specific findings.
- **Unsupported certainty** — stating speculative structure as confirmed fact.
- **Scope leakage** — pulling evidence from outside the requested path instead of admitting the scoped evidence is incomplete.
- **Style-only feedback** — focusing on formatting, naming nits, or implementation trivia.
- **Checklist drift** — wandering outside the seven dimensions or prescribing new frameworks, files, or abstractions unrelated to the evidence.
