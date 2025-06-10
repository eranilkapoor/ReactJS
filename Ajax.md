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