**Error Boundaries** are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of letting the entire application crash to a blank white screen. Before error boundaries existed, a single rendering error in any component could unmount the whole React tree, taking down the entire app with it. Understanding error boundaries is essential for building production-grade React applications that fail gracefully.

---

### **What Is an Error Boundary?**

An **error boundary** is a component that catches JavaScript errors that occur:
1. **During rendering** — an error thrown while a component's JSX is being evaluated.
2. **In lifecycle methods** — errors thrown inside `componentDidMount`, `componentDidUpdate`, etc. of any child.
3. **In constructors** — errors thrown while instantiating a child class component.

When an error is caught, the error boundary can render a fallback UI (e.g., "Something went wrong") instead of the broken component tree, preventing the rest of the application from crashing.

```jsx
// Without an error boundary, this error unmounts the ENTIRE app:
function BuggyComponent() {
  const user = null;
  return <p>{user.name}</p>; // Throws: Cannot read properties of null
}
```

With an error boundary wrapping `BuggyComponent`, only that part of the UI is replaced by a fallback — the rest of the page keeps working.

---

### **Why Error Boundaries Must Be Class Components**

As of React 18, **there is no hook equivalent for error boundaries**. You cannot write an error boundary as a functional component using hooks, because the underlying mechanism relies on two class-only lifecycle APIs:

- `static getDerivedStateFromError(error)`
- `componentDidCatch(error, errorInfo)`

Neither of these has a hooks-based counterpart (there is no `useError` or `useDerivedStateFromError` hook). This is one of the few remaining cases in modern React where a class component is genuinely required rather than just a stylistic choice.

#### **The Practical Workaround**

Most teams don't hand-write error boundary classes repeatedly. Instead they either:
1. Write **one** reusable class-based `ErrorBoundary` component and reuse it everywhere.
2. Use the popular **`react-error-boundary`** npm package, which wraps the class-component machinery internally and exposes a hook-friendly, functional API (covered later in this file).

---

### **Implementing an Error Boundary**

#### **`static getDerivedStateFromError(error)`**
Called during the "render" phase after a descendant throws. It receives the error and must return a new state value used to render the fallback UI on the next render. It should be a pure function with no side effects (no logging, no API calls here).

#### **`componentDidCatch(error, errorInfo)`**
Called during the "commit" phase, after the fallback UI has already been rendered. This is where you perform side effects, such as logging the error to a monitoring service like Sentry or LogRocket. `errorInfo.componentStack` gives you the component tree where the error occurred.

#### **Full Worked Example**

```jsx
import React from "react";

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  // Update state so the next render shows the fallback UI.
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  // Log the error to an external service.
  componentDidCatch(error, errorInfo) {
    console.error("ErrorBoundary caught an error:", error, errorInfo.componentStack);
    // logErrorToService(error, errorInfo);
  }

  handleReset = () => {
    this.setState({ hasError: false, error: null });
  };

  render() {
    if (this.state.hasError) {
      return (
        <div role="alert">
          <h2>Something went wrong.</h2>
          <p>{this.state.error?.message}</p>
          <button onClick={this.handleReset}>Try Again</button>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

#### **A Component That Throws**

```jsx
function BuggyCounter({ count }) {
  if (count === 5) {
    throw new Error("Counter crashed when it hit 5!");
  }
  return <p>Count: {count}</p>;
}
```

#### **Wrapping It**

```jsx
import React, { useState } from "react";
import ErrorBoundary from "./ErrorBoundary";
import BuggyCounter from "./BuggyCounter";

function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
      <ErrorBoundary>
        <BuggyCounter count={count} />
      </ErrorBoundary>
    </div>
  );
}

export default App;
```

When `count` reaches `5`, `BuggyCounter` throws. Instead of the whole `App` crashing, only the `ErrorBoundary`'s fallback UI is shown, and the "Increment" button outside the boundary keeps working.

---

### **What Error Boundaries Do NOT Catch**

Error boundaries only catch errors during React's own rendering, lifecycle, and constructor phases. They deliberately do **not** catch:

1. **Errors in event handlers** — e.g., a `throw` inside an `onClick` handler. These are regular JavaScript execution, not part of React's render cycle.
2. **Asynchronous code** — errors inside `setTimeout`, `setInterval`, `requestAnimationFrame`, or unhandled promise rejections. By the time the async callback runs, React has already finished rendering.
3. **Server-side rendering (SSR) errors** — error boundaries only work on the client during React's render lifecycle in the browser.
4. **Errors thrown in the error boundary itself** — an error boundary cannot catch errors it throws in its own render method; you need a parent error boundary above it for that.

#### **Handling These Cases with try/catch**

```jsx
function DangerousButton() {
  const handleClick = () => {
    try {
      riskyOperation(); // may throw
    } catch (error) {
      console.error("Caught in event handler:", error);
      // show a toast, set local error state, etc.
    }
  };

  return <button onClick={handleClick}>Do Something Risky</button>;
}
```

```jsx
function AsyncDataLoader() {
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch("/api/data")
      .then((res) => res.json())
      .catch((err) => setError(err)); // caught manually, not by an error boundary
  }, []);

  if (error) return <p>Failed to load data: {error.message}</p>;
  return <p>Data loaded successfully.</p>;
}
```

| Error Source | Caught by Error Boundary? | How to Handle |
|---|---|---|
| Rendering (JSX evaluation) | Yes | Error boundary |
| Lifecycle methods / effects setup | Yes (class lifecycle only) | Error boundary |
| Constructors | Yes | Error boundary |
| Event handlers (`onClick`, `onChange`, etc.) | No | `try`/`catch` in the handler |
| `setTimeout`/`setInterval`/promises | No | `try`/`catch` or `.catch()` |
| Server-side rendering | No | Server-side error handling |
| The error boundary's own render | No | A parent error boundary |

---

### **Granularity: App-Level vs Widget-Level Boundaries**

You can place error boundaries at different levels of your component tree, and the choice significantly affects user experience.

#### **Wrapping the Whole App**
A single top-level boundary is the simplest safety net, but if *any* component crashes, the user loses the entire UI.

```jsx
function App() {
  return (
    <ErrorBoundary>
      <Header />
      <MainContent />
      <Footer />
    </ErrorBoundary>
  );
}
```

#### **Wrapping Individual Widgets/Routes**
A more resilient strategy wraps independent sections separately, so a crash in one widget doesn't take down the rest of the page.

```jsx
function Dashboard() {
  return (
    <div className="dashboard">
      <ErrorBoundary>
        <UserProfileWidget />
      </ErrorBoundary>
      <ErrorBoundary>
        <RecentActivityWidget />
      </ErrorBoundary>
      <ErrorBoundary>
        <RecommendationsWidget />
      </ErrorBoundary>
    </div>
  );
}
```

If `RecommendationsWidget` crashes, `UserProfileWidget` and `RecentActivityWidget` keep rendering normally, each with its own fallback UI shown only for the widget that failed.

#### **Common Strategy**
Most production apps use a **layered approach**: one top-level boundary as a last-resort catch-all (often paired with a "reload the page" fallback), plus additional boundaries around risky or independent sections like routes, third-party widgets, or data-heavy panels.

---

### **`react-error-boundary`: A Modern, Hook-Friendly Alternative**

The **`react-error-boundary`** package (by Kent C. Dodds) wraps the class-based machinery internally so you can work with error boundaries declaratively, without writing a class yourself.

#### **Install**
```bash
npm install react-error-boundary
```

#### **Basic Usage**

```jsx
import React from "react";
import { ErrorBoundary } from "react-error-boundary";

function Fallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try Again</button>
    </div>
  );
}

function BuggyComponent({ shouldThrow }) {
  if (shouldThrow) {
    throw new Error("Simulated crash!");
  }
  return <p>Everything is fine.</p>;
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={Fallback}
      onReset={() => {
        // reset any state that caused the error, e.g. refetch data
      }}
      onError={(error, errorInfo) => {
        // log to a monitoring service
        console.error(error, errorInfo);
      }}
    >
      <BuggyComponent shouldThrow={false} />
    </ErrorBoundary>
  );
}

export default App;
```

`resetErrorBoundary` lets the fallback UI reset the boundary's state and attempt to re-render the children — useful for "Try Again" buttons. The package also exports a `useErrorBoundary` hook for imperatively triggering the nearest boundary from event handlers or async code, bridging the gap for errors that a plain error boundary would otherwise miss.

```jsx
import { useErrorBoundary } from "react-error-boundary";

function DataLoader() {
  const { showBoundary } = useErrorBoundary();

  useEffect(() => {
    fetchData().catch((error) => showBoundary(error)); // sends async error to the boundary
  }, [showBoundary]);

  return <p>Loading...</p>;
}
```

---

### **Best Practices**
- Place at least one top-level error boundary around your entire app as a last-resort safety net.
- Add additional boundaries around independent widgets, routes, or third-party integrations so isolated failures don't crash the whole page.
- Always log caught errors (`componentDidCatch` or `onError`) to a monitoring service in production — don't let errors disappear silently.
- Provide a way to recover, such as a "Try Again" or "Reload" button, rather than leaving users stuck on a dead-end fallback.
- Remember error boundaries don't catch event handler or async errors — pair them with `try`/`catch` for those cases.
- Prefer `react-error-boundary` over hand-rolled classes in new projects for its hook-friendly API and built-in reset semantics.
- Keep fallback UIs simple and dependency-free — if the fallback itself throws, there's nothing left to catch it except a parent boundary.

---

### **Interview Questions**

**Q1. What is an error boundary in React?**
An error boundary is a class component that catches JavaScript errors occurring during rendering, in lifecycle methods, and in constructors of its child component tree, and displays a fallback UI instead of letting the error crash the whole application.

**Q2. Why must error boundaries be class components?**
Because the two APIs that implement error boundary behavior — `static getDerivedStateFromError` and `componentDidCatch` — are class-only lifecycle methods with no hook equivalent as of React 18. There is no `useErrorBoundary`-style hook built into React itself.

**Q3. What is the difference between `getDerivedStateFromError` and `componentDidCatch`?**
`getDerivedStateFromError` runs during the render phase and returns new state to render the fallback UI; it must be pure, with no side effects. `componentDidCatch` runs during the commit phase, after the fallback has rendered, and is where you perform side effects like logging the error to a monitoring service.

**Q4. What types of errors do error boundaries NOT catch?**
They do not catch errors in event handlers, errors in asynchronous code (`setTimeout`, promises), server-side rendering errors, or errors thrown inside the error boundary's own render method.

**Q5. How do you handle an error thrown inside an `onClick` handler if error boundaries can't catch it?**
Wrap the risky code in a regular `try`/`catch` block inside the handler itself, and manage the error with local component state (e.g., showing a message or toast) instead of relying on a boundary.
```jsx
const handleClick = () => {
  try {
    doRiskyThing();
  } catch (err) {
    setError(err);
  }
};
```

**Q6. What is `react-error-boundary` and why would you use it?**
It's a third-party package that wraps the class-based error boundary machinery in a declarative, hook-friendly API (`<ErrorBoundary FallbackComponent={...} />`), plus a `useErrorBoundary` hook for manually triggering the nearest boundary from event handlers or async code — covering cases plain error boundaries miss.

**Q7. Should you wrap your entire app in a single error boundary, or use multiple?**
Ideally both: one top-level boundary as a last-resort catch-all, plus additional boundaries around independent widgets, routes, or risky third-party components, so a failure in one part of the UI doesn't take down the whole page.

**Q8. What does `resetErrorBoundary` do in `react-error-boundary`?**
It resets the boundary's internal error state, causing it to attempt to re-render its children (as if the error never happened). It's typically wired to a "Try Again" button in the fallback UI, and can be paired with `onReset` to also reset the state that caused the crash.

**Q9. Can an error boundary catch an error thrown by itself?**
No. An error boundary cannot catch an error thrown inside its own `render` method or its own lifecycle methods. To catch that, you need to wrap it in a parent error boundary.

**Q10. How would you log errors caught by an error boundary to a service like Sentry?**
Call the logging function inside `componentDidCatch(error, errorInfo)` (or the `onError` prop of `react-error-boundary`), since that's the commit-phase method meant for side effects, passing along `error` and `errorInfo.componentStack` for context.

**Q11. What happens to component state below an error boundary after it catches an error?**
The entire subtree that threw is unmounted and replaced by the fallback UI, so any local state in the crashed components is lost. Only when the boundary is reset (e.g., via `resetErrorBoundary`) does React attempt to remount and re-render the children fresh.

**Q12. Do error boundaries work during server-side rendering?**
No. Error boundaries only catch errors during the client-side render lifecycle in the browser. Errors thrown while rendering on the server need to be handled separately, typically with server-side `try`/`catch` around the rendering call.
