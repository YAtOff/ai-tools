---
name: react-review
description: Systematic React.js code review based on modern best practices. Analyzes components for architectural patterns, state management, performance, and error handling. Use when reviewing React code, refactoring components, or ensuring adherence to React best practices.
license: MIT
---

# React.js Code Review Skill

## Overview

This skill performs comprehensive, systematic reviews of React.js codebases against modern best practices. It identifies architectural anti-patterns and provides actionable recommendations to improve maintainability, scalability, and performance.

**Keywords**: React, code review, best practices, refactoring, components, hooks, state management, performance optimization, architectural patterns

## Mission

Evaluate React.js code against established best practices to foster a codebase that is maintainable, scalable, and performant. The review focuses on architectural improvements rather than just functional errors.

## How to Use This Skill

When invoked, you will be prompted to specify what to review. You can provide:

- **File paths**: `src/components/Header.jsx` or `src/**/*.tsx`
- **Git diff**: "review my uncommitted changes" or "review staged files"
- **Pull request**: "review PR #123" or provide a PR URL
- **Commit/branch**: "review commit abc123" or "compare feature-branch with main"
- **Directory**: "review all files in src/components/"

If no scope is specified, ask the user what they want to review before proceeding.

---

## 1. Foundational Principles Assessment

### 1.1. Component Composition Philosophy

**Check**: Evaluate whether the codebase favors **Composition over Inheritance**.

**Detection**:
- Identify ES6 class components using `extends React.Component`
- Flag inheritance-based patterns

**Recommendation**:
- Refactor class components to function components
- Use Hooks for stateful logic
- Use component nesting for UI structure
- Emphasize compositional approach for flexibility and reusability

### 1.2. Separation of Concerns (SoC)

**Check**: Scan for "everything-in-component" anti-patterns where a single component mixes:
- Data fetching
- State management
- Business logic
- UI rendering

**Detection**:
- Components with multiple unrelated responsibilities
- Mixed concerns in single files

**Recommendation**: Split responsibilities into distinct layers:
- **UI/Presentation**: Components responsible only for rendering based on props
- **Stateful Logic/Side Effects**: Extract into custom Hooks (e.g., `usePaymentMethods`)
- **Business Logic**: Move to plain TypeScript/JavaScript helper functions or domain model classes
- **Data Fetching**: Isolate in dedicated data layer or specialized hooks

**Justification**: Layered architecture improves readability, testability, and maintainability.

### 1.3. Single Responsibility Principle (SRP)

**Check**: Analyze each component and custom Hook for scope of responsibility.

**Detection**:
- Components handling multiple unrelated tasks
- "Too busy" components

**Recommendation**:
- Decompose monolithic components into smaller, focused components
- Extract distinct features into separate components
- Example: Split `Payment` component into `PaymentMethods` and `DonationCheckbox`

### 1.4. Domain Modeling and Business Logic Extraction

**Philosophy**: Treat React as a **view library only**, not a complete framework. Business logic should live outside React components in plain JavaScript/TypeScript.

**Check**: Identify business logic leakage into React components.

**Detection**:
- Conditional logic directly in JSX (e.g., `method.provider === "cash"`, `user.role === "admin"`)
- Data transformations and calculations within component bodies
- Business rules scattered across multiple components
- Validation logic mixed with rendering logic
- "Shotgun surgery" pattern: one business requirement change requires modifications across multiple files

**Recommendation**: Implement a **layered architecture** with clear separation:

#### Three-Layer Structure

1. **Presentation Layer** (React Components)
   - Responsible **only** for rendering UI based on props
   - No business logic or data transformation
   - Purely declarative
   - Example: `<PaymentMethodsList methods={methods} />`

2. **Domain/Model Layer** (Business Logic)
   - Plain JavaScript/TypeScript classes or functions
   - Contains business rules, validations, and calculations
   - Independent of React - testable without component framework
   - Encapsulates both data and related behavior
   - Example: `PaymentMethod` class with methods like `isCash()`, `isDefault()`, `requiresVerification()`

3. **Data Access Layer** (Network/External Services)
   - API calls and external service integration
   - Data fetching and persistence
   - Example: `paymentService.fetchMethods()`, `userService.authenticate()`

#### Domain Object Pattern

**Detection**: Logic checks scattered throughout components
```javascript
// ❌ Logic leakage into view
function PaymentList({ methods }) {
  return methods.map(method => (
    <div>
      {method.provider === "cash" && <CashIcon />}
      {method.id === user.defaultMethodId && <Badge>Default</Badge>}
    </div>
  ));
}
```

**Recommendation**: Extract into domain classes
```javascript
// ✅ Domain class encapsulates logic
class PaymentMethod {
  constructor(data) {
    this.id = data.id;
    this.provider = data.provider;
    this.defaultMethodId = data.defaultMethodId;
  }

  isCash() {
    return this.provider === "cash";
  }

  isDefault(userId) {
    return this.id === this.defaultMethodId;
  }

  requiresVerification() {
    return ["card", "bank"].includes(this.provider);
  }
}

// ✅ Clean view component
function PaymentList({ methods }) {
  return methods.map(method => (
    <div>
      {method.isCash() && <CashIcon />}
      {method.isDefault() && <Badge>Default</Badge>}
    </div>
  ));
}
```

#### Strategy Pattern for Variability

**Detection**:
- Multiple conditionals based on type/category across different files
- Country-specific, user-role-specific, or feature-flag-based variations
- Repeated switch statements or if-else chains

**Recommendation**: Use Strategy pattern with dependency injection

```javascript
// ❌ Scattered conditionals
function processPayment(method, country) {
  if (country === "US") {
    // US-specific logic
  } else if (country === "EU") {
    // EU-specific logic
  }
}

// ✅ Strategy pattern
class PaymentProcessor {
  constructor(strategy) {
    this.strategy = strategy;
  }

  process(method) {
    return this.strategy.process(method);
  }
}

class USPaymentStrategy {
  process(method) {
    // US-specific logic
  }
}

class EUPaymentStrategy {
  process(method) {
    // EU-specific logic
  }
}

// Usage in component
function Payment({ country }) {
  const strategy = country === "US" ? new USPaymentStrategy() : new EUPaymentStrategy();
  const processor = new PaymentProcessor(strategy);
  // ...
}
```

#### Anti-Corruption Layer Pattern

**Detection**:
- Direct coupling to external API response structures
- API changes forcing component updates
- Data transformation scattered across components

**Recommendation**: Create gateway/adapter layer

```javascript
// ❌ Direct API coupling
function UserProfile() {
  const { data } = useQuery('/api/user');
  return <div>{data.user_full_name}</div>; // Coupled to API structure
}

// ✅ Anti-corruption layer
class UserGateway {
  static async fetchUser() {
    const response = await fetch('/api/user');
    const data = await response.json();

    // Adapt external structure to domain model
    return new User({
      name: data.user_full_name,
      email: data.email_address,
      id: data.user_id
    });
  }
}

function UserProfile() {
  const { data: user } = useQuery('user', UserGateway.fetchUser);
  return <div>{user.name}</div>; // Decoupled from API
}
```

**Benefits of Domain Modeling**:
- **Testability**: Domain logic testable without React testing frameworks
- **Reusability**: Business logic portable across different UI frameworks
- **Maintainability**: Changes to business rules centralized in domain layer
- **Clarity**: Components become simpler, focused purely on presentation
- **Scalability**: New requirements map to specific layers, reducing coupling

**Justification**: This layered approach mirrors proven desktop GUI patterns and creates independence between layers, enabling teams to work on business logic and UI independently.

---

## 2. Component Architecture and Structure Review

### 2.1. Identify Monolithic Components

**Detection**:
- Large components with excessive line count (>200 lines)
- Deeply nested JSX (>4 levels)
- Components that are difficult to understand or test

**Recommendation**:
- Apply "Extract Component" pattern
- Break complex UI into hierarchy of smaller, reusable components
- Each component should have a single, clear purpose
- Example: Break `Tweet` into `Avatar` and `User` components

### 2.2. Component Type Standards

**Detection**:
- ES6 class components in new or recently modified code
- Usage of legacy patterns

**Recommendation**:
- Migrate class components to function components with Hooks
- React 18+ recommends function components for:
  - Simpler code
  - No `this` complexity
  - Better integration with new features
  - Avoidance of lifecycle method complexity

### 2.3. Controlled vs. Uncontrolled Inputs

**Check**: Analyze form elements (`<input>`, `<select>`, `<textarea>`) for state management approach.

**Detection**:
- Inputs without `value` and `onChange` props (uncontrolled)
- DOM-managed state in forms

**Recommendation**:
- Refactor to controlled inputs for predictable state management
- Link `value` and `onChange` props to React state
- Required for: validation, modification, parent component access

**Example**:
```javascript
// ❌ Uncontrolled - manages its own state
const UncontrolledInput = () => {
    return <input type="text" />;
};

// ✅ Controlled - state managed by React
const ControlledInput = ({ value, onChange }) => {
    return <input type="text" value={value} onChange={onChange} />;
};
```

### 2.4. Advanced Structural Patterns

**Check**: Identify opportunities for advanced patterns. See `patterns-reference.md` for detailed explanations and examples.

**Key Patterns to Look For**:

1. **Custom Hooks Pattern**
   - **Detection**: Reusable logic duplicated across components
   - **Recommendation**: Extract logic into custom hooks
   - **Benefit**: Improved testability, reusability, separation of concerns

2. **Container/Presentational Pattern**
   - **Detection**: Components mixing data fetching with UI rendering
   - **Recommendation**: Split into container (logic) and presentational (UI) components
   - **Benefit**: Clear separation of concerns, enhanced reusability

3. **Compound Components Pattern**
   - **Detection**: Components with large, unwieldy prop interfaces
   - **Recommendation**: Use compound component pattern for shared implicit state
   - **Benefit**: Improved API clarity, flexible composition, avoids prop drilling

4. **Headless Components Pattern**
   - **Detection**: Complex logic with tightly coupled styling
   - **Recommendation**: Extract logic into custom hook (e.g., `useDropdown`) with prop getters
   - **Benefit**: Maximum flexibility, reusable logic, full styling control

5. **Portals Pattern**
   - **Detection**: Modals/tooltips/dropdowns with CSS containment issues (z-index, overflow)
   - **Recommendation**: Use `ReactDOM.createPortal` to render outside parent DOM
   - **Benefit**: Solves CSS constraints, maintains React tree structure

6. **Props Getters Pattern**
   - **Detection**: Components passing many related props (especially accessibility attributes)
   - **Recommendation**: Provide functions that return pre-configured prop objects
   - **Benefit**: Simplified prop management, encapsulated complexity

**Legacy Patterns to Migrate Away From**:

- **Higher Order Components (HOC)**: Replace with custom hooks for better readability
- **Render Props**: Replace with custom hooks in most cases (unless specific use case requires it)

**Note**: Refer to `patterns-reference.md` for comprehensive explanations, code examples, and when to use each pattern.

---

## 3. State Management Strategy Analysis

### 3.1. Categorize State Type

**Process**: Classify every piece of state into:

1. **Local State**: Used by only one component (e.g., dropdown `isOpen`)
2. **Shared State**: Required by multiple components (e.g., theme, auth status)
3. **Remote State**: Data from external API/database (loading, error, success lifecycle)
4. **URL State**: Reflected in URL query parameters (e.g., search filters, active tabs)

### 3.2. Remote State Handling

**Detection**:
- Manual data fetching in `useEffect` hooks
- Combined with `useState` for loading/error states
- Boilerplate-heavy implementation
- Race condition vulnerabilities

**Recommendation**:
- Replace with dedicated data-fetching library:
  - **TanStack Query** (React Query)
  - **SWR**
- **Benefits**: Automatic caching, request deduplication, background refetching, built-in loading/error states

### 3.3. URL State Handling

**Detection**:
- Manual synchronization between component state and URL query parameters
- Complex two-way binding logic
- Error-prone parsing/setting

**Recommendation**:
- Use routing library hooks: `useSearchParams` (React Router)
- Or dedicated library: **nuqs**
- **Benefits**: Abstracts complexity, simple as local state

### 3.4. Shared State Handling

**Two-step analysis**:

**Step 1: Detect Prop Drilling**
- **Detection**: Props passed through 3+ levels of intermediate components that don't use them
- **Recommendation**:
  - Localized sharing: "Lifting state up" pattern
  - Global sharing: Context API

**Step 2: Detect Context Overuse**
- **Detection**:
  - "Provider Hell" (excessive nested Providers)
  - Large, monolithic context objects
- **Performance Issue**: All consumers re-render on any context value change
- **Recommendation**:
  - Use lightweight global state library: **Zustand**
  - **Benefits**: No provider nesting, selector-based model, granular re-renders

---

## 4. Performance Optimization and Error Handling

### 4.1. Identify Unnecessary Re-renders

**Detection**:
- Components receiving objects, arrays, or functions as props
- Props re-created on every parent render
- Wasteful child re-renders despite unchanged data

**Recommendation**: Apply memoization techniques:
- **React.memo**: Wrap functional components to prevent re-renders if props unchanged
- **useMemo**: Memoize expensive calculations or complex objects/arrays passed as props
- **useCallback**: Memoize callback functions passed to child components

### 4.2. Lazy Loading

**Detection**:
- Large components loaded eagerly
- Route-specific components in initial bundle
- Large initial JavaScript bundle size

**Recommendation**:
- Implement code-splitting with `React.lazy()`
- Wrap in `<Suspense>` boundary with fallback UI
- **Benefits**: Reduced initial bundle size, faster load times

**Example**:
```javascript
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### 4.3. Error Boundaries

**Detection**:
- Critical UI sections that could fail independently
- Third-party integrations
- Data widgets
- Missing error handling for component failures

**Recommendation**:
- Wrap fragile component sub-trees with Error Boundaries
- Error Boundary acts as try-catch for React components
- Catches JavaScript errors in child tree
- Renders fallback UI instead of crashing entire app

**Example**:
```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

---

## Review Process

### Understanding the Scope

The user will specify what to review. Common scenarios include:

1. **Specific Files**: Direct file paths or patterns (e.g., "review src/components/Header.jsx")
2. **Git Diff**: Changes in working directory (`git diff`) or staged changes (`git diff --cached`)
3. **Pull Request**: Review changes in a PR (e.g., "review PR #123" or `gh pr diff 123`)
4. **Commit Range**: Specific commits (e.g., "review commit abc123" or `git diff main...feature-branch`)
5. **All Files**: Review entire codebase or specific directories

### Review Workflow

When conducting a review:

1. **Determine Review Scope**:
   - Ask the user what to review if not specified
   - Use appropriate git commands to get the file list:
     - `git diff` for uncommitted changes
     - `git diff --cached` for staged changes
     - `gh pr diff <pr-number>` for pull requests
     - `git diff <commit1>..<commit2>` for commit ranges
     - `git diff <branch>...HEAD` for branch comparisons

2. **Identify React Files**:
   - Filter for React-related files: `.jsx`, `.tsx`, `.js`, `.ts` files
   - Focus on files that import React or use JSX syntax
   - Ignore non-React files, tests (unless specifically requested), and build artifacts

3. **Read and Analyze Files**:
   - Read the identified files
   - Understand component structure and patterns
   - Identify related files (hooks, utilities, types)

4. **Apply Systematic Checks** following the sections above in order:
   - Foundational Principles (Composition, SoC, SRP)
   - Component Architecture (Monolithic components, types, input handling, patterns)
   - State Management (Categorize, remote/URL/shared state handling)
   - Performance & Error Handling (Re-renders, lazy loading, error boundaries)

5. **Document Findings**:
   - Note specific file locations and line numbers
   - Include code snippets showing the issue
   - Explain why it's a problem

6. **Provide Actionable Recommendations**:
   - Offer concrete code examples
   - Prioritize by impact on maintainability and performance
   - Consider the review scope (e.g., for PRs, focus on changed lines)

## Output Format

Structure the review report as follows:

### Executive Summary
- Overall code quality assessment
- Critical issues count
- Key recommendations

### Detailed Findings

For each category (Foundational Principles, Architecture, State Management, Performance):
- **Finding**: What was detected
- **Location**: File path and line numbers
- **Issue**: Why it's a problem
- **Recommendation**: How to fix it with code examples
- **Priority**: High/Medium/Low

### Refactoring Roadmap
- Prioritized list of improvements
- Estimated effort for each
- Dependencies between changes

---

## Additional Resources

This skill includes supplementary reference materials for deeper context:

### patterns-reference.md
Comprehensive guide to 21+ React design patterns including:
- Core patterns (Composition, Custom Hooks, Control Props, Provider)
- Structural patterns (Container/Presentational, Compound Components, Headless Components, Atomic Design)
- Advanced patterns (Render Props, Props Getters, HOCs)
- General principles (DRY, SOLID, SoC, MVVM, KISS)
- Modern patterns (Server Components, Suspense, Concurrent features)
- Complete code examples for each pattern
- When to use and when to avoid each pattern
- Pattern selection guide

**Use this reference when**:
- You need detailed examples of a specific pattern
- Explaining pattern benefits to the user
- Deciding between multiple pattern options
- Understanding modern vs legacy patterns

---

## Conclusion

This systematic framework ensures React codebases are resilient, scalable, and maintainable. Apply these principles consistently to build robust React applications that are prepared for long-term evolution.

When conducting reviews, keep the main skill principles in focus and reference `patterns-reference.md` for detailed pattern information to keep context concise.
