Redux Toolkit (RTK) is the official, recommended way to use Redux. It simplifies the setup and usage of Redux by providing a set of tools and utilities. Here's a concise example of how to use Redux Toolkit in a React application:

---

### **1. Install Required Dependencies**
```bash
npm install @reduxjs/toolkit react-redux
```

---

### **2. Create a Redux Slice**
A slice contains the `reducer` logic and initial state.

```javascript
// features/counter/counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0,
  },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

export default counterSlice.reducer;
```

---

### **3. Configure the Store**
Use `configureStore` to set up the Redux store.

```javascript
// app/store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from '../features/counter/counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

---

### **4. Provide the Store**
Wrap your application with the Redux `Provider`.

```javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './app/store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

---

### **5. Use the Redux State and Dispatch Actions**
Access the state and dispatch actions using React-Redux hooks: `useSelector` and `useDispatch`.

```javascript
// App.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, incrementByAmount } from './features/counter/counterSlice';

function App() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
      <button onClick={() => dispatch(incrementByAmount(5))}>Increment by 5</button>
    </div>
  );
}

export default App;
```

---

### **Key Advantages of Redux Toolkit**
1. **Boilerplate Reduction:** Combines actions and reducers into a single slice.
2. **Immutability Made Simple:** Uses `Immer` internally for safe state updates.
3. **Built-in Middleware:** Includes helpful middleware like `redux-thunk`.
4. **DevTools Integration:** Preconfigured with Redux DevTools support.

This example demonstrates a basic counter application using Redux Toolkit. You can scale this pattern for more complex applications by adding more slices, actions, and reducers as needed.

---

### **Best Practices**
- Use `createSlice` instead of hand-writing action types, action creators, and a switch-based reducer.
- Write "mutating" logic freely inside `createSlice` reducers — Immer (built into RTK) converts it into safe, immutable updates behind the scenes.
- Use `configureStore` instead of the plain `createStore`, since it wires up Redux DevTools and default middleware (including thunk) automatically.
- Organize state into one slice per feature/domain, mirroring your folder structure, instead of one giant reducer.
- Use `createAsyncThunk` for async logic so you get consistent `pending`/`fulfilled`/`rejected` action types for free.
- For apps with heavy data-fetching needs, consider **RTK Query** instead of hand-rolled `useEffect` fetching plus a slice for server data.

---

### **Interview Questions**

**Q1. What problem does Redux Toolkit solve compared to plain Redux?**
It removes the repetitive boilerplate of plain Redux — separate action type constants, action creators, and switch-based reducers — by bundling them into a single `createSlice` call, while also reducing common mistakes like accidental state mutation.

**Q2. What does `createSlice` generate for you?**
Given a `name`, `initialState`, and a `reducers` object, it automatically generates action creators and action types matching each reducer function name, plus a single reducer function that handles them, all exported together.

**Q3. How does RTK let you write "mutating" code safely inside reducers?**
RTK uses **Immer** internally. Reducer functions can appear to mutate `state` directly (e.g., `state.value += 1`), but Immer tracks those changes against a draft and produces a proper new immutable state object behind the scenes.

**Q4. What is `configureStore`, and how does it differ from `createStore`?**
`configureStore` is RTK's replacement for Redux's `createStore` — it combines reducers, applies sensible default middleware (including `redux-thunk` and dev-only mutation/serializability checks), and enables Redux DevTools automatically, all with less manual setup.

**Q5. How do you read a specific slice's state inside a component using RTK?**
With `useSelector`, indexing into the store's state by the key the slice was registered under in `configureStore`'s `reducer` object.
```javascript
const count = useSelector((state) => state.counter.value);
```

**Q6. What is `createAsyncThunk` used for?**
It generates a thunk for an async operation (like an API call) along with three automatically dispatched action types — `pending`, `fulfilled`, and `rejected` — so you can handle loading and error states consistently in `extraReducers` without writing that boilerplate by hand.

**Q7. Does Redux Toolkit include Redux DevTools support by default?**
Yes — `configureStore` enables the Redux DevTools Extension integration automatically in development, with no extra setup required, unlike plain `createStore` which needs it wired in manually.

**Q8. How would you add a reducer that accepts a payload, like `incrementByAmount`, in a slice?**
Add a function to the slice's `reducers` object that takes `(state, action)` and uses `action.payload` for the amount, then dispatch it by calling the generated action creator with an argument.
```javascript
incrementByAmount: (state, action) => { state.value += action.payload; }
// usage: dispatch(incrementByAmount(5))
```

**Q9. What is RTK Query, and what problem does it solve?**
RTK Query is a data-fetching and caching layer built into Redux Toolkit. It generates hooks for API endpoints that handle fetching, caching, re-fetching, and loading/error states automatically, removing the need to manually write slices and thunks for server data.

**Q10. How do multiple slices get combined into a single store with RTK?**
Each slice's reducer is passed as a value in the `reducer` object given to `configureStore`, keyed by the name that slice's state will live under — RTK combines them the same way `combineReducers` does under the hood.

**Q11. Why does `createSlice` require a `name` property?**
The `name` is used as a prefix for the automatically generated action types (e.g., `counter/increment`), which keeps action types unique across slices and makes them identifiable in Redux DevTools.

**Q12. Can you still write reducers the traditional "return a new state object" way inside `createSlice`, or must you mutate?**
Both styles work — Immer only intercepts state that's mutated; if a reducer instead returns a brand-new state object, RTK uses that returned value as-is instead of applying the draft-mutation logic.