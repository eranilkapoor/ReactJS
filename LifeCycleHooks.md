# LifeCycle Hooks :-

* What does mounting mean?
* Since we're defining virtual representations of nodes in our DOM tree with React, we're not actually defining DOM nodes. Instead, we're building up an in-memory view that React maintains and manages for us.
* When we talk about mounting, we're talking about the process of converting the virtual components into actual DOM elements that are placed in the DOM by React.

## Four phases of a React component
* The React component, like anything else in the world, goes through the following phases
    - Initialization
    - Mounting (Birth of your component)
    - Update (Growth of your component)
    - Unmounting (Death of your component)

* The methods of ReactJS lifecycle
    - Initialization (setup props and state)
    - Mounting (componentWillMount, render, componentDidMount)
    - Updation 
        -- props (componentWillReceiveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate)
        -- states (shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate)
    - Unmounting (componentWillUnmount)


### **Life Cycle Hooks in React**

In React, **life cycle methods** (also called **life cycle hooks**) refer to the series of methods that are called at different stages during a component's life cycle. A component's life cycle includes creation, updating, and removal from the DOM. Understanding these hooks allows you to execute code at specific points during the component's life.

In React, the life cycle methods are primarily used in **class components**, but React introduced **hooks** (e.g., `useEffect`) in **functional components** starting from React 16.8, which serve similar purposes.

Let's break down the life cycle hooks into different phases and how they apply to both **class components** and **functional components**.

---

### **Life Cycle in Class Components**

In class components, the life cycle methods are automatically invoked at certain stages. They are divided into **Mounting**, **Updating**, and **Unmounting** phases.

#### **1. Mounting Phase**

This phase covers the life cycle from when the component is created and inserted into the DOM.

- **`constructor(props)`**:
  - Called when the component is created. It is used to initialize state and bind event handlers.
  - It is the first method called during the creation of a component.
  
  ```jsx
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  ```

- **`static getDerivedStateFromProps(nextProps, nextState)`**:
  - Called before every render (both initial and subsequent). It is used to update state based on changes in props.
  - It does not have access to the component instance (`this`), and it must return an object or `null`.

  ```jsx
  static getDerivedStateFromProps(nextProps, nextState) {
    if (nextProps.value !== nextState.value) {
      return { value: nextProps.value };
    }
    return null;
  }
  ```

- **`render()`**:
  - The only required method in class components. It returns the JSX that defines the component's UI.
  - It is called every time the component needs to re-render.
  
  ```jsx
  render() {
    return <div>{this.state.count}</div>;
  }
  ```

- **`componentDidMount()`**:
  - Called immediately after the component is mounted to the DOM (i.e., after the initial render).
  - This is commonly used for making AJAX requests or setting up subscriptions.

  ```jsx
  componentDidMount() {
    console.log('Component mounted');
  }
  ```

#### **2. Updating Phase**

This phase occurs when the component's state or props change, causing the component to re-render.

- **`static getDerivedStateFromProps(nextProps, nextState)`**:
  - As described earlier, it is also invoked during the update phase, before the render method.

- **`shouldComponentUpdate(nextProps, nextState)`**:
  - Allows you to control whether the component should re-render. If it returns `false`, the re-render is skipped.
  - This is useful for performance optimization.

  ```jsx
  shouldComponentUpdate(nextProps, nextState) {
    return nextProps.value !== this.props.value;
  }
  ```

- **`render()`**:
  - Called every time the component needs to re-render (whenever state or props change).
  
- **`getSnapshotBeforeUpdate(prevProps, prevState)`**:
  - Called right before the changes from the virtual DOM are reflected in the actual DOM.
  - It is used to capture information (like scroll position) from the DOM before the update.

  ```jsx
  getSnapshotBeforeUpdate(prevProps, prevState) {
    return null;  // Or capture snapshot information
  }
  ```

- **`componentDidUpdate(prevProps, prevState, snapshot)`**:
  - Called after the component has updated (i.e., after the render method is finished).
  - It is useful for handling side effects based on prop or state changes.

  ```jsx
  componentDidUpdate(prevProps, prevState) {
    if (this.props.value !== prevProps.value) {
      console.log('Prop value has changed');
    }
  }
  ```

#### **3. Unmounting Phase**

This phase occurs when the component is removed from the DOM.

- **`componentWillUnmount()`**:
  - Called just before the component is unmounted and destroyed.
  - It is commonly used for cleanup tasks such as invalidating timers, canceling network requests, or removing event listeners.

  ```jsx
  componentWillUnmount() {
    console.log('Component will unmount');
  }
  ```

---

### **Life Cycle in Functional Components**

With the introduction of **React Hooks** in version 16.8, functional components can also manage side effects and mimic class component life cycle methods. The `useEffect` hook is commonly used to handle component life cycle in functional components.

#### **`useEffect` Hook**

The `useEffect` hook allows you to perform side effects in functional components. It can be used for tasks like fetching data, setting up subscriptions, or manually changing the DOM.

The `useEffect` hook combines the functionality of `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` in one API.

- **Basic `useEffect` Usage:**

  ```jsx
  import { useState, useEffect } from 'react';

  function Example() {
    const [count, setCount] = useState(0);

    // Similar to componentDidMount and componentDidUpdate:
    useEffect(() => {
      console.log('Component rendered or count changed');
    }, [count]);  // Runs only when `count` changes

    return (
      <div>
        <p>{count}</p>
        <button onClick={() => setCount(count + 1)}>Increment</button>
      </div>
    );
  }
  ```

- **`componentDidMount` Equivalent:**

  To run a function when the component mounts (similar to `componentDidMount`):

  ```jsx
  useEffect(() => {
    console.log('Component mounted');
  }, []);  // Empty dependency array ensures this runs only once
  ```

- **`componentDidUpdate` Equivalent:**

  To run a function when the component updates:

  ```jsx
  useEffect(() => {
    console.log('Component updated or count changed');
  }, [count]);  // Runs only when `count` changes
  ```

- **`componentWillUnmount` Equivalent:**

  You can clean up side effects with the return function inside `useEffect`:

  ```jsx
  useEffect(() => {
    // Setup logic

    return () => {
      // Cleanup logic (similar to componentWillUnmount)
      console.log('Cleanup');
    };
  }, []);  // Runs only once when the component unmounts
  ```

---

### **Summary of Life Cycle Methods**

| **Life Cycle Phase**          | **Class Component Methods**                                  | **Functional Component (useEffect)** |
|-------------------------------|---------------------------------------------------------------|--------------------------------------|
| **Mounting**                   | `constructor`, `getDerivedStateFromProps`, `render`, `componentDidMount` | `useEffect` (empty dependency array `[]`) |
| **Updating**                   | `getDerivedStateFromProps`, `shouldComponentUpdate`, `render`, `getSnapshotBeforeUpdate`, `componentDidUpdate` | `useEffect` (with dependencies) |
| **Unmounting**                 | `componentWillUnmount`                                       | `useEffect` (return cleanup function) |

---

### **Conclusion**

- **Life cycle methods** are essential for managing a component's behavior throughout its existence in the DOM.
- In **class components**, life cycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` are used to handle side effects, state changes, and cleanup.
- In **functional components**, the `useEffect` hook has taken the place of traditional life cycle methods, providing a more concise and flexible way to handle side effects and cleanups.
- Understanding life cycle hooks allows you to optimize performance, handle side effects, and manage resources like timers or network requests efficiently.

---

### **Best Practices**
- Always clean up side effects (timers, subscriptions, event listeners) in `componentWillUnmount` or the `useEffect` cleanup function to prevent memory leaks.
- Avoid making API calls or heavy computations directly inside `render()`; use `componentDidMount`/`componentDidUpdate` (or `useEffect`) instead.
- Use `shouldComponentUpdate`, `React.PureComponent`, or `React.memo` to avoid unnecessary re-renders in performance-critical components.
- Keep `useEffect` callbacks focused — split unrelated side effects into separate `useEffect` calls with their own dependency arrays instead of one large effect.
- Never call `setState` unconditionally inside `componentWillUpdate`/`componentDidUpdate` without a guard, since it can trigger an infinite render loop.
- Prefer functional components with `useEffect` over class life cycle methods for new code, since it consolidates related logic more clearly by concern.

---

### **Interview Questions**

**Q1. What are the three main phases of a React component's life cycle?**
The three phases are Mounting (the component is created and inserted into the DOM), Updating (the component re-renders due to changes in props or state), and Unmounting (the component is removed from the DOM).

**Q2. What is the purpose of `componentDidMount`, and what is it commonly used for?**
`componentDidMount` is called once, immediately after a component is first rendered and inserted into the DOM. It's commonly used for making API calls, setting up subscriptions, or interacting with the DOM directly.

**Q3. What is the purpose of `componentWillUnmount`, and why is it important?**
`componentWillUnmount` is called just before a component is removed from the DOM. It's essential for cleanup tasks like clearing timers, canceling network requests, or removing event listeners to prevent memory leaks.

**Q4. What does `shouldComponentUpdate` do, and how can it be used for performance optimization?**
`shouldComponentUpdate(nextProps, nextState)` lets you control whether a component re-renders by returning `true` or `false`. Returning `false` when props/state haven't meaningfully changed skips an unnecessary render, improving performance.

**Q5. What is `getDerivedStateFromProps`, and why is it a static method?**
`getDerivedStateFromProps(props, state)` is called before every render (both initial and updates) to let a component update its state based on changes in props. It's static so it cannot access `this`, which prevents it from causing side effects and keeps it a pure function of its inputs.

**Q6. What is `getSnapshotBeforeUpdate` used for?**
It's called right before the DOM is updated with the latest render's changes, letting you capture information from the current DOM, such as scroll position, before it potentially changes. The value it returns is passed as the third argument to `componentDidUpdate`.

**Q7. How does the `useEffect` hook relate to class component life cycle methods?**
`useEffect` combines the behavior of `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` into a single API. Which behavior it mimics depends on its dependency array and whether it returns a cleanup function.

**Q8. How do you replicate `componentDidMount` behavior using `useEffect`?**
Pass an empty dependency array `[]` so the effect only runs once, right after the component's first render.
```jsx
useEffect(() => {
  console.log('Component mounted');
}, []);
```

**Q9. How do you replicate `componentWillUnmount` behavior using `useEffect`?**
Return a cleanup function from the effect; React calls it when the component unmounts (and before the effect re-runs on subsequent updates).
```jsx
useEffect(() => {
  return () => console.log('Cleanup');
}, []);
```

**Q10. What is the difference between `componentDidUpdate` and a `useEffect` with a dependency array?**
`componentDidUpdate(prevProps, prevState)` runs after every update and gives you direct access to the previous props/state for comparison. A `useEffect` with a specific dependency array achieves a similar effect by only re-running when one of the listed values changes, without manually comparing previous and current values.

**Q11. Why can't you use life cycle methods like `componentDidMount` inside a functional component?**
Life cycle methods are part of the class component API tied to `React.Component` instances; functional components have no instance or `this` context to attach them to. Instead, functional components use hooks like `useEffect` to achieve equivalent behavior.