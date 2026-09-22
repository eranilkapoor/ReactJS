**Performance Optimization** in React is about minimizing unnecessary rendering work so your app stays fast as it grows in size and complexity. React is already efficient thanks to the virtual DOM, but poorly structured components can still cause excessive re-renders, expensive recalculations, and sluggish user interfaces. Knowing when and how to optimize — and just as importantly, when *not* to — is a core skill for building production React applications.

---

### **Why Components Re-render**

A React functional component re-renders whenever:
1. **Its own state changes** — a `useState` or `useReducer` setter is called with a new value.
2. **Its props change** — the parent passes down a new value for any prop.
3. **Its parent re-renders** — by default, when a parent component re-renders, **all of its children re-render too**, even if the children's own props didn't change.
4. **Context it consumes changes** — a component using `useContext` re-renders whenever the context value it's subscribed to updates.

#### **Why Unnecessary Re-renders Hurt Performance**
Every render means React runs the component function again, builds a new virtual DOM tree, and diffs it against the previous one. For simple components this is cheap, but it adds up:
- Deeply nested component trees re-render top to bottom by default.
- Components doing expensive calculations recompute them on every render.
- Large lists re-render every row even when only one row changed.

```jsx
// Parent re-renders every second; Child re-renders too, even though
// Child's own props never change.
function Parent() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <div>
      <p>{time.toLocaleTimeString()}</p>
      <Child /> {/* re-renders every second for no reason */}
    </div>
  );
}

function Child() {
  console.log("Child rendered");
  return <p>I never change, but I keep re-rendering.</p>;
}
```

---

### **`React.memo`: Skipping Re-renders for Unchanged Props**

`React.memo` is a higher-order component that memoizes a functional component. React will skip re-rendering it if its props are shallowly equal to the props from the previous render.

#### **Before (no memoization)**
```jsx
function Child({ label }) {
  console.log("Child rendered");
  return <p>{label}</p>;
}
```

#### **After (memoized)**
```jsx
import React from "react";

const Child = React.memo(function Child({ label }) {
  console.log("Child rendered");
  return <p>{label}</p>;
});

export default Child;
```

Now, wrapping the earlier example:

```jsx
function Parent() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <div>
      <p>{time.toLocaleTimeString()}</p>
      <Child label="I never change" /> {/* skipped, thanks to React.memo */}
    </div>
  );
}
```

Because `label` never changes, `React.memo` prevents `Child` from re-rendering every time `Parent` updates its `time` state.

#### **Custom Comparison Function**
By default `React.memo` does a shallow comparison of props. You can supply a second argument to customize the comparison logic:

```jsx
const UserCard = React.memo(
  function UserCard({ user }) {
    return <p>{user.name}</p>;
  },
  (prevProps, nextProps) => prevProps.user.id === nextProps.user.id
  // return true to SKIP re-render, false to allow it
);
```

---

### **`useMemo`: Memoizing Expensive Computed Values**

`useMemo` caches the *result* of an expensive calculation and only recomputes it when its dependencies change.

```jsx
import React, { useMemo, useState } from "react";

function ExpensiveList({ items, filterText }) {
  const [count, setCount] = useState(0); // unrelated state

  const filteredItems = useMemo(() => {
    console.log("Filtering items..."); // only logs when items/filterText change
    return items.filter((item) =>
      item.name.toLowerCase().includes(filterText.toLowerCase())
    );
  }, [items, filterText]);

  return (
    <div>
      <button onClick={() => setCount((c) => c + 1)}>
        Unrelated clicks: {count}
      </button>
      <ul>
        {filteredItems.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

Without `useMemo`, clicking the unrelated "Unrelated clicks" button would re-run the expensive `.filter()` call on every click, even though `items` and `filterText` never changed. The dependency array `[items, filterText]` tells React to only recompute `filteredItems` when one of those values changes.

---

### **`useCallback`: Memoizing Function References**

Every time a component re-renders, any function defined inside it is recreated as a brand-new reference — even if its logic is identical. This matters when that function is passed as a prop to a child wrapped in `React.memo`, because a new function reference breaks the shallow-equality check and forces the child to re-render anyway.

#### **The Problem (Before `useCallback`)**
```jsx
const Button = React.memo(function Button({ onClick, children }) {
  console.log(`${children} rendered`);
  return <button onClick={onClick}>{children}</button>;
});

function Parent() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  // A NEW function is created on every render of Parent...
  const handleClick = () => setCount((c) => c + 1);

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      {/* ...so Button re-renders every time you type, despite React.memo! */}
      <Button onClick={handleClick}>Increment ({count})</Button>
    </div>
  );
}
```

#### **The Fix (With `useCallback`)**
```jsx
import React, { useCallback, useState } from "react";

function Parent() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  // The same function reference is reused across renders,
  // as long as its dependencies don't change.
  const handleClick = useCallback(() => setCount((c) => c + 1), []);

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <Button onClick={handleClick}>Increment ({count})</Button>
      {/* Button no longer re-renders when you type in the input */}
    </div>
  );
}
```

`useCallback` only pays off when combined with `React.memo` on the receiving child — otherwise the child re-renders regardless of whether the function reference changed.

| Hook/API | Memoizes | Typical Use Case |
|---|---|---|
| `React.memo` | A whole component | Skip re-rendering a child when its props haven't changed |
| `useMemo` | A computed value | Avoid recalculating an expensive value on every render |
| `useCallback` | A function reference | Keep a stable function identity to pass to a memoized child |

---

### **Common Mistake: Overusing `useMemo`/`useCallback`**

Memoization is not free — it costs memory (to store the cached value/dependencies) and a comparison check on every render. Wrapping trivial computations or every single function in `useMemo`/`useCallback` can actually make performance *worse*, not better, while adding noise and complexity to the code.

```jsx
// Unnecessary: string concatenation is essentially free.
// The useMemo overhead is not worth it here.
const fullName = useMemo(() => `${firstName} ${lastName}`, [firstName, lastName]);

// Better: just compute it directly.
const fullName = `${firstName} ${lastName}`;
```

```jsx
// Unnecessary: this function isn't passed to a memoized child
// or used as an effect dependency, so useCallback buys nothing.
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

**Rule of thumb**: only reach for `useMemo`/`useCallback` when you've identified an actual performance problem (e.g., via the Profiler) involving a genuinely expensive computation, or when passing a function/object to a `React.memo`-wrapped child or a `useEffect` dependency array where reference stability matters.

---

### **Windowing / Virtualization for Long Lists**

Rendering a list with thousands of DOM nodes — even if each row is simple — is expensive, because the browser has to create, layout, and paint every single node, most of which aren't even visible on screen. **Windowing** (also called **virtualization**) solves this by only rendering the rows currently visible in the viewport (plus a small buffer), recycling DOM nodes as the user scrolls.

Popular libraries for this:
- **`react-window`** — a lightweight, modern virtualization library.
- **`react-virtualized`** — the older, more feature-rich (and heavier) predecessor to `react-window`.

```jsx
import { FixedSizeList as List } from "react-window";

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index].name}</div>
  );

  return (
    <List height={400} itemCount={items.length} itemSize={35} width={300}>
      {Row}
    </List>
  );
}
```

Instead of rendering 10,000 `<div>` rows, `react-window` might only render ~15 at a time — dramatically reducing DOM node count and improving scroll performance.

---

### **Spotting Unnecessary Re-renders with React DevTools Profiler**

The **React DevTools Profiler** (part of the official browser extension) lets you record a session of interactions and visualize which components rendered, how long each render took, and why. Key ways to use it:
- Enable **"Highlight updates when components render"** to see a visual flash over components as they re-render in real time.
- Record a profiling session, then inspect the flame graph — components that render frequently despite unchanged props are good candidates for `React.memo`.
- Each component's profiler entry can show the reason it re-rendered (e.g., "props changed," "hooks changed"), which helps pinpoint exactly what's triggering the extra work.

This is the recommended first step before reaching for `useMemo`/`useCallback` — measure first, then optimize only what's actually slow.

---

### **Code-Splitting**

Reducing *initial* bundle size and deferring the loading of components until they're needed is another major performance lever, achieved via `React.lazy` and `Suspense`. This topic is covered in full detail in **[./LazyLoading.md](./LazyLoading.md)**.

---

### **Best Practices**
- Measure with the React DevTools Profiler before optimizing — don't guess where the bottleneck is.
- Use `React.memo` for components that render often with the same props, especially "leaf" components deep in the tree.
- Pair `useCallback`/`useMemo` with `React.memo` — memoizing a function or value has no benefit unless something downstream actually relies on referential stability.
- Avoid inline object/array/function literals as props to memoized children (`<Child style={{ color: "red" }} />` creates a new object every render, defeating `React.memo`).
- Don't over-optimize simple components; the overhead of memoization can outweigh the benefit for cheap renders.
- Virtualize long lists (hundreds+ of rows) with `react-window` instead of rendering every row.
- Split code with `React.lazy`/`Suspense` to keep the initial bundle small (see `./LazyLoading.md`).
- Keep state as local as possible — lifting state up unnecessarily causes broader re-render cascades.

---

### **Interview Questions**

**Q1. What causes a React component to re-render?**
A component re-renders when its own state changes, when its props change, when its parent re-renders (by default, regardless of whether its own props changed), or when a context value it consumes via `useContext` updates.

**Q2. What does `React.memo` do?**
It's a higher-order component that memoizes a functional component, causing React to skip re-rendering it if its props are shallowly equal to the props from the previous render, unless a custom comparison function says otherwise.

**Q3. What's the difference between `useMemo` and `useCallback`?**
`useMemo` memoizes the *return value* of a function (an expensive computed value), while `useCallback` memoizes the *function reference itself*. `useCallback(fn, deps)` is roughly equivalent to `useMemo(() => fn, deps)`.

**Q4. Why doesn't `React.memo` alone prevent a child from re-rendering when it receives a callback prop?**
Because functions defined inline in a parent component are recreated as new references on every render. Even though the callback's logic hasn't changed, `React.memo`'s shallow prop comparison sees a different function reference and re-renders the child anyway — `useCallback` is needed to keep the reference stable.

**Q5. When should you avoid using `useMemo`/`useCallback`?**
For cheap computations or functions not passed to memoized children or used in dependency arrays, since the memoization bookkeeping itself has a cost. Overusing them adds complexity and can make performance worse rather than better.

**Q6. How would you optimize rendering a list of 10,000 items?**
Use a windowing/virtualization library like `react-window` so only the rows currently visible in the viewport (plus a small buffer) are rendered to the DOM, instead of all 10,000 rows at once.

**Q7. What tool would you use to identify which components are re-rendering unnecessarily?**
The React DevTools Profiler, which can record an interaction and show a flame graph of what rendered and why, plus a "highlight updates" mode that visually flashes re-rendering components in real time.

**Q8. Does `React.memo` do a deep comparison of props by default?**
No, it does a shallow comparison — it checks reference equality for each prop. Objects, arrays, and functions created inline on every render will always be considered "different," even if their contents are equivalent, unless a custom comparison function is provided.

**Q9. Give an example of when `useMemo` is genuinely useful.**
When computing a derived value that involves real work — e.g., filtering or sorting a large array, or a heavy mathematical calculation — where recomputing it on every render (including renders triggered by unrelated state) would be noticeably slow.
```jsx
const sorted = useMemo(() => bigArray.slice().sort(compareFn), [bigArray]);
```

**Q10. How does code-splitting relate to performance optimization?**
While `React.memo`/`useMemo`/`useCallback` reduce *re-render* work at runtime, code-splitting (via `React.lazy` and `Suspense`) reduces the *initial bundle size*, so users download and parse less JavaScript before the app becomes interactive — a different but complementary optimization axis.

**Q11. What's the risk of passing a new object literal as a prop to a memoized component on every render?**
It defeats `React.memo`'s shallow comparison, since `{}` !== `{}` in JavaScript even with identical contents — the memoized component re-renders every time regardless of the memoization, because the prop reference always looks "new."

**Q12. Why might adding `useCallback` everywhere in a component actually hurt performance?**
Each `useCallback` call still allocates a dependency array and performs a comparison on every render, and it doesn't eliminate the function creation cost, it just conditionally reuses an old reference. If nothing downstream depends on referential stability (no memoized child, no effect dependency), this bookkeeping is pure overhead with no benefit.
