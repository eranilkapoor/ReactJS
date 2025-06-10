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