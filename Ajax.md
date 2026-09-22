Making **AJAX calls** in React involves fetching data from a server asynchronously without reloading the page. You can use either the built-in `Fetch API` or a third-party library like `Axios`. Both are widely used for HTTP requests in React applications.

### Fetch API
The **Fetch API** is a modern, browser-built method for making HTTP requests. It is Promise-based and works well in most scenarios.

#### Example: Fetch API in React
1. **Setup a Component for Data Fetching**
```javascript
import React, { useState, useEffect } from "react";

const FetchExample = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Fetch data from an API
    fetch("https://jsonplaceholder.typicode.com/posts")
      .then((response) => {
        if (!response.ok) {
          throw new Error("Network response was not ok");
        }
        return response.json();
      })
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((error) => {
        setError(error);
        setLoading(false);
      });
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {data.slice(0, 5).map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
};

export default FetchExample;
```

---

### Axios
**Axios** is a popular third-party HTTP client library. It simplifies making requests by providing better defaults, support for interceptors, and automatic JSON data handling.

#### Install Axios
```bash
npm install axios
```

#### Example: Axios in React
1. **Setup a Component for Data Fetching**
```javascript
import React, { useState, useEffect } from "react";
import axios from "axios";

const AxiosExample = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Fetch data using Axios
    axios
      .get("https://jsonplaceholder.typicode.com/posts")
      .then((response) => {
        setData(response.data);
        setLoading(false);
      })
      .catch((error) => {
        setError(error);
        setLoading(false);
      });
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {data.slice(0, 5).map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
};

export default AxiosExample;
```

---

### Comparison of Fetch API and Axios

| Feature                  | Fetch API                              | Axios                                      |
|--------------------------|----------------------------------------|-------------------------------------------|
| **Syntax**               | Requires more boilerplate             | Simple and concise                        |
| **Error Handling**       | Errors only for network issues; manual for HTTP codes | Automatically handles HTTP errors         |
| **Response Type**        | Needs manual `response.json()` parsing| Automatically parses JSON responses       |
| **Request/Response Interceptors** | Not built-in                          | Supports request/response interceptors    |
| **Browser Support**      | Native to modern browsers              | Needs installation via npm/yarn           |

---

### Key Notes
- **Fetch API** is suitable for simple, lightweight projects.
- **Axios** offers more advanced features, making it a better choice for complex applications requiring interceptors, custom headers, or error handling.

---

### **Best Practices**
- Always check `response.ok` (or catch Axios's rejected promise) — `fetch` only rejects on network failure, not on HTTP error statuses like 404 or 500.
- Wrap data-fetching logic in `try/catch` with `async/await` for cleaner, more readable error handling than chained `.then()` calls.
- Guard against updating state after a component unmounts, using an `AbortController` or a cleanup flag inside `useEffect`.
- Centralize API calls in a dedicated module or Axios instance with a `baseURL` and shared interceptors, instead of duplicating URLs everywhere.
- Always render distinct loading and error states so the user isn't left staring at a blank screen when a request is slow or fails.
- For anything beyond a couple of simple requests, consider a data-fetching library (React Query, SWR) to get caching, retries, and race-condition handling for free.

---

### **Interview Questions**

**Q1. Why doesn't `fetch()` reject its promise for HTTP error responses like 404 or 500?**
`fetch` only rejects when the request itself fails at the network level (e.g., no connectivity, DNS failure). A response with a 4xx or 5xx status is still considered a "successful" fetch as far as the promise is concerned, so `response.ok` must be checked manually.

**Q2. How do you manually handle HTTP errors with the Fetch API?**
Check `response.ok` (or `response.status`) after the promise resolves, and throw an error yourself if it's false so it flows into the `.catch()` block or a surrounding `try/catch`.
```javascript
if (!response.ok) throw new Error("Network response was not ok");
```

**Q3. What are the key differences between Fetch and Axios?**
Axios automatically parses JSON and rejects on HTTP error statuses, supports request/response interceptors, and needs to be installed via npm, while Fetch is built into the browser but requires manual `response.json()` parsing and manual HTTP-error handling.

**Q4. Why do you need to guard against setting state after a component unmounts during an async fetch, and how?**
If a component unmounts before an in-flight request resolves, calling `setState` on it triggers a React warning and can mask a memory leak. It's guarded against with an `AbortController` to cancel the request, or a boolean flag checked before calling the state setter in the `useEffect` cleanup.

**Q5. How would you cancel an in-flight fetch request in React?**
Create an `AbortController`, pass its `signal` to `fetch`, and call `controller.abort()` in the `useEffect` cleanup function when the component unmounts or dependencies change.

**Q6. Why does the `useEffect` in the fetch examples use an empty dependency array `[]`?**
An empty dependency array means the effect runs only once, after the initial mount, which is the desired behavior for fetching data one time when the component first renders rather than on every re-render.

**Q7. How does Axios automatically parse JSON compared to Fetch?**
Axios inspects the response `Content-Type` and automatically parses JSON bodies into a JavaScript object available at `response.data`, whereas Fetch requires an explicit `await response.json()` call to do the same.

**Q8. What are Axios interceptors used for?**
Interceptors let you run code before a request is sent (e.g., attaching an auth token to headers) or after a response is received (e.g., handling a 401 globally), centralizing cross-cutting concerns instead of repeating them in every call.

**Q9. How would you implement a retry for a failed API call?**
Wrap the request in a loop or recursive function with a retry counter, catching failures and re-attempting the request (often with a delay or exponential backoff) up to a maximum number of attempts before giving up.

**Q10. What is a race condition in data fetching, and when might it occur in a React component?**
It occurs when multiple requests are in flight and an earlier, slower request resolves *after* a later one, overwriting the UI with stale data — for example, when a search input triggers a new fetch on every keystroke without cancelling the previous request.

**Q11. When would you reach for a library like React Query instead of manual `fetch` + `useEffect`?**
When the app needs caching, automatic refetching, deduplication of identical requests, background updates, or built-in loading/error state management — all of which React Query provides out of the box instead of being hand-rolled.

**Q12. How do you send a POST request with a JSON body using Fetch versus Axios?**
With Fetch you must set the method, stringify the body, and set the `Content-Type` header manually; Axios does the JSON stringification and header for you.
```javascript
fetch(url, { method: "POST", headers: { "Content-Type": "application/json" }, body: JSON.stringify(data) });
axios.post(url, data);
```