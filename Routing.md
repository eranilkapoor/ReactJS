Routing in React allows you to create a **single-page application (SPA)** with multiple views or pages. It enables navigation between different parts of your application without reloading the page, providing a seamless user experience.

React uses the **React Router** library for implementing routing.

---

### **Core Concepts of React Routing**

1. **Single Page Application (SPA)**:
   - React applications are SPAs, meaning they load a single HTML file and dynamically render components based on the URL.

2. **React Router**:
   - A popular library for managing routing in React applications.
   - Offers dynamic routing capabilities.
   - Provides various components like `BrowserRouter`, `Routes`, `Route`, `Link`, and `useNavigate`.

---

### **Key Components in React Router**

1. **BrowserRouter**:
   - Wraps the application and enables routing.
   - Uses HTML5 history API for navigation.

2. **Routes and Route**:
   - Define the mapping of URLs to components.
   - `Route` renders the component when the path matches the URL.

3. **Link**:
   - Used to create navigational links without reloading the page.
   - Replaces the traditional `<a>` tag.

4. **useNavigate**:
   - A hook for programmatic navigation.

5. **Switch (Legacy) / Routes**:
   - `Switch` (React Router v5) or `Routes` (React Router v6) renders the first matching route.

---

### **Setting Up Routing**

1. **Install React Router**
   ```bash
   npm install react-router-dom
   ```

2. **Basic Routing Example**
   ```javascript
   import React from "react";
   import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

   const Home = () => <h2>Home Page</h2>;
   const About = () => <h2>About Page</h2>;
   const Contact = () => <h2>Contact Page</h2>;

   const App = () => {
     return (
       <BrowserRouter>
         <nav>
           <Link to="/">Home</Link> | <Link to="/about">About</Link> | <Link to="/contact">Contact</Link>
         </nav>
         <Routes>
           <Route path="/" element={<Home />} />
           <Route path="/about" element={<About />} />
           <Route path="/contact" element={<Contact />} />
         </Routes>
       </BrowserRouter>
     );
   };

   export default App;
   ```

---

### **Dynamic Routing**
Dynamic routing allows you to define routes with parameters.

#### Example:
```javascript
import React from "react";
import { BrowserRouter, Routes, Route, useParams } from "react-router-dom";

const User = () => {
  const { id } = useParams(); // Access route parameter
  return <h2>User ID: {id}</h2>;
};

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/user/:id" element={<User />} />
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```
- Visiting `/user/123` will render `User ID: 123`.

---

### **Programmatic Navigation**
You can navigate programmatically using the `useNavigate` hook.

#### Example:
```javascript
import React from "react";
import { BrowserRouter, Routes, Route, useNavigate } from "react-router-dom";

const Home = () => {
  const navigate = useNavigate();

  const goToAbout = () => {
    navigate("/about");
  };

  return (
    <div>
      <h2>Home Page</h2>
      <button onClick={goToAbout}>Go to About</button>
    </div>
  );
};

const About = () => <h2>About Page</h2>;

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```

---

### **Nested Routes**
React Router supports nested routes, allowing you to organize pages hierarchically.

#### Example:
```javascript
import React from "react";
import { BrowserRouter, Routes, Route, Outlet } from "react-router-dom";

const Dashboard = () => (
  <div>
    <h2>Dashboard</h2>
    <Outlet /> {/* Nested routes will render here */}
  </div>
);

const Settings = () => <h3>Settings</h3>;
const Profile = () => <h3>Profile</h3>;

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />}>
          <Route path="settings" element={<Settings />} />
          <Route path="profile" element={<Profile />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```
- `/dashboard/settings` renders the `Settings` component under `Dashboard`.

---

### **404 Page (Not Found Route)**
Handle undefined routes by creating a "catch-all" route.

#### Example:
```javascript
<Route path="*" element={<h2>Page Not Found</h2>} />
```

---

### **Switch vs. Routes**
- `Switch` (React Router v5) renders the first matching route.
- `Routes` (React Router v6) replaces `Switch` and allows nested routes with `element`.

---

### **Advantages of React Router**
1. **Dynamic Routing**: Enables real-time updates without reloading the page.
2. **Nested Routes**: Organize routes hierarchically.
3. **Programmatic Navigation**: Navigate dynamically in response to user actions.
4. **Component-Based**: Integrates seamlessly with React’s component model.

By leveraging React Router, you can efficiently implement navigation in your React applications, making them more intuitive and user-friendly.

---

### **Best Practices**
- Lazy-load route components with `React.lazy` and `Suspense` so users only download the code for the route they're visiting.
- Use `Outlet` and nested `Route`s to share layout (nav bars, sidebars) instead of duplicating it in every page component.
- Always include a catch-all `<Route path="*" />` so unmatched URLs show a proper 404 instead of a blank page.
- Use `<Link>` / `<NavLink>` instead of `<a href>` for internal navigation, so the page doesn't do a full reload.
- Wrap protected routes in a guard component that checks auth state and redirects with `<Navigate>` rather than scattering auth checks across pages.
- Keep route paths and structure centralized (e.g., a single routes config or `App.jsx`) so the app's URL structure is easy to see and maintain.

---

### **Interview Questions**

**Q1. What is the purpose of `BrowserRouter`?**
It wraps the application and enables client-side routing using the HTML5 History API, allowing the URL to change and components to re-render without a full page reload.

**Q2. What is the difference between `Switch` (React Router v5) and `Routes` (React Router v6)?**
`Switch` renders the first `Route` that matches, using a `component`/`render` prop. `Routes` (v6) replaces it with improved matching, uses the `element` prop, and natively supports nested routes via `Outlet`.

**Q3. How do you access a dynamic URL parameter in React Router v6?**
With the `useParams` hook, which returns an object of the parameters defined in the matching route's path.
```javascript
const { id } = useParams(); // for path="/user/:id"
```

**Q4. How does `useNavigate` differ from `<Link>`?**
`<Link>` renders an anchor-like element for the user to click to navigate declaratively. `useNavigate` returns a function for navigating programmatically, e.g. after a form submission or a timed redirect, without rendering a link.

**Q5. What does the `<Outlet>` component do in nested routing?**
It acts as a placeholder inside a parent route's element, marking where the matched child route's element should be rendered, enabling shared layouts around nested pages.

**Q6. How do you implement a "page not found" route?**
Add a `Route` with `path="*"` as the last route in the `Routes` list; it matches any URL that didn't match an earlier, more specific route.
```javascript
<Route path="*" element={<h2>Page Not Found</h2>} />
```

**Q7. How would you protect a route so only authenticated users can access it?**
Create a wrapper component (e.g., `ProtectedRoute`) that checks authentication state and either renders its `children`/`Outlet` or returns `<Navigate to="/login" />`, then wrap the protected routes with it.

**Q8. What is the difference between `BrowserRouter` and `HashRouter`?**
`BrowserRouter` uses the HTML5 History API for clean URLs (`/about`) and requires server configuration to handle deep links. `HashRouter` stores the route in the URL hash (`/#/about`), which works without server configuration but produces less clean URLs.

**Q9. How can you pass data during navigation with `useNavigate`?**
Pass a `state` object as the second argument to `navigate`, which can then be read on the destination page via the `useLocation` hook's `state` property.
```javascript
navigate("/details", { state: { fromSearch: true } });
```

**Q10. How would you combine `React.lazy` with React Router for code-splitting routes?**
Define each page component with `React.lazy(() => import("./Page"))` and wrap the `<Routes>` block in a `<Suspense fallback={...}>`, so each route's code is only downloaded when the user navigates to it.

**Q11. What's the practical difference between a `<Link>` and a plain `<a>` tag?**
`<Link>` intercepts the click and updates the URL via the router without triggering a full page reload, preserving app state, whereas a plain `<a>` causes the browser to make a fresh request and reload the entire page.

**Q12. How do you perform a programmatic redirect in React Router v6?**
Either render a `<Navigate to="/target" />` element conditionally, or call the `navigate` function returned by `useNavigate()` inside an event handler or effect.