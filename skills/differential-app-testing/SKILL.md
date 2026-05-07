---
name: differential-app-testing
description: Use when comparing an old and new version of the same web app for UI or functional regressions, especially when you have paired URLs, optional login steps, and need browser evidence plus source-level fix guidance across legacy .js and rewritten .tsx code.
---

# Differential App Testing

## Overview

Treat the **old app as the executable spec**. Use `playwright-cli` to discover controls and visible list items in the old app, replay the same actions on the new app, compare functional, DOM, and visual outcomes, then trace each regression into the legacy `.js` and rewrite `.tsx` code.

**REQUIRED COMPANION SKILL:** Use `playwright-cli` for sessions, login reuse, snapshots, screenshots, interaction replay, and targeted `eval`.

Do **not** switch to generated Playwright tests unless the user explicitly asks for reusable automation. This skill is for direct investigation.

## When to Use

- Old and new versions of the same page or flow both exist
- The user wants regression discovery, not a single-app smoke test
- Filters, search, lists, sorting, item cards, or detail navigation must match legacy behavior
- The user needs evidence plus likely fix locations in both legacy `.js` and rewritten `.tsx` code

Do not use when:
- The pages are not intended to be behaviorally equivalent
- Only one app/version is available
- The user only wants a one-off visual review with no source analysis

## Inputs

- `OLD_URL`
- `NEW_URL`
- Login instructions or reusable auth state
- Legacy source root (`*.js`)
- Rewrite source root (`*.tsx`)
- Optional page or route scope

## Ground Rules

1. **OLD first, always.** Discover controls and expected behavior from the old app before touching the new one.
2. **Keep sessions isolated.** Use named Playwright CLI sessions such as `-s=old` and `-s=new`, with the same browser, viewport, and auth state.
3. **Enumerate all visible targets on the old page.** Capture every filter button, search input, and visible list item on the currently loaded page.
4. **Do not silently sample.** If the page is paginated or infinite-scroll, stop at the current loaded page and explicitly report that boundary.
5. **Compare after every matched action.** Use both `snapshot` and `screenshot`; screenshots alone are not enough.
6. **Browser before code.** Do not build the audit plan from source files first. Establish parity from the old app UI, then inspect code only after a regression is reproduced or a UI-discovered parity gap is already visible.
7. **Trace differences into code immediately once reproduced.** Do not stop at browser evidence if the user asked for fix guidance.

## Default Scope Boundary

Unless the user explicitly asks for deeper coverage:

- Test **every visible filter control** on the currently loaded old-app page
- Test **every visible search input** on the currently loaded old-app page
- Test **every visible actionable list item** on the currently loaded old-app page
- **Do not** auto-traverse every pagination page or exhaust infinite scroll

For paginated or infinite-scroll lists, the default behavior is:
1. Audit the items already loaded on the current page/view
2. Record that pagination or infinite scroll exists
3. Flag deeper traversal as out of scope by default

Do not silently upgrade the audit to full-list traversal.

## Workflow

### 1. Normalize the environment

- Open `old` and `new` sessions with the same browser and viewport
- Perform login once if possible, then reuse `state-save` / `state-load`
- Record the tested route, viewport, and auth assumptions in the report

### 2. Crawl the old app

Do **not** search source files before this step.

Use `snapshot --boxes`, `eval`, and stable attributes to build a target inventory:

- Filter buttons, chips, tabs, toggles, checkboxes, selects
- Search inputs and placeholders
- Visible list items, cards, rows, or links

Record a semantic signature for each target:

- Section heading or nearby label
- Accessible name / placeholder / visible text
- Role and selected/disabled state
- `data-testid`, `id`, class, href, or query key when available
- Visible index only as a last resort

### 3. Build the action matrix from the old app

Replay everything the old page exposes on the current loaded page:

- Default load
- Every filter control change
- Search flows for each input: exact visible item text, partial text, and no-match text
- Clear/reset actions
- Sort or view toggles when present
- Click/open action for every visible list item that is actionable

For paginated or infinite lists, test all currently loaded items and flag that deeper pages were not traversed unless the user explicitly asked for full pagination coverage.

### 4. Replay on the new app

For each old-app action:

- Find the corresponding new-app target from the semantic signature
- Perform the same interaction in the same order
- Capture `snapshot` and `screenshot` after the action settles

If no corresponding target exists, report a parity gap immediately.

Do **not** do an early source pass to invent extra journeys, fill speculative coverage gaps, or replace the replay with code review.

### 5. Compare evidence

After each action, compare:

- URL and query params
- Selected state, counts, ordering, empty/loading/error states
- Visible list text, badges, CTA state, and navigation outcome
- Snapshot structure: roles, accessible names, state, attributes, grouping
- Screenshot differences for layout, visibility, and styling regressions

Prefer semantic DOM differences over raw framework markup diffs.

### 6. Trace the regression into source

When a difference appears, search the legacy `.js` and rewrite `.tsx` trees using:

- Visible labels, placeholders, badges, and item text
- `data-testid`, `id`, class, href, route names, and query keys
- Request URLs or parameter names observed in the browser

Inspect the matching files for:

- Event handlers and action wiring
- Filter/search state and query-param serialization
- Sorting/filter predicates
- API adapters, mappers, and normalization
- Conditional rendering, empty states, and item-card rendering
- CSS or styling logic hiding or misplacing content

## Quick Reference

| Stage | Primary tools | What to capture |
|-------|---------------|-----------------|
| Setup | `playwright-cli open`, `state-save`, `state-load`, `resize` | Matching browser/auth/viewport |
| Crawl old | `snapshot --boxes`, `eval`, `generate-locator` | Filter/search/item inventory and semantic signatures |
| Replay | `click`, `fill`, `press`, `select`, `check` | Exact action sequence from old to new |
| Compare | `snapshot`, `screenshot`, `requests`, `console` | Functional, DOM, and visual evidence |
| Code trace | `rg`, `glob`, `view` | Legacy `.js` vs rewrite `.tsx` root-cause candidates |

## Output

Return a regression report that engineers can fix from directly.

At minimum, include:

| ID | Scenario | Old | New | Evidence | Legacy reference | Rewrite reference | Why it diverged | Fix likely belongs in |
|----|----------|-----|-----|----------|------------------|-------------------|-----------------|-----------------------|

Example row:

| R1 | Search `Acme` | 12 results | 0 results | old/new snapshots + screenshots | `legacy/src/search.js:45` | `new/src/hooks/useListingFilters.ts:28` | New query builder drops the search term | `useListingFilters.ts` and related API param mapping |

## Common Mistakes

### Starting with the new app

- **Problem:** The new app becomes the baseline and missing legacy behavior is overlooked.
- **Fix:** Crawl the old app first and treat it as the source of truth.

### Sampling list items without saying so

- **Problem:** The audit quietly covers only a few items and overstates confidence.
- **Fix:** Test every visible item on the current page and explicitly flag pagination or infinite-scroll boundaries.

### Exhausting pagination or infinite scroll by default

- **Problem:** The audit expands into full-list traversal and becomes slow, brittle, and inconsistent with the default scope.
- **Fix:** Stop at the currently loaded page/view unless the user explicitly asks for full pagination coverage.

### Comparing only screenshots

- **Problem:** Visual diffs catch layout problems but miss state, structure, and query-parameter regressions.
- **Fix:** Pair every screenshot with a snapshot-based semantic DOM comparison.

### Stopping at the UI

- **Problem:** The report names the symptom but gives no actionable fix path.
- **Fix:** Search the corresponding legacy `.js` and rewrite `.tsx` files as soon as the regression is reproduced.

### Starting from code instead of old-app behavior

- **Problem:** The audit becomes a speculative code review instead of an evidence-led regression comparison.
- **Fix:** Crawl the old app first, build the action matrix from observed UI behavior, and only then trace reproduced differences into source.

### Using source code to expand scope before replay

- **Problem:** The audit drifts into speculative coverage planning before any mismatch is observed.
- **Fix:** Keep source inspection in the root-cause phase unless the UI itself already exposed a missing or mismatched feature.

### Replacing the audit with generated test code

- **Problem:** Time is lost building automation instead of investigating the live difference.
- **Fix:** Stay in `playwright-cli` unless the user explicitly asks for reusable Playwright tests.
