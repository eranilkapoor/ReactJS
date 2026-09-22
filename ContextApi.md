The **Context API** in React provides a way to share values between components without passing props explicitly through every level of the component tree. It is particularly useful for managing global or shared state, such as themes, authentication, or language preferences.

### Key Components of Context API
1. **React.createContext()**:
   - Creates a Context object.
   - Returns a Provider and a Consumer.

2. **Provider**:
   - Wraps a part of the component tree.
   - Supplies the context value to its children.

3. **Consumer** (or `useContext` Hook):
   - Consumes the context value in components.

---

### Example: Theme Context
Let's implement a simple example where a theme (light or dark) is shared across components using the Context API.

#### 1. Create the Context
```javascript
import React, { createContext, useState } from "react";

// Create a Context
export const ThemeContext = createContext();

// Create a Provider Component
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

---

#### 2. Use the Context in Components
```javascript
import React, { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

const Header = () => {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <header
      style={{
        backgroundColor: theme === "light" ? "#f9f9f9" : "#333",
        color: theme === "light" ? "#000" : "#fff",
        padding: "1rem",
        textAlign: "center",
      }}
    >
      <h1>{theme.toUpperCase()} THEME</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === "light" ? "Dark" : "Light"} Mode
      </button>
    </header>
  );
};
```

---

#### 3. Wrap the App with Provider
```javascript
import React from "react";
import { ThemeProvider } from "./ThemeContext";
import Header from "./Header";

const App = () => {
  return (
    <ThemeProvider>
      <Header />
    </ThemeProvider>
  );
};

export default App;
```

---

### How It Works:
1. **Provider**:
   - The `ThemeProvider` wraps the `App` component, supplying `theme` and `toggleTheme` to all components inside it.

2. **Consumer**:
   - The `Header` component uses the `useContext` hook to consume the `theme` and `toggleTheme` values.

3. **Dynamic Updates**:
   - When `toggleTheme` is called, the state in the `ThemeProvider` updates, and the UI re-renders with the new theme.

---

### Advantages of Context API
- Avoids prop drilling.
- Simplifies state management for small to medium applications.
- Works well with functional components and hooks.

### Limitations
- May lead to performance issues if not optimized (e.g., excessive re-renders).
- Less suitable for very large-scale applications compared to state management libraries like Redux.

---

### **Best Practices**
- Split contexts by concern (e.g., `ThemeContext`, `AuthContext`) instead of one giant context, so unrelated consumers don't re-render.
- Memoize the value passed to `Provider` with `useMemo` to avoid recreating a new object on every render.
- Wrap `useContext` in a custom hook (e.g., `useTheme`) that throws a clear error if used outside its `Provider`.
- Only put values in context that genuinely need to be shared widely; keep local state local.
- Reach for Context for low-frequency, broadly-shared values (theme, auth, locale) rather than fast-changing state.
- Always provide a sensible default value in `createContext()` to simplify testing and avoid `undefined` checks everywhere.

---

### **Interview Questions**

**Q1. What problem does the Context API solve?**
It solves "prop drilling" — the need to manually pass props through many intermediate components that don't use the data themselves, just to get it to a deeply nested child.

**Q2. What does `React.createContext()` return?**
It returns a Context object with `Provider` and `Consumer` components attached to it, which are used to supply and read the context value respectively.
```javascript
const ThemeContext = createContext();
```

**Q3. What is the difference between `Provider` and `Consumer`?**
`Provider` wraps part of the component tree and supplies the current context value to it. `Consumer` (or the `useContext` hook) is used inside descendant components to read that value.

**Q4. How does `useContext` simplify consuming context compared to `Context.Consumer`?**
`useContext` lets a function component read a context value directly as a variable, avoiding the render-prop/nested-callback syntax required by `<Context.Consumer>{value => ...}</Context.Consumer>`.

**Q5. If a Provider's value changes, do all consuming components re-render?**
Yes — every component that calls `useContext` on that context re-renders whenever the Provider's `value` reference changes, even if the specific field it reads didn't change, unless the tree is optimized with memoization.

**Q6. How would you avoid unnecessary re-renders caused by Context updates?**
Split large contexts into smaller, more focused ones, memoize the value object passed to `Provider` with `useMemo`, and wrap expensive consumers in `React.memo`.

**Q7. Can a component consume multiple contexts at once?**
Yes, by calling `useContext` multiple times, once for each context, or by nesting multiple `Provider`s around the tree.

**Q8. How does Context API differ from Redux?**
Context API is a built-in mechanism for passing values down the tree without extra libraries, with no built-in dev tools, middleware, or update-batching optimizations. Redux is a full state-management library with a single store, middleware support, and tooling, better suited for complex, frequently-updated, cross-cutting state.

**Q9. What is the purpose of the default value passed to `createContext()`?**
It's the value used when a component consumes the context without a matching `Provider` above it in the tree, which is useful for standalone testing or providing a safe fallback.

**Q10. How would you implement a global theme toggle using Context API?**
Create a `ThemeContext` with `createContext`, build a `ThemeProvider` that holds `theme` state and a `toggleTheme` function in `value`, wrap the app with it, and call `useContext(ThemeContext)` in any component that needs to read or toggle the theme.

**Q11. Is the Context API a replacement for state management libraries like Redux?**
Not entirely — it's a dependency-injection mechanism for sharing values, not a state-management solution with features like middleware, time-travel debugging, or optimized selective updates. It complements local state and is often enough for small-to-medium apps.

**Q12. What is "provider hell" and how can it be mitigated?**
It's the nesting of many `Provider` components at the root of an app (theme, auth, locale, etc.), making the tree hard to read. It can be mitigated by writing a small helper component that composes all providers together into a single wrapper.