---
title: "WebAssembly (Wasm) for the Web: Running More Than Just JavaScript in the Browser"
excerpt: "A practical introduction to WebAssembly — what it is, why it lets languages like C++, Rust, and Dart run near-natively in the browser, and where it's actually used."
header:
  image: /assets/images/unsplash-gallery-image-1.jpg
  teaser: assets/images/unsplash-gallery-image-1-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

For most of the browser's history, JavaScript was the only language that could actually *run* on the web — everything else had to be compiled down to it or avoided entirely. WebAssembly (Wasm) changes that. It's a binary instruction format that browsers can execute at near-native speed, which means languages like C, C++, Rust, Go, and Dart can now ship real, performance-sensitive code straight to the browser.

## What Wasm actually is

WebAssembly is not a programming language you write by hand day-to-day — it's a low-level, portable **binary format** and a corresponding text format (`.wat`) that acts as a compilation target. You write your application in a language like Rust or C++, and a compiler (like `emscripten` or `wasm-pack`) turns it into a `.wasm` module. The browser then loads that module into a sandboxed execution environment and runs it alongside your regular JavaScript.

A few properties make this work well:

- **Near-native speed**: Wasm is a compact, stack-based bytecode designed to be decoded and executed (or JIT-compiled) extremely fast — far closer to native machine code performance than interpreted JavaScript for CPU-heavy tasks.
- **Sandboxed by design**: A Wasm module has no ambient access to the DOM, network, or filesystem. It can only do what you explicitly wire up through "imports" — functions you provide from the JavaScript side. This keeps it as safe as any other browser code.
- **Language-agnostic**: The browser doesn't know or care that your module started life as Rust or C++. It just sees standardized bytecode.
- **Interoperable with JS**: Wasm modules can call JavaScript functions and be called from JavaScript, so you typically use Wasm for the hot, computationally expensive path and keep DOM manipulation, UI, and glue code in JS.

## Why this matters beyond games

Wasm's first famous use cases were porting existing game engines (Unity, Unreal) to run in-browser, but that undersells it. The pattern generalizes to *any* workload where you want to reuse existing native code or need raw computational throughput in the browser:

- **Design and media tools**: Figma's canvas rendering engine and Photoshop's web version both rely on Wasm-compiled C++ for performance that plain JS canvas operations couldn't match.
- **Video/audio/image processing**: Codecs and filters compiled from C libraries (e.g., `ffmpeg.wasm`) run entirely client-side, avoiding a server round trip for processing.
- **Scientific computing and CAD**: AutoCAD's web version runs its decades-old C++ core compiled to Wasm rather than being rewritten from scratch.
- **Cryptography**: Password hashing and encryption libraries benefit from Wasm's predictable, near-native performance characteristics.
- **Cross-platform UI frameworks**: Flutter Web can compile your Dart application to a Wasm module (via its `dart2wasm` / `wasm-gc` compilation target) instead of JavaScript, giving Flutter apps a rendering path closer to how Flutter runs natively on mobile. If you're building with Flutter, see the dedicated write-up on this at [Flutter Web and WebAssembly](/Flutter/flutter-web-webassembly/).

The common thread: Wasm lets you bring *existing, battle-tested, performance-critical code* to the browser instead of rewriting it in JavaScript and hoping the JIT compiler optimizes it well enough.

## A simple conceptual example

You don't need Rust or C++ installed to understand the shape of a Wasm workflow. Conceptually, it looks like this:

1. **Write** a performance-sensitive function in a systems language:

```rust
// lib.rs (Rust)
#[no_mangle]
pub fn fibonacci(n: u32) -> u64 {
    if n <= 1 { return n as u64; }
    let (mut a, mut b) = (0u64, 1u64);
    for _ in 2..=n {
        let c = a + b;
        a = b;
        b = c;
    }
    b
}
```

2. **Compile** it to a `.wasm` binary using a toolchain like `wasm-pack` or `emscripten`.

3. **Load and call it from JavaScript**, using the browser's `WebAssembly` API:

```javascript
async function run() {
  const { instance } = await WebAssembly.instantiateStreaming(
    fetch("fibonacci.wasm")
  );
  console.log(instance.exports.fibonacci(40)); // near-native speed
}

run();
```

That's the entire mental model: compile once, fetch the binary like any other asset, instantiate it, and call its exported functions as if they were JavaScript functions — except the actual number-crunching happens in Wasm.

## When *not* to reach for Wasm

Wasm isn't a general replacement for JavaScript. For typical DOM manipulation, event handling, and business logic, JavaScript (or TypeScript) remains simpler to write, debug, and ship — and the DOM APIs aren't even directly accessible from inside Wasm without going through JS glue code. Reach for Wasm specifically when you have an existing native codebase to reuse, or a CPU-bound bottleneck that profiling has already confirmed JavaScript can't solve efficiently.

If you're weighing where your rendering logic should even execute — client, server, or somewhere in between — it's worth reading the companion post on [Edge Functions](/WebDevelopment/edge-functions/), since both technologies are about moving computation to where it's most efficient to run.

# Made with ❤️ by Vaibhav Hariramani
