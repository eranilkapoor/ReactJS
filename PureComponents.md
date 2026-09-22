# Pure Component :- 
* For more performance and simplicity, React also allows us to create pure, stateless components using a normal JavaScript function.
* A Pure component can replace a component that only has a render function.
* Pure components are the simplest, fastest components we can write.
* Simplest One 
```
const HelloWorld = () => (<div>Hello world</div>);

```
* A Notification component 
```

const Notification = (props) => {
    const { level, message } = props;
    const classNames = ['alert', 'alert-'+ level];

    return (
        <div className={className}>
            {message}
        </div>
    )
};

```
## Advantages :-
* We can do away with the heavy lifting of components, no constructor, state, life-cycle madness, etc.
* There is no `this` keyword (i.e. no need to bind)
* Presentational components (also called dumb components) emphasize UI over business logic (i.e. no state manipulation in the component)
* Encourages building smaller, self-contained components
* Highlights badly written code (for better refactoring)
* Fast fast
* They are easy to reuse

## Disadvantages :-
* No life-cycle callback hooks
* Limited functionality
* There is no this keyword 

### **Pure Components in React**

A **Pure Component** in React is a component that only re-renders when its props or state change. It is a type of component that helps optimize the performance of an application by reducing unnecessary re-renders. A pure component implements a **shallow comparison** of props and state to determine if the component should update.

---

### **Key Characteristics of Pure Components**

1. **Shallow Comparison of Props and State:**
   - Pure components automatically perform a shallow comparison (a simple comparison of values) of their props and state.
   - If the previous and new props or state are equal (i.e., no changes), the component will **not re-render**.

2. **Performance Optimization:**
   - By reducing unnecessary re-renders, pure components help optimize the performance of React applications, especially in cases where components have expensive rendering logic.

3. **Inherits from `React.PureComponent`:**
   - A pure component is created by extending `React.PureComponent` instead of `React.Component`.
   - `React.PureComponent` automatically implements the `shouldComponentUpdate()` lifecycle method with a shallow comparison of props and state.

---

### **How Pure Components Work**

React's `PureComponent` optimizes performance by implementing **shouldComponentUpdate()** with a shallow prop and state comparison. 

- **Shallow comparison:** React compares the previous and next values of props and state in a shallow manner, meaning it only compares the values at the first level of the object. If any prop or state value is an object or array, it compares the references (not deep comparison).

For example:
```jsx
const obj1 = { name: 'Alice' };
const obj2 = { name: 'Alice' };
const obj3 = obj1;  // obj3 and obj1 are referencing the same object

console.log(obj1 === obj2); // false (different objects)
console.log(obj1 === obj3); // true (same object reference)
```

---

### **Creating a Pure Component**

To create a pure component, you can extend `React.PureComponent` instead of `React.Component`. 

#### **Example: Pure Component**
```jsx
import React from 'react';

class PureGreeting extends React.PureComponent {
  render() {
    console.log('Rendering PureGreeting');
    return <h1>Hello, {this.props.name}!</h1>;
  }
}

export default PureGreeting;
```

In this example:
- `PureGreeting` is a pure component that only re-renders when its `props.name` value changes.
- React will perform a shallow comparison of the `name` prop, and if it hasn't changed, it will not re-render the component.

---

### **Difference Between `React.Component` and `React.PureComponent`**

| Feature                          | `React.Component`                           | `React.PureComponent`                           |
|-----------------------------------|--------------------------------------------|-----------------------------------------------|
| **Re-rendering**                  | Always re-renders when `setState` or props change. | Only re-renders when `props` or `state` change (shallow comparison). |
| **Shallow Comparison**            | No shallow comparison.                     | Performs shallow comparison of props and state. |
| **`shouldComponentUpdate()`**     | You need to manually implement `shouldComponentUpdate()` for performance optimization. | Automatically implements `shouldComponentUpdate()` with shallow comparison. |
| **Use Case**                      | Suitable for components where re-renders should occur frequently. | Suitable for components where re-renders can be minimized by comparing props and state. |

---

### **When to Use Pure Components**

1. **Performance Optimization:**
   - Use pure components to optimize performance, especially when dealing with large lists or complex UIs where unnecessary re-renders could be costly.
   
2. **Immutability of Props and State:**
   - Pure components work best when props and state are **immutable**, meaning you don't modify them directly, but instead, you create new objects or arrays when their values change. This ensures that the shallow comparison can detect changes reliably.

3. **Simple Components:**
   - Use pure components for simple, presentational components that don't involve complex logic or side effects. Pure components work best in situations where the state and props are relatively simple and don’t involve deep object or array structures.

---

### **Limitations of Pure Components**

1. **Shallow Comparison:**
   - React performs only a shallow comparison of objects and arrays, so if a prop or state contains complex objects, the component may still re-render even though the object’s contents didn’t change.
   - Example:
     ```jsx
     class Person extends React.PureComponent {
       render() {
         return <h1>{this.props.name}</h1>;
       }
     }

     const person = { name: 'John' };
     // On every render, a new object reference is created for `person`
     <Person name={person} />
     ```

   - In the example above, `Person` would re-render every time, even if the `name` didn't change, because the `person` object reference is different on each render.

2. **Mutating Props or State:**
   - If props or state are mutated directly, React won't be able to detect changes, which could lead to stale UI and unexpected behavior.

---

### **Best Practices for Pure Components**

1. **Ensure Immutability:**
   - Always treat props and state as immutable. Use tools like `Object.assign()`, the spread operator (`...`), or libraries like **Immutable.js** to ensure data isn’t mutated.

2. **Avoid Complex Objects in Props or State:**
   - Avoid passing complex objects or arrays as props or state to pure components unless necessary, as React will only perform a shallow comparison.

3. **Manual `shouldComponentUpdate`:**
   - If you need more control over the comparison process, you can manually implement the `shouldComponentUpdate()` lifecycle method in `React.Component` or override it in `React.PureComponent`.

---

### **Example: PureComponent with Shallow Comparison**
```jsx
import React from 'react';

class MyComponent extends React.PureComponent {
  render() {
    console.log('Rendering MyComponent');
    return <div>{this.props.value}</div>;
  }
}

const App = () => {
  const value = { text: 'Hello' };

  return (
    <div>
      <MyComponent value={value} />
    </div>
  );
};

export default App;
```

In this example:
- Since the object `value` is created on every render, `MyComponent` would re-render each time even if the `value`'s content remains the same.
- To prevent unnecessary re-renders, ensure that objects are not recreated each time or pass primitive values instead.

---

### **Conclusion**

- **Pure Components** help optimize React applications by reducing unnecessary re-renders. They perform shallow comparisons of props and state and only re-render when necessary.
- They work best with **immutable data** and simple props and state structures.
- When used appropriately, pure components can significantly improve performance, especially in large and complex applications.

---

### **Best Practices**
- Extend `React.PureComponent` for simple, presentational class components whose props and state are primitives or shallowly comparable.
- Keep props and state immutable; create new objects or arrays with the spread operator instead of mutating existing ones directly.
- Avoid passing new object, array, or function literals as props on every render, since a shallow comparison will always treat them as changed references.
- For functional components, use `React.memo` as the equivalent optimization to `PureComponent`.
- Combine with `useMemo`/`useCallback` (or memoized selectors) to keep object and function prop references stable across renders.
- Don't rely on pure components for deeply nested data structures; implement a custom `shouldComponentUpdate` or a deep-compare utility if a true deep comparison is required.

---

### **Interview Questions**

**Q1. What is a `React.PureComponent`, and how does it differ from `React.Component`?**
`React.PureComponent` is identical to `React.Component` except it automatically implements `shouldComponentUpdate()` with a shallow comparison of props and state. `React.Component` re-renders whenever `setState` is called or new props are passed, regardless of whether the values actually changed.

**Q2. What does "shallow comparison" mean in the context of `PureComponent`?**
A shallow comparison checks whether primitive values are equal and whether object/array values reference the exact same object in memory — it does not recursively compare the contents of nested objects or arrays.
```jsx
{ name: 'Alice' } === { name: 'Alice' } // false, different references
```

**Q3. Why might a `PureComponent` still re-render unnecessarily when passed an object prop?**
If a new object or array literal is created on every parent render (e.g., `<Person name={{ name: 'John' }} />`), its reference changes each time even if its contents are identical, so the shallow comparison sees it as "changed" and re-renders anyway.

**Q4. What is the functional-component equivalent of `PureComponent`, and how do you use it?**
`React.memo` wraps a functional component to give it the same shallow-comparison optimization as `PureComponent`.
```jsx
const Greeting = React.memo(function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
});
```

**Q5. What lifecycle method does `PureComponent` implement automatically, and what does it return?**
It automatically implements `shouldComponentUpdate(nextProps, nextState)`, returning `false` (skipping re-render) when a shallow comparison finds no differences in props or state, and `true` otherwise.

**Q6. Why is mutating state or props directly problematic when using `PureComponent`?**
Because the shallow comparison relies on reference equality, mutating an object or array in place keeps the same reference, so `PureComponent` won't detect the change and will incorrectly skip a needed re-render, leading to stale UI.

**Q7. In what situations should you avoid using `PureComponent`?**
Avoid it when props or state involve deeply nested objects/arrays that mutate in place, when props change on every render as new object/function literals (defeating the optimization), or when you specifically need the component to always re-render regardless of shallow equality.

**Q8. What are the main advantages of pure/stateless components besides re-render optimization?**
They avoid the overhead of constructors, `this` binding, and lifecycle complexity; they emphasize presentation over business logic; and they encourage building smaller, self-contained, and easily reusable components.

**Q9. How would you achieve a deep comparison instead of the default shallow one for a performance-sensitive component?**
You can manually implement `shouldComponentUpdate` (extending `React.Component` instead of `PureComponent`) with a custom deep-equality check, or use `React.memo` with a custom comparison function as its second argument.

**Q10. What are the disadvantages of relying heavily on pure/stateless components?**
They lack life-cycle callback hooks (in the simplest stateless form), have limited functionality since they can't hold internal state or complex logic on their own, and shallow comparison alone can't reliably optimize components with complex, frequently-recreated data structures.

**Q11. Does using `PureComponent` guarantee better performance in every case?**
No. The shallow comparison itself has a small cost on every render, so for components that would re-render anyway (props genuinely change every time) or that are cheap to render, `PureComponent` can add overhead without meaningful benefit.