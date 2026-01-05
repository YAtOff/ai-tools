# React Design Patterns Reference

This document provides detailed information about React design patterns to support code reviews. Use this as a reference when identifying patterns in the codebase.

## Architectural Patterns

### 0. Layered Architecture Pattern

**Description:** Organizing React applications into distinct layers with clear responsibilities and dependencies flowing in one direction.

**When to Use:**
- Non-trivial applications requiring long-term maintainability
- Teams needing clear architectural boundaries
- Applications where business logic should be framework-independent

**The Three Layers:**

1. **Presentation Layer (View)**
   - React components focused solely on rendering
   - Receives data via props
   - Delegates user actions to domain layer
   - No business logic

2. **Domain Layer (Model/Business Logic)**
   - Plain JavaScript/TypeScript classes and functions
   - Contains all business rules, validations, calculations
   - Framework-independent (no React dependencies)
   - Testable without component testing tools

3. **Data Access Layer (Services/Repositories)**
   - API calls and external service integration
   - Data fetching and persistence
   - Adapts external data to domain models

**Key Benefits:**
- Independent testing of each layer
- Business logic reusable across different UI frameworks
- Clear mental models for developers
- Changes isolated to specific layers

**Example:**
```javascript
// ❌ Mixed layers - everything in component
function UserProfile() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch('/api/user')
      .then(res => res.json())
      .then(data => {
        // Business logic mixed in
        const fullName = data.first_name + ' ' + data.last_name;
        const isActive = data.status === 'active' && data.verified;
        setUser({ ...data, fullName, isActive });
      });
  }, []);

  // More business logic in component
  const canEdit = user?.role === 'admin' || user?.id === currentUserId;

  return (
    <div>
      {user?.fullName}
      {canEdit && <EditButton />}
    </div>
  );
}

// ✅ Layered architecture
// Data Access Layer
class UserService {
  static async fetchUser() {
    const response = await fetch('/api/user');
    return response.json();
  }
}

// Domain Layer
class User {
  constructor(data) {
    this.id = data.id;
    this.firstName = data.first_name;
    this.lastName = data.last_name;
    this.status = data.status;
    this.verified = data.verified;
    this.role = data.role;
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  get isActive() {
    return this.status === 'active' && this.verified;
  }

  canBeEditedBy(currentUserId) {
    return this.role === 'admin' || this.id === currentUserId;
  }
}

// Presentation Layer
function UserProfile({ currentUserId }) {
  const { data: userData } = useQuery('user', UserService.fetchUser);
  const user = userData ? new User(userData) : null;

  if (!user) return <Loading />;

  return (
    <div>
      {user.fullName}
      {user.canBeEditedBy(currentUserId) && <EditButton />}
    </div>
  );
}
```

**Philosophy:**
"React is a humble library for building views" - treat it as such. Keep React components focused on presentation, and extract all business logic to the domain layer.

---

## Core Design Patterns

### 1. Component Composition Pattern
**Description:** Breaking applications into smaller, reusable components that work together rather than building monolithic structures.

**When to Use:** Always—foundational to React development.

**Key Benefits:**
- Modularity and flexibility
- Code reusability across the application
- Easier maintenance and testing

---

### 2. Custom Hook Pattern
**Description:** Extracting and encapsulating logic (useState, useEffect) into reusable hooks to separate concerns from component rendering.

**When to Use:**
- When components contain reusable logic
- When components become overly complex
- To share stateful logic across components

**Key Benefits:**
- Improved testability
- Code reusability
- Better readability through naming
- Separation of concerns

**Example:**
```javascript
// Before: Logic mixed in component
function UserProfile() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/user')
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, []);

  return loading ? <Spinner /> : <div>{user.name}</div>;
}

// After: Logic extracted to hook
function useUser() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/user')
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, []);

  return { user, loading };
}

function UserProfile() {
  const { user, loading } = useUser();
  return loading ? <Spinner /> : <div>{user.name}</div>;
}
```

---

### 3. Control Props Pattern
**Description:** Determining whether components manage their own state (uncontrolled) or receive state from parents (controlled).

**When to Use:**
- When parent components need to control or override child state programmatically
- For form inputs that need validation
- When state needs to be synchronized across components

**Key Benefits:**
- Flexibility in component usage
- Adherence to the Open/Closed Principle
- Predictable state management

**Example:**
```javascript
// Uncontrolled
function UncontrolledInput() {
  return <input type="text" />;
}

// Controlled
function ControlledInput({ value, onChange }) {
  return <input type="text" value={value} onChange={onChange} />;
}

// Hybrid (supports both patterns)
function FlexibleInput({ value: controlledValue, onChange, defaultValue }) {
  const [internalValue, setInternalValue] = useState(defaultValue || '');

  const isControlled = controlledValue !== undefined;
  const value = isControlled ? controlledValue : internalValue;

  const handleChange = (e) => {
    if (!isControlled) {
      setInternalValue(e.target.value);
    }
    onChange?.(e);
  };

  return <input type="text" value={value} onChange={handleChange} />;
}
```

---

### 4. Provider Pattern (Context API)
**Description:** Using React Context API and useContext to share state across component subtrees without prop drilling.

**When to Use:**
- Sharing infrequently-changing app-wide configurations (themes, localization, auth)
- Avoiding deep prop drilling for data needed by many components
- **NOT for global state management** (use Zustand, Redux, or Jotai instead)

**Key Benefits:**
- Eliminates prop drilling
- Centralizes configuration management
- Clean component interfaces

**Important Notes:**
- Context causes re-renders of all consumers when value changes
- Best for data that changes infrequently
- Not suitable for frequently-updating state

**Example:**
```javascript
const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const { theme } = useContext(ThemeContext);
  return <button className={theme}>Click me</button>;
}
```

---

## Structural Patterns

### 5. Container/Presentational Pattern
**Description:** Separating components into containers (handling logic/data) and presentational components (rendering only).

**When to Use:**
- Implementing Single Responsibility Principle
- Creating reusable UI components
- When the same UI needs to be used with different data sources

**Key Benefits:**
- Clear separation of concerns
- Enhanced component reusability
- Easier testing (presentational components are pure)

**Example:**
```javascript
// Container Component (logic)
function UserListContainer() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUsers().then(data => {
      setUsers(data);
      setLoading(false);
    });
  }, []);

  return <UserList users={users} loading={loading} />;
}

// Presentational Component (UI only)
function UserList({ users, loading }) {
  if (loading) return <Spinner />;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

### 6. Compound Components Pattern
**Description:** Multiple components working together as a cohesive unit while sharing internal state through React Context.

**When to Use:**
- Building complex, customizable UI components
- When components have interdependent behavior
- To create flexible APIs without excessive props

**Key Benefits:**
- Improved API readability
- Avoidance of excessive prop drilling
- Flexible composition

**Example:**
```javascript
const TabsContext = createContext();

function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ id, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      className={activeTab === id ? 'active' : ''}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
}

function TabPanel({ id, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === id ? <div>{children}</div> : null;
}

// Attach sub-components
Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panel = TabPanel;

// Usage - clean and intuitive API
<Tabs defaultTab="profile">
  <Tabs.List>
    <Tabs.Tab id="profile">Profile</Tabs.Tab>
    <Tabs.Tab id="settings">Settings</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel id="profile">Profile content</Tabs.Panel>
  <Tabs.Panel id="settings">Settings content</Tabs.Panel>
</Tabs>
```

---

### 7. Headless Components Pattern
**Description:** Providing complex component logic without styling, allowing consumers to handle visual presentation completely.

**When to Use:**
- Building reusable component logic with styling flexibility
- Creating component libraries for multiple design systems
- When UI requirements vary but logic remains consistent

**Key Benefits:**
- Full control over styling
- Separation of concerns (UI logic vs. styling)
- Maximum reusability

**Example:**
```javascript
// Headless hook providing dropdown logic
function useDropdown() {
  const [isOpen, setIsOpen] = useState(false);
  const [selectedItem, setSelectedItem] = useState(null);

  const toggle = () => setIsOpen(!isOpen);
  const close = () => setIsOpen(false);
  const selectItem = (item) => {
    setSelectedItem(item);
    close();
  };

  return {
    isOpen,
    selectedItem,
    toggle,
    close,
    selectItem,
    // Prop getters for accessibility
    getToggleProps: () => ({
      onClick: toggle,
      'aria-expanded': isOpen,
      'aria-haspopup': true,
    }),
    getMenuProps: () => ({
      hidden: !isOpen,
    }),
    getItemProps: (item) => ({
      onClick: () => selectItem(item),
      role: 'option',
    }),
  };
}

// Consumer provides all styling
function StyledDropdown({ items }) {
  const { selectedItem, getToggleProps, getMenuProps, getItemProps } = useDropdown();

  return (
    <div className="my-custom-dropdown">
      <button {...getToggleProps()}>
        {selectedItem || 'Select...'}
      </button>
      <ul {...getMenuProps()}>
        {items.map(item => (
          <li key={item} {...getItemProps(item)}>
            {item}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

### 8. Atomic Design Pattern
**Description:** Organizing components hierarchically as atoms (basic blocks), molecules, organisms, templates, and pages.

**When to Use:**
- Creating scalable design systems
- Larger applications requiring consistency
- Teams needing clear component organization

**Key Benefits:**
- Systematic thinking about UI components
- Clear organizational hierarchy
- Promotes reusability at all levels

**Hierarchy:**
- **Atoms**: Basic building blocks (Button, Input, Label)
- **Molecules**: Simple combinations of atoms (SearchField = Input + Button)
- **Organisms**: Complex components (Header = Logo + Nav + SearchField)
- **Templates**: Page layouts without content
- **Pages**: Specific instances of templates with real content

---

### 9. Portal Pattern
**Description:** Rendering children into DOM nodes outside the parent component's hierarchy using createPortal().

**When to Use:**
- Building modals, tooltips, dropdowns
- Escaping parent CSS constraints (overflow: hidden, z-index)
- Rendering to different DOM locations while maintaining React tree

**Key Benefits:**
- Solves z-index issues
- Escapes CSS overflow complications
- Maintains event bubbling in React tree

**Example:**
```javascript
import { createPortal } from 'react-dom';

function Modal({ children, isOpen }) {
  if (!isOpen) return null;

  return createPortal(
    <div className="modal-overlay">
      <div className="modal-content">
        {children}
      </div>
    </div>,
    document.body // Render outside parent DOM hierarchy
  );
}
```

---

### 9. Strategy Pattern

**Description:** Using polymorphism to replace scattered conditional logic. Extract variations into strategy classes that can be injected as dependencies.

**When to Use:**
- When you have conditional logic based on types, categories, or contexts scattered across multiple files
- Country-specific, user-role-specific, or feature-flag-based variations
- To eliminate "shotgun surgery" (one business change requires edits in many files)
- When different algorithms or behaviors need to be swapped at runtime

**Key Benefits:**
- Centralizes variation points
- Eliminates scattered conditionals
- Makes adding new variations easy (Open/Closed Principle)
- Improves testability

**Example:**
```javascript
// ❌ Scattered conditionals across multiple files
// In PaymentForm.jsx
function PaymentForm({ country, method }) {
  if (country === "US") {
    return <USPaymentForm method={method} />;
  } else if (country === "EU") {
    return <EUPaymentForm method={method} />;
  }
}

// In paymentValidation.js
function validatePayment(method, country) {
  if (country === "US") {
    // US validation rules
    return method.zipCode && method.zipCode.length === 5;
  } else if (country === "EU") {
    // EU validation rules
    return method.iban && method.iban.startsWith('EU');
  }
}

// In paymentProcessor.js
function processPayment(method, country) {
  if (country === "US") {
    // US processing
  } else if (country === "EU") {
    // EU processing
  }
}

// ✅ Strategy pattern - variations centralized
// Domain Layer - Strategy Interface & Implementations
class PaymentStrategy {
  validate(method) {
    throw new Error('Must implement validate');
  }

  process(method) {
    throw new Error('Must implement process');
  }

  getFormComponent() {
    throw new Error('Must implement getFormComponent');
  }
}

class USPaymentStrategy extends PaymentStrategy {
  validate(method) {
    return method.zipCode && method.zipCode.length === 5;
  }

  process(method) {
    // US-specific processing logic
    return usPaymentGateway.charge(method);
  }

  getFormComponent() {
    return USPaymentForm;
  }
}

class EUPaymentStrategy extends PaymentStrategy {
  validate(method) {
    return method.iban && method.iban.startsWith('EU');
  }

  process(method) {
    // EU-specific processing logic
    return euPaymentGateway.charge(method);
  }

  getFormComponent() {
    return EUPaymentForm;
  }
}

// Factory to create strategies
class PaymentStrategyFactory {
  static createStrategy(country) {
    switch (country) {
      case 'US': return new USPaymentStrategy();
      case 'EU': return new EUPaymentStrategy();
      default: throw new Error(`Unsupported country: ${country}`);
    }
  }
}

// Presentation Layer - Strategy injected
function PaymentForm({ country, method }) {
  const strategy = PaymentStrategyFactory.createStrategy(country);
  const FormComponent = strategy.getFormComponent();

  const handleSubmit = async () => {
    if (strategy.validate(method)) {
      await strategy.process(method);
    }
  };

  return <FormComponent method={method} onSubmit={handleSubmit} />;
}
```

**Adding New Variations:**
```javascript
// Adding a new country is simple - just create a new strategy
class AUPaymentStrategy extends PaymentStrategy {
  validate(method) {
    return method.bsb && method.accountNumber;
  }

  process(method) {
    return auPaymentGateway.charge(method);
  }

  getFormComponent() {
    return AUPaymentForm;
  }
}

// Update factory
class PaymentStrategyFactory {
  static createStrategy(country) {
    switch (country) {
      case 'US': return new USPaymentStrategy();
      case 'EU': return new EUPaymentStrategy();
      case 'AU': return new AUPaymentStrategy(); // New strategy
      default: throw new Error(`Unsupported country: ${country}`);
    }
  }
}
```

**Note:** The Strategy pattern works particularly well with dependency injection - strategies can be passed as props or provided through Context.

---

### 10. Anti-Corruption Layer Pattern

**Description:** Creating gateway functions or classes that mediate between external services and your domain models, isolating adaptation logic in a single location.

**When to Use:**
- Working with external APIs with structures different from your domain model
- Protecting your codebase from changes in third-party APIs
- When external API responses need significant transformation
- Integrating multiple external services with inconsistent interfaces

**Key Benefits:**
- Isolates external API changes to one location
- Prevents API structure from leaking into domain/presentation layers
- Makes switching external services easier
- Centralizes data transformation logic
- Improves testability (mock the gateway, not the API)

**Example:**
```javascript
// ❌ Direct API coupling throughout codebase
function UserProfile() {
  const { data } = useQuery('/api/user');

  // Components directly depend on API structure
  return (
    <div>
      <h1>{data.user_full_name}</h1>
      <p>{data.email_address}</p>
      <p>Member since: {new Date(data.registration_ts).getFullYear()}</p>
      {data.user_status === 'VIP' && <VIPBadge />}
    </div>
  );
}

function UserSettings() {
  const { data } = useQuery('/api/user');

  // Same API structure dependency duplicated
  return (
    <div>
      <label>{data.user_full_name}</label>
      <input defaultValue={data.email_address} />
    </div>
  );
}

// ✅ Anti-Corruption Layer (Gateway/Adapter)
// Domain Model
class User {
  constructor({ id, name, email, registeredAt, isVIP }) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.registeredAt = registeredAt;
    this.isVIP = isVIP;
  }

  get memberSince() {
    return this.registeredAt.getFullYear();
  }
}

// Data Access Layer - Gateway isolates API
class UserGateway {
  static async fetchUser() {
    const response = await fetch('/api/user');
    const data = await response.json();

    // Adaptation logic centralized here
    return new User({
      id: data.user_id,
      name: data.user_full_name,
      email: data.email_address,
      registeredAt: new Date(data.registration_ts),
      isVIP: data.user_status === 'VIP'
    });
  }

  static async updateUser(user) {
    // Adapt domain model back to API structure
    const apiPayload = {
      user_id: user.id,
      user_full_name: user.name,
      email_address: user.email,
      user_status: user.isVIP ? 'VIP' : 'REGULAR'
    };

    const response = await fetch('/api/user', {
      method: 'PUT',
      body: JSON.stringify(apiPayload)
    });

    return response.json();
  }
}

// Presentation Layer - Clean, decoupled from API
function UserProfile() {
  const { data: user } = useQuery('user', UserGateway.fetchUser);

  if (!user) return <Loading />;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <p>Member since: {user.memberSince}</p>
      {user.isVIP && <VIPBadge />}
    </div>
  );
}

function UserSettings() {
  const { data: user } = useQuery('user', UserGateway.fetchUser);

  if (!user) return <Loading />;

  return (
    <div>
      <label>{user.name}</label>
      <input defaultValue={user.email} />
    </div>
  );
}
```

**Multiple External Services Example:**
```javascript
// Different external services with different structures
class StripePaymentGateway {
  static async fetchPaymentMethods() {
    const response = await stripe.paymentMethods.list();

    // Adapt Stripe structure to domain model
    return response.data.map(pm => new PaymentMethod({
      id: pm.id,
      type: pm.type,
      lastFour: pm.card?.last4,
      expiryDate: pm.card ? new Date(pm.card.exp_year, pm.card.exp_month) : null
    }));
  }
}

class PayPalPaymentGateway {
  static async fetchPaymentMethods() {
    const response = await paypal.billing_agreements.list();

    // Adapt PayPal structure to same domain model
    return response.agreements.map(agreement => new PaymentMethod({
      id: agreement.id,
      type: 'paypal',
      lastFour: null,
      expiryDate: new Date(agreement.end_date)
    }));
  }
}

// Components work with domain model, not external APIs
function PaymentMethods({ provider }) {
  const gateway = provider === 'stripe' ? StripePaymentGateway : PayPalPaymentGateway;
  const { data: methods } = useQuery('payment-methods', gateway.fetchPaymentMethods);

  // Same rendering logic regardless of provider
  return methods.map(method => <PaymentMethodCard method={method} />);
}
```

**Testing Benefits:**
```javascript
// Easy to mock the gateway in tests
class MockUserGateway {
  static async fetchUser() {
    return new User({
      id: '123',
      name: 'Test User',
      email: 'test@example.com',
      registeredAt: new Date('2020-01-01'),
      isVIP: true
    });
  }
}

// Test uses mock gateway, no need to mock fetch/API
test('UserProfile displays user name', () => {
  render(<UserProfile userGateway={MockUserGateway} />);
  expect(screen.getByText('Test User')).toBeInTheDocument();
});
```

---

## Advanced Patterns

### 11. Render Props Pattern
**Description:** Passing functions as props that return JSX, enabling component logic sharing with customizable rendering.

**When to Use:**
- Sharing complex logic where UI output varies by consumer
- Before hooks existed (now custom hooks are preferred)
- When you need more control than Compound Components provide

**Key Benefits:**
- Flexible code sharing
- Consumer control over rendered output
- Works with class components

**Modern Alternative:** Custom hooks are now preferred for most use cases.

**Example:**
```javascript
// Component with render prop
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return render(position);
}

// Usage - consumer controls rendering
<MouseTracker render={({ x, y }) => (
  <div>Mouse at {x}, {y}</div>
)} />
```

---

### 12. Props Getters Pattern
**Description:** Providing functions that return pre-configured prop objects instead of passing individual props.

**When to Use:**
- Managing complex, related prop configurations
- Handling accessibility attributes
- Simplifying consumer API for complex components

**Key Benefits:**
- Simplified prop management
- Reduced consumer configuration burden
- Encapsulates related props together

**Example:**
```javascript
function useCheckbox(initialChecked = false) {
  const [checked, setChecked] = useState(initialChecked);

  // Props getter bundles related props
  const getCheckboxProps = () => ({
    type: 'checkbox',
    checked,
    onChange: (e) => setChecked(e.target.checked),
    role: 'checkbox',
    'aria-checked': checked,
  });

  return { checked, getCheckboxProps };
}

// Usage - consumer gets all props at once
function Checkbox() {
  const { getCheckboxProps } = useCheckbox();
  return <input {...getCheckboxProps()} />;
}
```

---

## Legacy Patterns

### 13. Higher Order Components (HOC)
**Description:** Wrapping components with functions that enhance them with additional props or logic.

**When to Use:**
- Legacy codebases
- Class components
- **Modern approach:** Use custom hooks instead

**Key Benefits (Historical):**
- Code reuse in pre-hooks React

**Why Avoid:**
- Custom hooks offer better readability
- Hooks are easier to test
- HOCs can cause "wrapper hell"
- Naming collisions with props

**Example (for reference only):**
```javascript
// Legacy HOC pattern
function withAuth(Component) {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();

    if (loading) return <Spinner />;
    if (!user) return <Redirect to="/login" />;

    return <Component {...props} user={user} />;
  };
}

// Modern equivalent with hook
function useRequireAuth() {
  const { user, loading } = useAuth();

  if (!user && !loading) {
    // Handle redirect
  }

  return { user, loading };
}
```

---

## General Principles

### 14. DRY Principle (Don't Repeat Yourself)
**Description:** Eliminating code duplication by extracting common logic into reusable functions or components.

**When to Use:**
- When code is duplicated across multiple locations
- **Caution:** Don't abstract too early—wait for clear patterns

**Key Benefits:**
- Reduced maintenance burden
- Improved code consistency
- Single source of truth

**When NOT to use:**
- Premature abstraction can make code harder to understand
- Sometimes duplication is better than the wrong abstraction

---

### 15. SOLID Principles in React

#### Single Responsibility Principle (SRP)
- Components and hooks should handle one clear responsibility
- If a component does too much, split it

#### Open/Closed Principle (OCP)
- Components should be extendable through props without modification
- Use composition and configuration

#### Liskov Substitution Principle (LSP)
- Similar components should maintain compatible interfaces
- Consumers should be able to swap implementations

#### Interface Segregation Principle (ISP)
- Components should require only necessary props
- Avoid bloated prop interfaces
- Use props spreading carefully

#### Dependency Inversion Principle (DIP)
- High-level components should depend on abstractions
- Don't hard-code dependencies
- Use dependency injection

---

### 16. Dependency Injection
**Description:** Providing dependencies to components/hooks rather than having them create dependencies internally.

**When to Use:**
- Testing (inject mocks)
- Flexibility (swap implementations)
- Inversion of control

**Implementation Methods:**
- Props
- Context API
- Custom hooks

**Example:**
```javascript
// Without DI - hard to test
function UserProfile() {
  const user = fetchUser(); // Hard-coded dependency
  return <div>{user.name}</div>;
}

// With DI - easy to test
function UserProfile({ userService }) {
  const user = userService.getUser();
  return <div>{user.name}</div>;
}
```

---

### 17. Separation of Concerns (SoC)
**Description:** Organizing code into layers handling distinct concerns: data, business logic, and presentation.

**When to Use:**
- Non-trivial applications requiring scalability
- Team collaboration
- Long-term maintainability

**Layers:**
1. **Data Layer**: API calls, data fetching
2. **Business Logic Layer**: Data transformation, validation, calculations
3. **Presentation Layer**: Components, UI rendering

**Key Benefits:**
- Easier testing
- Independent updates to different concerns
- Better maintainability

---

### 18. MVVM (Model-View-ViewModel)
**Description:** Separating models, views, and view models to organize larger React applications.

**When to Use:**
- Larger applications needing clear architectural separation
- Optional—not a React standard pattern

**Structure:**
- **Model**: Data structures and business logic
- **View**: React components (presentation)
- **ViewModel**: State management and presentation logic (hooks, state)

---

### 19. Stable Dependency Principle (SDP)
**Description:** Building upon stable, well-tested dependencies rather than volatile, frequently-changing ones.

**When to Use:**
- Selecting third-party dependencies
- Avoiding private or experimental APIs
- Choosing between libraries

**Key Benefits:**
- Prevents cascading failures
- Reduces breaking changes
- More reliable applications

---

### 20. KISS Principle (Keep It Simple, Stupid)
**Description:** Prioritizing simplicity in design and implementation over complex solutions.

**When to Use:**
- Always—default to simple solutions
- Avoid over-engineering
- Don't use patterns for the sake of patterns

**Key Benefits:**
- Improved maintainability
- Reduced cognitive load
- Faster development

**Remember:**
- Simple code is not simplistic code
- Complexity should be justified by real requirements
- "Premature optimization is the root of all evil"

---

## Anti-Patterns to Avoid

### 21. Clean Architecture (in React)
**Description:** Backend architectural pattern sometimes inappropriately applied to React.

**Why Avoid:**
- Introduces unnecessary complexity for frontend
- React already has established patterns
- Over-engineering for typical React apps

**Recommendation:**
- Follow React-specific patterns instead
- Use Separation of Concerns, but don't force backend patterns

---

### 22. Prop Drilling
**Not a pattern to use—a problem to solve**

**Problem:** Passing props through multiple levels of components that don't use them.

**Solutions:**
- Context API (for configuration)
- State management libraries (for frequently-changing state)
- Component composition
- Lifting state up (for localized sharing)

---

## Modern & Emerging Patterns (React 18+)

### Server Components
- Render components on the server
- Reduce client bundle size
- Better initial load performance
- Used in Next.js App Router, Remix

### Suspense for Data Fetching
- Declarative loading states
- Coordinate multiple async operations
- Better UX for async boundaries

### Concurrent Features
- useTransition for non-urgent updates
- useDeferredValue for responsive UI
- Automatic batching

**Note:** These patterns are evolving and framework-specific. Refer to React documentation and framework guides for up-to-date information.

---

## Pattern Selection Guide

When reviewing code, consider:

1. **Start Simple**: Use basic patterns first (composition, custom hooks)
2. **Add Complexity When Needed**: More advanced patterns solve specific problems
3. **Context Matters**: Choose patterns based on:
   - Team size and experience
   - Application scale
   - Performance requirements
   - Maintenance considerations

4. **Common Pattern Progression**:
   - Small apps: Composition + custom hooks + local state
   - Medium apps: + Context API + container/presentational
   - Large apps: + State management library + compound components + design system

5. **Avoid Pattern Overuse**:
   - Don't use patterns because they exist
   - Don't prematurely optimize
   - Keep it simple until complexity is justified
