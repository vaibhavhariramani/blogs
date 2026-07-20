---
title: "Core Web Vitals Explained: LCP, INP, and CLS"
excerpt: "What Google's Core Web Vitals actually measure, why they affect both SEO and real user experience, and concrete steps to improve each one."
header:
  image: /assets/images/mm-browser-mockups.png
  teaser: assets/images/mm-browser-mockups.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

"Make the site faster" is vague enough to be useless as an engineering goal. Core Web Vitals exist to make performance measurable: three specific metrics, each targeting a distinct moment of user frustration — the page taking too long to show something meaningful, the page freezing when clicked, or content jumping around while it loads.

## Why these metrics exist

Google introduced Core Web Vitals because older performance metrics (like total load time or `DOMContentLoaded`) didn't correlate well with what users actually *feel* when using a site. You can have a fast `load` event and still feel like a page is janky and unresponsive. Core Web Vitals are field metrics — measured from real user visits, not just lab conditions — and since 2021 they've been a confirmed Google Search ranking signal for page experience. So they matter for two separate reasons: **SEO** (search visibility) and **UX** (whether real users stick around or bounce).

## Largest Contentful Paint (LCP) — loading

**What it measures**: The render time of the largest image or text block visible within the viewport, from when the page starts loading. It's a proxy for "when did the main content actually become visible," which matters far more to users than "when did every last byte finish downloading."

**Good score**: 2.5 seconds or less.

**What hurts it**: Slow server response times, render-blocking CSS/JS, large unoptimized images, and client-side rendering that delays content until JS finishes executing.

**How to improve it**:
- Optimize and correctly size images; serve modern formats (WebP/AVIF) and use `<img fetchpriority="high">` or preload hints for the hero image.
- Reduce server response time (TTFB) — this is where CDN edge caching and edge functions genuinely help, since content is served from a nearby PoP instead of a distant origin. See the [Edge Functions](/WebDevelopment/edge-functions/) post for how that works.
- Eliminate render-blocking resources: defer non-critical CSS/JS, inline critical CSS.
- Use a CDN and pre-render/SSR content instead of relying on client-side JS to paint the largest element.

## Interaction to Next Paint (INP) — responsiveness

**What it measures**: How long the page takes to visually respond after a user interaction (click, tap, key press), measured across the entire page visit and reporting close to the worst-case interaction. It replaced the older First Input Delay (FID) in March 2024 because FID only measured the *first* interaction's delay, while INP captures responsiveness throughout the whole session — including the third or tenth click, which is often where slow pages actually reveal themselves.

**Good score**: 200 milliseconds or less.

**What hurts it**: Long JavaScript tasks blocking the main thread, heavy event handler logic, large component re-renders, and third-party scripts (ads, analytics, chat widgets) monopolizing the main thread.

**How to improve it**:
- Break up long JavaScript tasks ("long tasks" over 50ms) using techniques like `scheduler.yield()` or chunking work so the main thread can breathe between steps.
- Debounce or defer expensive work triggered by input events; do the minimum needed to paint a visual response immediately, then finish the rest.
- Audit and lazy-load third-party scripts — a chat widget shouldn't be able to freeze your checkout button.
- Avoid unnecessary re-renders in frameworks like React by memoizing components and stabilizing props/callbacks.

## Cumulative Layout Shift (CLS) — visual stability

**What it measures**: The total, unexpected movement of visible elements during the page's lifecycle. Anyone who's tried to tap a button only to have an ad load in above it and shift the whole page down has experienced high CLS firsthand.

**Good score**: 0.1 or less.

**What hurts it**: Images and ads without reserved dimensions, web fonts that swap in and reflow text (FOIT/FOUT), and content injected above existing content without reserving space first.

**How to improve it**:
- Always set explicit `width` and `height` (or `aspect-ratio` in CSS) on images and video elements so the browser reserves space before the asset loads.
- Reserve space for ad slots and embeds up front instead of letting them push content when they load.
- Use `font-display: optional` or preload web fonts to minimize layout shifts from font swapping.
- Never insert new content above existing content in response to a user action unless it's a direct result of that interaction (e.g., an accordion expanding is fine; an unrelated banner popping in is not).

## Measuring what you actually ship

Lab tools like Lighthouse and PageSpeed Insights are great for catching regressions before deploy, but Core Web Vitals as a ranking signal are based on real-user field data collected via the Chrome User Experience Report (CrUX) — visible in Google Search Console's "Core Web Vitals" report. Treat lab scores as your early warning system and field data as the source of truth for what your actual visitors experience.

If your site is a static or Jekyll-based build like this one, most of the LCP and CLS battle is already half-won by having minimal JavaScript and pre-rendered HTML — see the [GitHub Pages](/WebDevelopment/Github-pages/) post for how that kind of static hosting pipeline works.

# Made with ❤️ by Vaibhav Hariramani
