---
name: ubiquitous-language-extractor
description: Systematic discovery and extraction of Ubiquitous Language from an undocumented or existing codebase. Use when you need to understand the business intent, domain entities, and core logic buried in the implementation.
---

# Ubiquitous Language Extractor

This skill provides a structured methodology for "Software Archaeology" to reconstruct a Domain-Driven Design (DDD) Ubiquitous Language from an existing codebase.

## Workflow

Follow these steps to extract the language from the repository.

### 1. Noun Discovery (Static Analysis)
Identify the core domain entities and value objects by analyzing the data structures.

- **Data Model:** Examine database schemas, migration files, and ORM definitions.
- **Domain Objects:** Search for classes/types in directories like `Domain`, `Models`, `Entities`, or `Core`.
- **Naming Clashes:** Note differences between variable names and their semantic meaning (e.g., a class `User` that exclusively handles `Member` logic).

### 2. Verb Discovery (Behavioral Analysis)
Identify the actions and business processes by analyzing logic and state transitions.

- **Domain Services/Use Cases:** Analyze method names in service layers (e.g., `OrderService.fulfill()`).
- **State Transitions:** Search for status updates and enums. Map magic numbers (e.g., `status = 3`) to their domain meanings (e.g., `AWAITING_SHIPMENT`).
- **Intentful Verbs:** Prioritize domain-specific verbs (e.g., `refund`, `reconcile`, `onboard`) over generic CRUD verbs (`save`, `update`).

### 3. Intent Discovery (Contextual Analysis)
Understand the "Why" behind the code using metadata and tests.

- **Automated Tests:** Read test descriptions (e.g., `should_not_allow_checkout_when_cart_is_empty`). These often reveal business invariants.
- **Git History:** Search commit logs and PR descriptions for domain keywords and business justifications.
- **Bounded Contexts:** Identify if the same noun has different meanings in different modules (e.g., `Account` in `Billing` vs. `Account` in `Identity`).

### 4. Synthesis and Validation
Consolidate the findings into a draft glossary.

- **Structure:** Organize by Bounded Context.
- **Drafting:** For each term, provide:
    - **Term:** The Ubiquitous Language name.
    - **Source Code equivalent:** The class/variable/method name currently used.
    - **Definition:** The business meaning derived from the code.
- **Validation:** Present the draft to a domain expert to align the code terminology with the actual business speech.

## Output Template

Use the following format for the extracted language:

| Ubiquitous Term | Code Symbol | Context | Business Definition |
| :--- | :--- | :--- | :--- |
| [Term] | `[Symbol]` | [Module] | [Derived Meaning] |

## Search Patterns

Useful grep patterns for discovery:

```bash
# Find entities
grep -rE "class|interface|type" src/domain

# Find status enums
grep -riE "status|state|enum" src/

# Find domain-specific actions
grep -rE "public function [a-z]+[A-Z]" src/services
```
