---
title: "Edge Functions: Running Your Backend Logic Closer to the User"
excerpt: "Learn what edge functions actually are, why running code near the user beats a single centralized server region, and how to write one."
header:
  image: /assets/images/unsplash-gallery-image-2.jpg
  teaser: assets/images/unsplash-gallery-image-2-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

If you have ever deployed a Node.js or Python API, you already know the pattern: you pick a region — say `us-east-1` — and every request, from every user on the planet, travels to that one data center. A user in Mumbai hitting a server in Virginia pays for that distance in latency, every single time. Edge functions exist to fix exactly this problem.

## What "the edge" actually means

"The edge" refers to the network of Points of Presence (PoPs) that CDNs like Cloudflare, Vercel, and Fastly already operate in dozens of cities worldwide — the same infrastructure that used to just cache static files. An edge function is a small, stateless piece of code that runs in one of these PoPs, physically close to whoever made the request, instead of in one centralized backend region.

The key architectural difference:

- **Centralized backend**: One region. Every request travels there and back. Simple to reason about, but latency scales with distance.
- **Edge function**: Deployed to every PoP simultaneously. The request is handled by whichever PoP is geographically nearest, so latency stays low no matter where the user is.

Edge functions typically run in lightweight JavaScript runtimes (V8 isolates, not full Node.js containers) so they can spin up in milliseconds with no cold-start penalty comparable to traditional serverless functions. That speed and low overhead is *why* they're viable for logic that has to run on every single request.

## Why you'd actually use one

Edge functions aren't a replacement for your main backend — they're best for small decisions that need to happen before a page is even rendered:

- **Auth / session checks**: Reject or redirect unauthenticated users before your origin server does any real work.
- **Redirects and rewrites**: Geo-based redirects (send EU visitors to `/eu/`), locale detection, legacy URL rewrites — all without a client-side JS flash of the wrong content.
- **A/B testing**: Assign a visitor to a variant at the edge and rewrite the response, so there's no layout shift from a client-side experiment library loading late.
- **Personalization**: Read a cookie or geo header and tailor the response (currency, language, banner copy) without a round trip to your database.
- **Bot / abuse mitigation**: Inspect headers and block suspicious traffic before it ever reaches your origin.

This pairs naturally with rendering strategies like React Server Components — if you're exploring how server-rendered React decides what to compute where, see [React Server Components](/React/react-server-components/).

## A simple example

Here's a Vercel Edge Function (using the Web-standard `Request`/`Response` APIs, not Node's `http` module) that redirects visitors based on a cookie, a classic personalization use case:

```javascript
// api/greet.js
export const config = {
  runtime: "edge",
};

export default function handler(request) {
  const country = request.headers.get("x-vercel-ip-country") || "US";
  const url = new URL(request.url);

  // Redirect visitors from India to a localized landing page
  if (country === "IN" && url.pathname === "/") {
    return Response.redirect(new URL("/in", request.url), 307);
  }

  return new Response(
    JSON.stringify({ message: `Hello from the edge, visitor from ${country}!` }),
    { headers: { "content-type": "application/json" } }
  );
}
```

The Cloudflare Workers equivalent looks almost identical, because both platforms converged on the same Web Fetch API shape:

```javascript
export default {
  async fetch(request) {
    const country = request.headers.get("cf-ipcountry") || "US";
    return new Response(`Hello from the edge, visitor from ${country}!`);
  },
};
```

Notice what's *not* here: no Express app, no server to keep alive, no port to bind. You just export a function that takes a request and returns a response — the platform handles deploying it to every PoP and routing traffic to the nearest one.

## Trade-offs to keep in mind

Edge functions come with real constraints:

- **No persistent connections**: You generally can't hold a long-lived database connection open; you'd use an edge-compatible data layer (like Cloudflare D1, Vercel KV, or a database with an HTTP-based driver).
- **Limited runtime APIs**: You're in a V8 isolate, not full Node — some npm packages that rely on Node-specific APIs (`fs`, native bindings) simply won't run.
- **Execution time limits**: Edge functions are meant for fast decisions (milliseconds), not long-running jobs. Heavy computation still belongs in your regional backend.

Think of the edge as a fast, globally-distributed *gatekeeper* in front of your application — not a replacement for it. If you're hosting a static or Jekyll-based site yourself, it's worth pairing this mental model with how your hosting actually deploys — see the [GitHub Pages](/WebDevelopment/Github-pages/) post in this collection for the deployment side of that story.

# Made with ❤️ by Vaibhav Hariramani
