---
title: "TanStack Query: Server-State Management for React"
excerpt: "Why fetching data with useState and useEffect gets messy fast, and how TanStack Query's useQuery hook handles caching, refetching, and loading state for you."
header:
  image: /assets/images/unsplash-gallery-image-4.jpg
  teaser: /assets/images/unsplash-gallery-image-4-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Once you've learned the basics of [React](/React/getting-started-with-react/) — components, props, `useState`, `useEffect` — fetching data from an API feels like the obvious next step. It usually looks something like this:

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => setUser(data))
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  }, [userId]);

  if (loading) return <p>Loading…</p>;
  if (error) return <p>Something went wrong.</p>;
  return <h1>{user.name}</h1>;
}
```

This works for a single component. But it doesn't scale, and here's why.

## The Problem: Server State Is Not Component State

`useState` and `useEffect` were designed for **client state** — things like "is this dropdown open" that live and die with a component. Data from an API is **server state**: it lives on a server, can be stale the moment you receive it, might be needed by multiple components at once, and can change from actions outside your control. Treating it like client state creates real problems:

* **No caching** — navigate away from `UserProfile` and back, and it re-fetches from scratch, even though nothing changed.
* **Duplicate requests** — if two components on the same page both need the same user, you either fetch it twice or thread it through props/context manually.
* **No automatic refetching** — if the tab was in the background and the data might now be stale, or the user refocuses the window, nothing tells your `useEffect` to run again.
* **Race conditions** — if `userId` changes quickly, an older `fetch` can resolve after a newer one and overwrite fresher data, unless you carefully guard against it.
* **Boilerplate** — every component that fetches data ends up rewriting the same `loading`/`error`/`data` state trio.

TanStack Query (formerly React Query) is a library built specifically to manage server state: fetching, caching, synchronizing, and updating it, so you stop reimplementing the same logic in every component.

## Setting It Up

```bash
npm install @tanstack/react-query
```

Wrap your app in a `QueryClientProvider` once, near the root:

```jsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <UserProfile userId="42" />
    </QueryClientProvider>
  );
}
```

## The `useQuery` Hook

The same `UserProfile` component, rewritten with `useQuery`:

```jsx
import { useQuery } from "@tanstack/react-query";

function fetchUser(userId) {
  return fetch(`/api/users/${userId}`).then((res) => res.json());
}

function UserProfile({ userId }) {
  const { data, isLoading, isError } = useQuery({
    queryKey: ["user", userId],
    queryFn: () => fetchUser(userId),
  });

  if (isLoading) return <p>Loading…</p>;
  if (isError) return <p>Something went wrong.</p>;
  return <h1>{data.name}</h1>;
}
```

Two things do all the work here:

* **`queryKey`** — a unique identifier for this piece of data, `["user", userId]`. TanStack Query uses it as the cache key. Any other component that calls `useQuery` with the same key gets the same cached data instantly, and only one network request is made even if ten components ask for it at once.
* **`queryFn`** — any function that returns a promise resolving to the data. TanStack Query calls it, tracks its loading/error/success state for you, and stores the result under `queryKey`.

Everything the manual version tracked by hand — `loading`, `error`, `data` — comes back as `isLoading`, `isError`, `data` (plus extras like `isFetching` for background refetches) without you writing a single `useState`.

## What You Get for Free

* **Caching** — switch away from `UserProfile` and back, and cached data renders immediately while a fresh fetch happens quietly in the background.
* **Automatic refetching** — by default, TanStack Query refetches on window refocus and network reconnect, so displayed data doesn't go stale unnoticed.
* **Deduplication** — multiple components querying the same `queryKey` at the same time share a single network request.
* **Retries** — failed requests are retried automatically with backoff, configurable per query.
* **Mutations** — a matching `useMutation` hook handles POST/PUT/DELETE calls and can invalidate related queries afterward:

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

function useUpdateUser(userId) {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (newData) =>
      fetch(`/api/users/${userId}`, {
        method: "PUT",
        body: JSON.stringify(newData),
      }),
    onSuccess: () => {
      // Refetch the user query so the UI reflects the update.
      queryClient.invalidateQueries({ queryKey: ["user", userId] });
    },
  });
}
```

## When to Reach for TanStack Query

* Any time a component fetches data from a REST or GraphQL API and you find yourself writing `loading`/`error`/`data` state by hand.
* When the same data is needed in multiple places and you don't want to prop-drill or duplicate fetches.
* Client-side data fetching in a traditional React SPA, or inside Client Components in a [Next.js App Router](/React/nextjs-app-router/) project for data that changes after the initial server render (things like live notifications, polling, or user-triggered refetches) — for the *initial* page load, Server Components fetching data directly is often simpler, and the two approaches combine well: server-render the first paint, then let TanStack Query take over for subsequent client-side updates.

If your data-fetching `useEffect`s are starting to feel repetitive or fragile, that's usually the sign it's time to bring in a dedicated server-state library rather than reaching for more `useState`.

# Made with ❤️ by Vaibhav Hariramani
