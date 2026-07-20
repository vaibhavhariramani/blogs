---
title: "Zustand: Minimal Global State Management for React"
excerpt: "How Zustand gives React apps simple global state without Redux's boilerplate, and when to choose it over Context or Redux."
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

`useState` works fine while a piece of state belongs to one component. As soon as several unrelated components need to read and update the *same* value — a logged-in user, a shopping cart, a theme toggle — you need somewhere shared to put it. Zustand is a small library that gives you that shared place without the ceremony Redux is known for.

(Note: this is about **client** state — UI flags, form values, app-wide preferences. If what you're managing is actually data from a server, [TanStack Query](/React/tanstack-query/) is usually the better tool; the two solve different problems and often get used side by side.)

## The Problem: Sharing State Without the Boilerplate

React's built-in answer to shared state is Context: wrap a subtree in a `Provider`, read the value with `useContext`. It works, but it comes with friction:

* Every Context update re-renders **every** component that consumes that context, even ones that only care about a slice of the value — you have to split contexts carefully to avoid this.
* You still need `useState` or `useReducer` somewhere to actually hold the value, plus a Provider component wrapping part of your tree.
* Redux solves the re-render granularity problem, but at the cost of actions, reducers, a store setup, and (traditionally) a lot of files for what might be three pieces of state.

Zustand sits in between: a real store outside the React tree (like Redux), but defined in a few lines with no reducers, no action types, and no `Provider` wrapping required.

## Creating a Store

```bash
npm install zustand
```

```jsx
// store/useCartStore.js
import { create } from "zustand";

export const useCartStore = create((set) => ({
  items: [],
  addItem: (item) =>
    set((state) => ({ items: [...state.items, item] })),
  removeItem: (id) =>
    set((state) => ({
      items: state.items.filter((item) => item.id !== id),
    })),
  clear: () => set({ items: [] }),
}));
```

`create` takes a function that receives `set` (and optionally `get`) and returns the initial state plus any functions that update it. That's the entire store — no separate reducer, no dispatched action objects, no switch statement.

## Using the Store in Components

```jsx
import { useCartStore } from "../store/useCartStore";

function CartBadge() {
  // Only re-renders when `items.length` actually changes.
  const itemCount = useCartStore((state) => state.items.length);
  return <span>🛒 {itemCount}</span>;
}

function AddToCartButton({ product }) {
  const addItem = useCartStore((state) => state.addItem);
  return <button onClick={() => addItem(product)}>Add to cart</button>;
}
```

`useCartStore` is a hook — call it with a **selector** function, and the component only re-renders when the selected slice of state changes. `CartBadge` re-renders when `items.length` changes; it doesn't care if some unrelated field in the store changes. There's no `Provider` anywhere in the tree; both components import the same store directly and read from it.

## Why This Feels Different From Redux

* No action types, no `dispatch`, no reducer switch statement — `addItem` and `removeItem` are just functions that call `set` directly.
* No boilerplate wiring — creating the store *is* the setup. There's no separate store configuration file combining reducers, no middleware required to get started.
* State lives outside React, so components anywhere in the tree can read or update it without prop drilling or Context nesting.
* Selectors give you Redux-level render efficiency (only re-render on the slice you use) without `useSelector`'s equality-check ceremony or connecting components manually.

## When to Reach for Zustand vs. Context vs. Redux

* **`useState` / `useReducer`** — state used by one component and maybe passed a level or two down via props. Simplest option; reach here first.
* **React Context** — state that's genuinely simple and changes rarely (a theme, a locale, feature flags) shared across a subtree. Fine for low-frequency updates; avoid it for state that changes often, since consumers re-render on every change by default.
* **Zustand** — shared, frequently-changing client state used across many components that aren't necessarily nested in a clean subtree (cart contents, UI state like sidebar open/closed, multi-step form state, auth session flags). Minimal setup, good render performance via selectors, no Provider required.
* **Redux (with Redux Toolkit)** — large applications where you specifically want its ecosystem: time-travel debugging, a strict unidirectional action log, middleware for side effects, or a team convention already built around it. Redux Toolkit has significantly cut the historical boilerplate, but Zustand is still the lighter starting point for most new projects.

For a small-to-medium React app, or even a large one where the state itself isn't the bottleneck, Zustand tends to be the pragmatic middle ground: real global state, minimal code, and no architecture decisions forced on you upfront. If you're just getting comfortable with [React's own state model](/React/getting-started-with-react/) first, that's a reasonable place to stay until you actually feel the pain global state is meant to solve.

# Made with ❤️ by Vaibhav Hariramani
