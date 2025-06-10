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

---

### Best Practices
- Keep dependencies in `useEffect` accurate to avoid unnecessary re-renders.
- Use custom hooks to encapsulate reusable logic.
- Avoid overusing hooks in a single component; split functionality into smaller components or hooks.

Hooks are a powerful addition to React, making functional components equally capable as class components while simplifying code and enhancing reusability.