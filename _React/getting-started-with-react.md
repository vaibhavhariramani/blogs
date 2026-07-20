---
title: "Getting Started with React"
excerpt: "A beginner-friendly introduction to React: what it is, why it exists, and how to build your first component."
header:
  image: /assets/images/unsplash-gallery-image-1.jpg
  teaser: /assets/images/unsplash-gallery-image-1-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

# What is React?

[React](https://react.dev/) is a JavaScript library for building user interfaces out of small, reusable pieces called **components**. Instead of manipulating the DOM directly, you describe *what* the UI should look like for a given state, and React takes care of updating the page efficiently when that state changes.

## Why React?

* **Component-based** — build encapsulated pieces of UI that manage their own state, then compose them into complex interfaces.
* **Declarative** — describe the desired UI and let React handle the DOM updates.
* **Huge ecosystem** — routing, state management, and tooling libraries exist for almost every need.

## Your First Component

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

export default Greeting;
```

A React component is just a JavaScript function that returns markup (JSX). Props (like `name` above) let you pass data into a component from its parent.

## Managing State with Hooks

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

`useState` gives a component memory that persists between renders, and calling the setter schedules a re-render with the new value.

## Where to Go Next

* Official docs: [react.dev/learn](https://react.dev/learn)
* Try it in the browser with [CodeSandbox](https://codesandbox.io/) — no local setup required.
* Once comfortable with components and hooks, look into a framework like [Next.js](https://nextjs.org/) for routing, data fetching, and deployment.

More React posts — covering hooks in depth, component patterns, and connecting to APIs — are on the way in this series.

# Made with ❤️ by Vaibhav Hariramani
