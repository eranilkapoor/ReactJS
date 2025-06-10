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