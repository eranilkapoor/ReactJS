**Server-Side Rendering (SSR)** is a technique where React components are rendered to HTML on the server for each request, and that fully-formed HTML is sent to the browser before any JavaScript runs. This contrasts with the client-side rendering approach used by plain React apps (like a default Create React App project), where the server sends a nearly empty HTML shell and the browser has to download, parse, and execute JavaScript before anything appears on screen. Understanding SSR — and the framework that made it mainstream for React, **Next.js** — is essential for building fast, SEO-friendly production applications.

---

### **CSR vs SSR: Why It Matters**

#### **Client-Side Rendering (CSR)**
In a pure CSR app (what plain Create React App produces), the initial HTML response looks roughly like this:

```html
<div id="root"></div>
<script src="/static/js/bundle.js"></script>
```

The browser must download the JavaScript bundle, execute it, let React build the virtual DOM, and only then does content appear. This means:
- **Slower first paint** — users see a blank page or spinner until JS loads and runs.
- **Poor SEO** — search engine crawlers that don't execute JavaScript (or execute it poorly) may index an empty page.

#### **Server-Side Rendering (SSR)**
With SSR, the server runs your React component code for each incoming request and returns a fully rendered HTML document:

```html
<div id="root">
  <h1>Welcome, Alice!</h1>
  <p>Your latest orders...</p>
</div>
<script src="/static/js/bundle.js"></script>
```

The browser can paint meaningful content immediately, before JavaScript even loads. React then "hydrates" the static HTML — attaching event listeners and making it interactive — using the same bundle. Benefits:
- **Faster perceived first paint** — users see real content sooner.
- **Better SEO** — crawlers receive fully rendered HTML with real content already in place.

#### **Static Site Generation (SSG) — a Third Option**
**SSG** renders pages to HTML **at build time**, not per-request. The resulting static HTML files are then served instantly from a CDN, with no server-side rendering work happening on each visit. This is ideal for content that doesn't change per-request (blogs, marketing pages, documentation).

| Approach | When HTML Is Generated | Best For |
|---|---|---|
| **CSR** | In the browser, after JS loads | Highly interactive apps where SEO doesn't matter (dashboards, internal tools) |
| **SSR** | On the server, per request | Pages needing fresh, per-user or frequently changing data plus SEO |
| **SSG** | At build time, once | Content that rarely changes (blogs, docs, marketing pages) |

---

### **Next.js: The Most Popular React SSR Framework**

**Next.js** (by Vercel) is a full-fledged React framework built around SSR, SSG, and modern rendering strategies. Out of the box, it provides:

1. **File-based routing** — pages/routes are created automatically based on your file structure, no router configuration needed.
2. **SSR, SSG, and ISR** — multiple rendering strategies chosen per-page (ISR = Incremental Static Regeneration, which re-generates static pages in the background after deployment).
3. **API routes** — write backend endpoints alongside your frontend code, in the same project.
4. **Image optimization** — the built-in `next/image` component automatically resizes, lazy-loads, and serves modern image formats.
5. **Zero-config bundling** — Webpack/Turbopack, code-splitting, and fast refresh are all preconfigured.

---

### **Creating a Next.js App**

```bash
npx create-next-app@latest my-next-app
cd my-next-app
npm run dev
```

This scaffolds a full project with a dev server (usually at `http://localhost:3000`), TypeScript/ESLint options during setup, and either the `pages/` or `app/` directory structure depending on the options chosen.

---

### **File-Based Routing Basics**

#### **The `pages/` Directory (Pages Router — classic Next.js)**
Every file inside `pages/` automatically becomes a route:

```
pages/
  index.js         -> /
  about.js         -> /about
  blog/
    index.js       -> /blog
    [id].js        -> /blog/:id   (dynamic route)
```

```jsx
// pages/blog/[id].js
import { useRouter } from "next/router";

export default function BlogPost() {
  const router = useRouter();
  const { id } = router.query; // dynamic segment from the URL

  return <h1>Blog Post #{id}</h1>;
}
```

#### **The `app/` Directory (App Router — modern Next.js 13+)**
Newer Next.js projects default to the `app/` directory, where routing is based on nested folders, and components are **React Server Components by default**:

```
app/
  page.js               -> /
  about/
    page.js              -> /about
  blog/
    [id]/
      page.js             -> /blog/:id   (dynamic route)
```

```jsx
// app/blog/[id]/page.js
export default function BlogPost({ params }) {
  const { id } = params; // dynamic segment from the folder name
  return <h1>Blog Post #{id}</h1>;
}
```

---

### **Data Fetching Methods**

#### **`getServerSideProps` — Server-Side Rendering (Pages Router)**
Runs on **every request**, on the server. Use it when data must be fresh per request (e.g., user-specific dashboards).

```jsx
// pages/products/[id].js
export async function getServerSideProps({ params }) {
  const res = await fetch(`https://api.example.com/products/${params.id}`);
  const product = await res.json();

  return {
    props: { product }, // passed to the page component as props
  };
}

export default function ProductPage({ product }) {
  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}
```

#### **`getStaticProps` — Static Site Generation (Pages Router)**
Runs **at build time**, producing static HTML that's reused for every visitor. Use it for content that doesn't change per-request.

```jsx
export async function getStaticProps() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  return {
    props: { posts },
    revalidate: 60, // ISR: regenerate this page at most once every 60 seconds
  };
}

export default function BlogIndex({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

#### **`getStaticPaths` — Dynamic SSG Routes**
When using `getStaticProps` on a dynamic route (like `[id].js`), Next.js needs to know **which** dynamic values to pre-render at build time. `getStaticPaths` supplies that list.

```jsx
// pages/blog/[id].js
export async function getStaticPaths() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  const paths = posts.map((post) => ({ params: { id: String(post.id) } }));

  return {
    paths,
    fallback: false, // any id not in `paths` returns a 404
  };
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);
  const post = await res.json();

  return { props: { post } };
}

export default function BlogPost({ post }) {
  return <h1>{post.title}</h1>;
}
```

#### **App Router: Server Components with `async`/`await` (Next.js 13+, Modern Approach)**
In the `app/` directory, **Server Components** can fetch data directly inside the component with plain `async`/`await` — no special data-fetching function needed. This is the direction the Next.js team recommends for new projects.

```jsx
// app/products/[id]/page.js
async function getProduct(id) {
  const res = await fetch(`https://api.example.com/products/${id}`, {
    next: { revalidate: 60 }, // ISR-style caching, configured inline
  });
  return res.json();
}

export default async function ProductPage({ params }) {
  const product = await getProduct(params.id);

  return (
    <div>
      <h1>{product.name}</h1>
      <p>${product.price}</p>
    </div>
  );
}
```

Because this component runs entirely on the server and is never shipped to the browser as JavaScript, this pattern combines the simplicity of writing `async`/`await` directly in a component with the performance benefits of SSR/SSG, without needing `getServerSideProps` or `getStaticProps` at all.

| Method | Directory | Runs | Use Case |
|---|---|---|---|
| `getServerSideProps` | `pages/` | Every request, on the server | Frequently changing, per-request data |
| `getStaticProps` | `pages/` | At build time (+ optional revalidation) | Content that changes infrequently |
| `getStaticPaths` | `pages/` | At build time | Enumerates dynamic routes for `getStaticProps` |
| Server Components + `async`/`await` | `app/` | On the server, per request or cached | Modern default; replaces the above in new projects |

---

### **CSR vs SSR vs SSG Comparison Table**

| Aspect | CSR (plain React SPA) | SSR (Next.js `getServerSideProps` / Server Components) | SSG (Next.js `getStaticProps`) |
|---|---|---|---|
| **HTML generated** | In the browser, after JS runs | On the server, per request | At build time, once |
| **First paint speed** | Slower (blank until JS loads) | Fast (HTML ready immediately) | Fastest (served from CDN) |
| **SEO** | Poor without extra work | Good | Good |
| **Data freshness** | Always fresh (fetched client-side) | Always fresh (fetched per request) | Stale until rebuild/revalidation (ISR helps) |
| **Server load** | Low (static file server only) | Higher (renders on every request) | Lowest (pre-built static files) |
| **Best for** | Highly interactive apps, dashboards | Personalized or frequently changing pages | Blogs, docs, marketing pages |

---

### **Best Practices**
- Choose SSG by default for content that doesn't change per user or changes infrequently; use SSR only when data must be fresh per request.
- Use Incremental Static Regeneration (`revalidate`) to get the speed of SSG with periodically refreshed data, avoiding full rebuilds.
- In new Next.js 13+ projects, prefer the App Router with Server Components and native `async`/`await` data fetching over the older `getServerSideProps`/`getStaticProps` APIs.
- Keep Server Components server-only when they don't need interactivity; mark components `"use client"` only when they actually need hooks, state, or browser APIs.
- Use `next/image` for automatic image optimization instead of raw `<img>` tags.
- Avoid fetching the same data redundantly on both the server and client — let the server-rendered data hydrate directly into the page.
- Monitor server response times for SSR pages, since unlike SSG, every request triggers fresh rendering work.

---

### **Interview Questions**

**Q1. What is Server-Side Rendering (SSR) and why does it matter?**
SSR renders React components to HTML on the server for each request, sending fully-formed HTML to the browser before JavaScript runs. It matters because it improves first-paint speed (users see content sooner) and SEO (crawlers receive real HTML content instead of an empty shell).

**Q2. How does SSR differ from CSR?**
In CSR, the server sends a nearly empty HTML shell and the browser must download and execute JavaScript before any content appears. In SSR, the server does that rendering work per request and sends complete HTML immediately, which the browser then "hydrates" to make interactive.

**Q3. What is Static Site Generation (SSG), and how does it differ from SSR?**
SSG generates HTML once, at build time, and serves the same static files to every visitor (typically from a CDN) — no per-request server rendering. SSR regenerates HTML fresh on every request, which is more flexible for dynamic data but has higher per-request server cost.

**Q4. What does Next.js provide out of the box?**
File-based routing, multiple rendering strategies (SSR, SSG, ISR), built-in API routes for backend endpoints, automatic image optimization via `next/image`, and zero-config bundling/code-splitting.

**Q5. What is the difference between `getServerSideProps` and `getStaticProps`?**
`getServerSideProps` runs on every request, on the server, making it suitable for frequently changing or per-user data. `getStaticProps` runs once at build time (or periodically with `revalidate`), producing static HTML reused across all visitors.

**Q6. What is `getStaticPaths` used for?**
It tells Next.js which dynamic route parameters (e.g., which blog post IDs) should be pre-rendered at build time when using `getStaticProps` on a dynamic route like `[id].js`. Without it, Next.js wouldn't know which specific pages to generate statically.

**Q7. What is Incremental Static Regeneration (ISR)?**
ISR lets a statically generated page be regenerated in the background after deployment, at most once per a specified interval (via the `revalidate` option), combining the speed of static files with periodically refreshed data — without requiring a full site rebuild.

**Q8. How does data fetching differ in the Next.js App Router compared to the Pages Router?**
The App Router (Next.js 13+) uses React Server Components, which can fetch data directly inside the component using plain `async`/`await`, with caching behavior configured via `fetch` options like `next: { revalidate }`. The Pages Router instead requires exported functions like `getServerSideProps` or `getStaticProps` alongside the component.

**Q9. Why is SSR generally better for SEO than CSR?**
Search engine crawlers receive fully rendered HTML content immediately with SSR, whereas with CSR they may receive a largely empty HTML shell and have to execute JavaScript correctly to see real content — something not all crawlers do reliably.

**Q10. What is hydration in the context of SSR?**
Hydration is the process where React "attaches" to the server-rendered static HTML in the browser, adding event listeners and internal state so the already-visible markup becomes interactive, without needing to re-render everything from scratch.

**Q11. When would you choose SSR over SSG for a page?**
When the page's content must be fresh and accurate per request — for example, a user's personalized dashboard, real-time pricing, or search results — where pre-building static HTML at build time wouldn't reflect current data.

**Q12. What's the tradeoff of using SSR compared to SSG in terms of server cost?**
SSR does rendering work for every single incoming request, increasing server load and response time under high traffic. SSG does the rendering once at build time and simply serves static files afterward, which is cheaper and faster to scale, at the cost of data freshness unless combined with ISR.
