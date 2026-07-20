---
title: "Impeller: Flutter's Modern Rendering Engine Explained"
excerpt: "Why Flutter rebuilt its rendering pipeline from scratch, and how Impeller kills the shader-compilation jank that plagued Skia."
header:
  image: /assets/images/flutter/3.jpg
  teaser: /assets/images/flutter/2.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Once you've got Flutter [installed and running](/Flutter/Configuring-&-Installing-Flutter/), everything you build gets drawn to the screen by something under the hood called the rendering engine. For most of Flutter's life that engine was Skia, the same 2D graphics library Chrome and Android use. Since 2023, Flutter has been shipping a purpose-built replacement called **Impeller** — and understanding why it exists tells you a lot about how Flutter actually gets pixels on screen.

## The problem: shader compilation jank

Skia is a general-purpose graphics library. To draw a shadow, a gradient, or a clip, it generates a small GPU program called a **shader** on the fly, compiles it, and hands it to the GPU driver. That compilation step happens the *first time* a particular visual effect is drawn — which in practice means the first time your app renders a new kind of widget, animation, or transition.

The result was a well-known Flutter complaint: an animation runs buttery smooth on the second and third playthrough, but stutters badly the very first time, because the driver is compiling shaders mid-frame. This is "shader compilation jank," and no amount of `const` widgets or `RepaintBoundary` tuning fixes it, because the problem lives below your Dart code entirely, in the graphics driver.

Flutter's old workaround was **shader warm-up** — pre-rendering offscreen frames at app startup to force early compilation. It helped but was fragile: you had to manually maintain a list of representative shaders, and it never covered every case.

## The fix: precompiled shaders, ahead of time

Impeller takes a fundamentally different approach: instead of generating shaders at runtime, it ships with a small, fixed library of shaders **compiled at build time**, before the app ever runs. Rendering operations are expressed as combinations of these precompiled shaders rather than bespoke ones generated on demand. There is no runtime shader compilation step left to cause a hitch.

Architecturally, Impeller also talks to the GPU through modern, lower-overhead APIs instead of Skia's abstraction layer:

- **Metal** on iOS and macOS
- **Vulkan** on Android (with an OpenGL ES fallback on older devices that don't support Vulkan)

This is also why Impeller tends to reduce CPU overhead generally, not just eliminate jank: modern GPU APIs let Flutter submit work with fewer driver round-trips than the older OpenGL/Skia path required.

## Platform rollout status

Impeller's rollout has been gradual and platform-by-platform, which is worth knowing if you're debugging a rendering issue and wondering "is this even the engine I think it is?":

- **iOS**: Impeller became the default renderer starting with Flutter 3.10 (2023). It's the mature, stable path on iOS.
- **Android**: Impeller (on Vulkan) became the default starting with Flutter 3.29, after being available as an opt-in for several releases before that.
- **Desktop and web**: support has been arriving later and is less mature than mobile; the web renderer story (CanvasKit/HTML/WebAssembly) is largely separate from Impeller and is covered in our [Flutter Web and WebAssembly post](/Flutter/flutter-web-webassembly/).

Because the engine evolves quickly, always cross-check the current default for your target platform against the official Flutter engine documentation before shipping — what's experimental one release can be default the next.

## Checking which engine you're using

You can confirm Impeller is active by looking at the startup log or `flutter run -v` output, which prints the selected backend. On older Flutter versions you could explicitly toggle it:

```bash
# Force-enable (older Flutter versions where it wasn't default yet)
flutter run --enable-impeller

# Explicitly opt out and fall back to Skia
flutter run --no-enable-impeller
```

On current stable Flutter releases where Impeller is the default, these flags are mostly unnecessary — they exist mainly as an escape hatch if you hit a rendering regression and need to confirm whether Impeller is the cause.

## Why this matters even if you never touch a shader

You'll never write a shader by hand in a typical Flutter app, but Impeller affects real decisions:

- **Custom shaders / `FragmentProgram`**: if your app uses hand-written fragment shaders (frosted glass effects, custom gradients), test them specifically on Impeller — the compilation model differs from Skia's.
- **Profiling**: DevTools' "Rendering" tab and the frame timeline look different once shader compilation stalls disappear from the trace — don't be surprised if your old jank-hunting habits need updating.
- **Minimum OS/driver versions**: Vulkan support on Android varies by device age, which is why the OpenGL ES fallback still exists.

Impeller is a good example of Flutter solving a performance problem at the engine level rather than asking app developers to work around it — the kind of change that just makes every app a little smoother without a single line of your Dart code changing.

# Made with ❤️ by Vaibhav Hariramani
