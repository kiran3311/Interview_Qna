
Perfect timing 🚀
These four — **CSR, SSR, SSG, ISR** — are the **rendering strategies** in Next.js (and modern web apps). Let’s break them down step by step with examples.

---

# 🔹 1. **CSR – Client-Side Rendering**

👉 Rendering happens **only in the browser**. The server sends a bare HTML file with an empty `<div id="root">`, then React loads, fetches data with `useEffect`/API calls, and fills the page.

✅ Pros:

* Fully dynamic on client side.
* Great for logged-in dashboards, highly interactive apps.

❌ Cons:

* Slower **First Paint** (bad for SEO) since user sees a blank screen until JS runs.
* SEO is poor unless you add extra steps.

### Example:

```jsx
"use client"; 
import { useState, useEffect } from "react";

export default function UsersPage() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then((res) => res.json())
      .then(setUsers);
  }, []);

  return <pre>{JSON.stringify(users, null, 2)}</pre>;
}
```

---

# 🔹 2. **SSR – Server-Side Rendering**

👉 Rendering happens **on each request at the server**.
The server fetches data, builds the HTML, and sends it to the browser.
Next.js in **App Router** (`app/`) does SSR by default with `fetch` inside an `async` component.

✅ Pros:

* SEO friendly (Google sees full HTML).
* Fresh data on every request.

❌ Cons:

* Slower than static pages because rendering happens **on every request**.

### Example:

```jsx
// app/users/page.js
export default async function UsersPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users", {
    cache: "no-store", // disables caching → SSR
  });
  const users = await res.json();

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

---

# 🔹 3. **SSG – Static Site Generation**

👉 Rendering happens **at build time** (when you run `next build`).
The HTML is generated once, then served from CDN.

✅ Pros:

* Super fast (just static files).
* Cheap to serve.
* Great for content that rarely changes (docs, blogs, landing pages).

❌ Cons:

* Data gets stale unless you rebuild the site.

### Example:

```jsx
// app/users/page.js
export default async function UsersPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users", {
    cache: "force-cache", // default → SSG
  });
  const users = await res.json();

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

---

# 🔹 4. **ISR – Incremental Static Regeneration**

👉 Hybrid of **SSG + revalidation**.
Pages are pre-rendered like SSG, but Next.js can **regenerate them in the background** at intervals (`revalidate`).

✅ Pros:

* Best of both worlds (fast + keeps data fresh).
* Ideal for blogs, e-commerce product pages.

❌ Cons:

* Next request after cache expiry might be slower (because regeneration happens).

### Example:

```jsx
// app/users/page.js
export default async function UsersPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users", {
    next: { revalidate: 60 }, // regenerate page every 60s
  });
  const users = await res.json();

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

---

# 📊 Quick Comparison

| Strategy | Render Time                     | Data Freshness      | SEO         | Use Case                               |
| -------- | ------------------------------- | ------------------- | ----------- | -------------------------------------- |
| **CSR**  | Client                          | Always fresh        | ❌ Bad       | Dashboards, apps needing interactivity |
| **SSR**  | On every request                | Always fresh        | ✅ Good      | News, live data                        |
| **SSG**  | Build time                      | Stale until rebuild | ✅ Excellent | Blogs, docs, landing pages             |
| **ISR**  | Build + background regeneration | Fresh (with delay)  | ✅ Excellent | E-commerce, blogs with updates         |

---

👉 
