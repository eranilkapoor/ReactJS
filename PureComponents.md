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