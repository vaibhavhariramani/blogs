---
title: "React Server Components: Rendering React on the Server"
excerpt: "Learn how React Server Components move rendering work off the client, shrink your JavaScript bundle, and change how you think about data fetching."
header:
  image: /assets/images/unsplash-gallery-image-2.jpg
  teaser: /assets/images/unsplash-gallery-image-2-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

If you learned React the traditional way — components that run in the browser, `useState`, `useEffect`, a bundle shipped to the client — React Server Components (RSC) will feel like a different mental model. They are not a new syntax, they are a new *place* for a component to run. Once you understand server vs. client rendering, features like the [Next.js App Router](/React/nextjs-app-router/) make a lot more sense, since App Router is built directly on top of RSC.

## The Problem RSC Solves

In a classic client-rendered React app, every component — even a static list of blog post titles that never changes after load — is bundled up as JavaScript, sent to the browser, and executed there. The browser has to download React itself, download your component code, run it, and only then does it fetch the data the component needs. More components mean a bigger bundle, and a bigger bundle means a slower page, especially on a phone with a weak connection.

A lot of components don't actually need to be interactive. They fetch some data and render markup. They never call `useState`, never attach an `onClick`. Shipping their code to the client is pure waste. React Server Components let you mark these components as **server components**: they render entirely on the server, produce HTML/RSC payload, and their JavaScript never reaches the browser at all.

## Server Components vs. Client Components

* **Server Components** (the default in a framework like Next.js App Router) run only on the server — at request time or build time. They can talk directly to a database, read files, or call internal APIs with `async`/`await`, right inside the component body. Their code is never sent to the browser.
* **Client Components** run in the browser (and are also pre-rendered on the server once, for fast first paint). They're the components you already know — they can use `useState`, `useEffect`, event handlers, and browser-only APIs.

The boundary between the two is a single directive: `"use client"` at the top of a file. Everything below that line — and everything it imports — becomes part of the client bundle. Everything else stays on the server by default.

## A Simple Example

Imagine a page that shows a list of posts fetched from a database, with a "like" button on each post.

**`PostList.jsx` — a Server Component (no directive needed):**

```jsx
// Runs only on the server. Talking to a database directly is fine here.
import { db } from "../lib/db";
import LikeButton from "./LikeButton";

export default async function PostList() {
  const posts = await db.post.findMany();

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <h3>{post.title}</h3>
          <LikeButton postId={post.id} initialLikes={post.likes} />
        </li>
      ))}
    </ul>
  );
}
```

**`LikeButton.jsx` — a Client Component:**

```jsx
"use client";

import { useState } from "react";

export default function LikeButton({ postId, initialLikes }) {
  const [likes, setLikes] = useState(initialLikes);

  async function handleClick() {
    setLikes((n) => n + 1);
    await fetch(`/api/posts/${postId}/like`, { method: "POST" });
  }

  return <button onClick={handleClick}>👍 {likes}</button>;
}
```

Notice what happened: `PostList` fetches data with a plain `await db.post.findMany()` — no `useEffect`, no loading state, no client-side data-fetching library needed, because it runs on the server where the database is reachable and there's no "loading" — the component simply doesn't render until the data is ready. `LikeButton` needs interactivity (`onClick`, `useState`), so it's marked `"use client"` and its code does ship to the browser — but that's the *only* JavaScript sent for this feature. The rest of the page's markup arrives pre-rendered.

## Why This Matters

* **Smaller client bundles** — only components that truly need interactivity ship JavaScript. A page with mostly static content can have a client bundle close to zero.
* **Simpler data fetching** — server components can `await` data directly; no client-side loading/error juggling for content that never needed to be interactive in the first place.
* **Security** — secrets, database credentials, and internal API calls stay on the server since that code never reaches the browser.
* **Composability** — you can pass a server component as a `children` prop into a client component (e.g., a client-side modal shell wrapping server-rendered content), letting you mix and match rendering locations.

## Things to Keep in Mind

* Server components cannot use `useState`, `useEffect`, or any browser-only API — they simply don't run in a place that has those.
* Client components can still receive data as props from server components; they just can't `import` a server component's internals or fetch server-only resources directly.
* You don't manually configure this in Next.js's App Router — it's the default. Adding `"use client"` is how you opt *into* the browser, not the other way around.

RSC on its own is just a rendering model; frameworks are what make it usable end-to-end. The [Next.js App Router](/React/nextjs-app-router/) is the most common way to actually build with Server Components today, wiring them up with file-based routing, layouts, and streaming. If you're still comfortable with basic components and hooks, it's worth reviewing [Getting Started with React](/React/getting-started-with-react/) first — RSC builds on that foundation rather than replacing it.

# Made with ❤️ by Vaibhav Hariramani
