**Hooks** in React are functions that let you use state and other React features in functional components, which previously lacked the ability to manage state or use lifecycle methods. Introduced in React 16.8, hooks allow you to write cleaner and more reusable code.

---

### Commonly Used Hooks
1. **useState**: Manage state in a functional component.
2. **useEffect**: Perform side effects (e.g., data fetching, subscriptions).
3. **useContext**: Access context values without wrapping components in `Context.Consumer`.
4. **useRef**: Access and interact with DOM elements or retain a mutable value across renders.
5. **useReducer**: Manage complex state logic.
6. **Custom Hooks**: Create reusable logic for specific needs.

---

### 1. `useState` Hook
The `useState` hook adds state to a functional component.

#### Example:
```javascript
import React, { useState } from "react";

const Counter = () => {
  const [count, setCount] = useState(0); // Initialize state

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
};

export default Counter;
```

---

### 2. `useEffect` Hook
The `useEffect` hook performs side effects, such as fetching data or updating the DOM.

#### Example:
```javascript
import React, { useState, useEffect } from "react";

const DataFetcher = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/posts")
      .then((response) => response.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      });
  }, []); // Empty dependency array ensures the effect runs once after the component mounts.

  if (loading) return <p>Loading...</p>;

  return (
    <ul>
      {data.slice(0, 5).map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default DataFetcher;
```

---

### 3. `useContext` Hook
The `useContext` hook simplifies accessing context values.

#### Example:
```javascript
import React, { createContext, useContext } from "react";

const ThemeContext = createContext("light");

const ThemeComponent = () => {
  const theme = useContext(ThemeContext); // Access context value
  return <p>Current Theme: {theme}</p>;
};

const App = () => {
  return (
    <ThemeContext.Provider value="dark">
      <ThemeComponent />
    </ThemeContext.Provider>
  );
};

export default App;
```

---

### 4. `useRef` Hook
The `useRef` hook provides a way to access a DOM element or retain mutable values that persist across renders.

#### Example:
```javascript
import React, { useRef } from "react";

const InputFocus = () => {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Focus me" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
};

export default InputFocus;
```

---

### 5. `useReducer` Hook
The `useReducer` hook is an alternative to `useState` for managing more complex state logic.

#### Example:
```javascript
import React, { useReducer } from "react";

const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      return state;
  }
};

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <h1>Count: {state.count}</h1>
      <button onClick={() => dispatch({ type: "increment" })}>Increment</button>
      <button onClick={() => dispatch({ type: "decrement" })}>Decrement</button>
    </div>
  );
};

export default Counter;
```

---

### 6. Custom Hooks
Custom hooks allow you to extract and reuse component logic.

#### Example:
```javascript
import React, { useState, useEffect } from "react";

const useFetch = (url) => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then((response) => response.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      });
  }, [url]);

  return { data, loading };
};

const CustomHookExample = () => {
  const { data, loading } = useFetch("https://jsonplaceholder.typicode.com/posts");

  if (loading) return <p>Loading...</p>;

  return (
    <ul>
      {data.slice(0, 5).map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default CustomHookExample;
```

---

### Advantages of Hooks
1. **Cleaner Code**: Reduce boilerplate by using functional components.
2. **Reusable Logic**: Custom hooks allow code reuse.
3. **Simplified State Management**: Manage state and lifecycle in functional components.
4. **No More Class Components**: Avoid complexities of `this` keyword.

Hooks are a powerful addition to React, making functional components equally capable as class components while simplifying code and enhancing reusability.

---

### **Best Practices**
- Keep dependencies in `useEffect` accurate to avoid unnecessary re-renders or stale-closure bugs.
- Use custom hooks to encapsulate reusable logic instead of duplicating it across components.
- Avoid overusing hooks in a single component; split functionality into smaller components or hooks.
- Only call hooks at the top level of a component or custom hook — never inside loops, conditions, or nested functions.
- Prefer multiple `useState` calls (or `useReducer`) for unrelated pieces of state instead of one large combined state object.
- Always return a cleanup function from `useEffect` when setting up subscriptions, timers, or event listeners to prevent memory leaks.
- Name custom hooks starting with `use` so React's linter rules and other developers can recognize and validate them correctly.

---

### **Interview Questions**

**Q1. What problem do React Hooks solve, and when were they introduced?**
Hooks, introduced in React 16.8, let functional components use state, context, and lifecycle-like behavior that were previously only available in class components. They remove the need for `this`, class boilerplate, and make it easier to share stateful logic between components via custom hooks.

**Q2. What does `useState` return, and how do you use it?**
`useState` returns an array with two elements: the current state value and a setter function to update it. It is commonly consumed with array destructuring.
```javascript
const [count, setCount] = useState(0);
```

**Q3. Why doesn't the state variable from `useState` update immediately after calling its setter?**
State updates in React are asynchronous and scheduled for the next render; the component function doesn't re-run synchronously when the setter is called. The old `count` value inside the current render's closure stays the same until the component re-renders with the new state.

**Q4. What is the purpose of the dependency array in `useEffect`?**
The dependency array tells React when to re-run the effect. React compares each value in the array to its previous render, and only re-runs the effect if at least one value changed. An empty array `[]` means "run once, after mount."

**Q5. What happens if you omit the dependency array in `useEffect` entirely?**
Without a dependency array, the effect runs after every single render of the component, which can lead to performance issues or infinite loops if the effect itself triggers a state update.

**Q6. How do you clean up a side effect created inside `useEffect`?**
Return a function from the effect callback; React calls this returned function before the effect runs again and when the component unmounts, making it the equivalent of `componentWillUnmount`.
```javascript
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

**Q7. What problem does `useContext` solve compared to passing props through many levels?**
`useContext` lets a component read a value from the nearest matching `Context.Provider` directly, avoiding "prop drilling" where data must be manually passed through every intermediate component that doesn't actually need it.

**Q8. What is `useRef` used for, and how does it differ from `useState`?**
`useRef` returns a mutable object (`{ current: value }`) that persists across renders without causing a re-render when it changes. Unlike `useState`, updating a ref's `.current` value does not trigger the component to re-render, making it ideal for accessing DOM nodes or storing values that shouldn't affect rendering.

**Q9. When would you choose `useReducer` over `useState`?**
`useReducer` is preferable when state logic is complex, involves multiple sub-values, or when the next state depends on the previous state in non-trivial ways (e.g., multiple related actions). It centralizes update logic in a single reducer function, similar to Redux.

**Q10. What are the two main rules of hooks?**
Hooks must only be called at the top level of a function component or custom hook (never inside loops, conditions, or nested functions), and hooks must only be called from React function components or other custom hooks, not regular JavaScript functions.

**Q11. What is a custom hook, and what naming convention must it follow?**
A custom hook is a JavaScript function that calls one or more built-in hooks to encapsulate and reuse stateful logic across components. By convention, its name must start with `use` (e.g., `useFetch`) so React's linter can verify the rules of hooks are followed.

**Q12. How would a custom hook like `useFetch` be implemented, and why is it useful?**
It wraps `useState` and `useEffect` to manage loading and data state for a given URL, returning `{ data, loading }` to any component that calls it. This avoids duplicating the same fetch-and-track-loading logic in every component that needs remote data.