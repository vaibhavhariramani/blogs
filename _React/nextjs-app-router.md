---
title: "Next.js App Router: File-Based Routing Meets Server Components"
excerpt: "A practical guide to the Next.js App Router — the app/ directory, nested layouts, and how it's built on top of React Server Components."
header:
  image: /assets/images/unsplash-gallery-image-3.jpg
  teaser: /assets/images/unsplash-gallery-image-3-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Once you've built a few components with plain [React](/React/getting-started-with-react/), the next question is usually: how do multiple pages, navigation, and data fetching fit together? Next.js answers that with the **App Router** — a routing system built around the `app/` directory that also happens to be the primary way most developers use [React Server Components](/React/react-server-components/) in practice.

## File-Based Routing in `app/`

Instead of configuring routes in a router library, the App Router derives routes from your folder structure inside `app/`. Each folder is a URL segment, and a special `page.js` (or `.tsx`) file inside a folder makes that segment a visitable route.

```
app/
├── layout.js          # Root layout — wraps every page
├── page.js             # Route: /
├── about/
│   └── page.js         # Route: /about
├── blog/
│   ├── layout.js       # Layout for all /blog/* routes
│   ├── page.js         # Route: /blog
│   └── [slug]/
│       └── page.js     # Route: /blog/my-first-post (dynamic segment)
└── dashboard/
    ├── layout.js
    ├── page.js         # Route: /dashboard
    └── settings/
        └── page.js     # Route: /dashboard/settings
```

A folder named in square brackets, like `[slug]`, defines a **dynamic segment** — `app/blog/[slug]/page.js` matches `/blog/anything-here`, and the value is available to the page as a parameter.

```jsx
// app/blog/[slug]/page.js
export default async function BlogPost({ params }) {
  const { slug } = await params;
  const post = await getPostBySlug(slug);

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </article>
  );
}
```

This is a Server Component by default — no `"use client"` directive — so it can `await` data directly, the same way described in the [React Server Components](/React/react-server-components/) post.

## Layouts and Nesting

A `layout.js` file wraps every `page.js` beneath it in the folder tree, and — crucially — it **doesn't re-render when you navigate between sibling pages**. That makes layouts the natural place for navigation bars, sidebars, or anything that should persist across route changes.

```jsx
// app/dashboard/layout.js
export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard">
      <nav>
        <a href="/dashboard">Overview</a>
        <a href="/dashboard/settings">Settings</a>
      </nav>
      <main>{children}</main>
    </div>
  );
}
```

Layouts nest: the root `app/layout.js` wraps everything (it's the one place you must render `<html>` and `<body>`), `app/dashboard/layout.js` wraps everything under `/dashboard`, and so on. Navigating from `/dashboard` to `/dashboard/settings` only re-renders the `page.js` content — the dashboard nav in the layout stays mounted.

## How This Builds on Server Components

Before the App Router, Next.js's Pages Router (`pages/` directory) rendered everything as a client-oriented React tree, with data fetched via functions like `getServerSideProps`. The App Router instead treats **every component in `app/` as a Server Component unless you opt out**. That means:

* `page.js` and `layout.js` files can be `async` functions that fetch data directly with `await`, with no separate data-fetching API required.
* Only the leaf components you explicitly mark `"use client"` — a like button, a form, a dropdown — ship JavaScript to the browser.
* Next.js streams the server-rendered HTML as it becomes ready, so a slow data fetch in one part of the page doesn't block the rest from showing up (using `loading.js` files or `<Suspense>`).

A `loading.js` file dropped next to a `page.js` is shown automatically while that page's data is being fetched:

```
app/dashboard/
├── layout.js
├── page.js       # slow data fetch inside
└── loading.js    # shown instantly while page.js awaits its data
```

```jsx
// app/dashboard/loading.js
export default function Loading() {
  return <p>Loading dashboard…</p>;
}
```

## Client Components Still Fit In

Interactive pieces work exactly as described in the Server Components post — add `"use client"` at the top of the file:

```jsx
// app/dashboard/settings/RefreshButton.js
"use client";

import { useState } from "react";

export default function RefreshButton() {
  const [refreshing, setRefreshing] = useState(false);

  return (
    <button
      disabled={refreshing}
      onClick={() => {
        setRefreshing(true);
        location.reload();
      }}
    >
      {refreshing ? "Refreshing…" : "Refresh"}
    </button>
  );
}
```

You'd import and render `<RefreshButton />` from a server-rendered `page.js` just like any other component — Next.js takes care of drawing the line between what's sent to the browser and what stays on the server.

## When to Reach for the App Router

* Starting a new Next.js project today — the App Router is the recommended default, with the older Pages Router kept mainly for existing codebases.
* You want nested layouts that persist across navigation without re-fetching or re-mounting shared UI.
* You want server-side data fetching co-located with the component that needs it, instead of a separate `getServerSideProps` function.

The App Router is what turns React Server Components from a rendering concept into an actual application structure — routes, layouts, and streaming all fall out of the same `app/` directory convention.

# Made with ❤️ by Vaibhav Hariramani
