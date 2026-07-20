---
title: "GetX: State Management, DI, and Routing in One Package"
excerpt: "GetX bundles reactive state management, dependency injection, and BuildContext-free navigation into a single lightweight library."
header:
  image: /assets/images/flutter/9.png
  teaser: /assets/images/flutter/12.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Most Flutter state management libraries solve one problem well — Riverpod and Provider focus on state, `go_router` focuses on navigation, `get_it` focuses on dependency injection. GetX takes the opposite approach: it's one package that tries to solve state management, dependency injection, and routing all at once, with as little boilerplate as possible.

If you haven't set up a Flutter project yet, see our [installation walkthrough](/Flutter/Configuring-&-Installing-Flutter/) first.

## What GetX actually offers

**1. Reactive state management.** You mark a variable as observable with `.obs`, and any widget wrapped in `Obx` that reads it rebuilds automatically when it changes — no `StatefulWidget`, no `notifyListeners()`, no provider wiring.

**2. Dependency injection.** `Get.put()`, `Get.find()`, and `Get.lazyPut()` act as a simple service locator. You register a controller or service once, and any part of the app can retrieve the *same instance* without passing it down through constructors or the widget tree.

**3. Route management without BuildContext.** `Get.to()`, `Get.back()`, `Get.snackbar()`, and `Get.dialog()` all work from anywhere — a controller, a service, a callback — because GetX keeps its own reference to the navigator instead of requiring a `BuildContext`. This is convenient, though it's also the single most debated design choice in GetX (more on that below).

## A counter app with GetX

Add the dependency:

```yaml
dependencies:
  get: ^4.6.6
```

Replace `MaterialApp` with `GetMaterialApp` so GetX's routing and overlay features (snackbars, dialogs) work app-wide:

```dart
void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: 'GetX Counter',
      home: const CounterPage(),
    );
  }
}
```

Define a controller — a plain Dart class, no `extends State` needed:

```dart
import 'package:get/get.dart';

class CounterController extends GetxController {
  final count = 0.obs; // observable int

  void increment() => count.value++;
}
```

Register it and consume it in the UI:

```dart
class CounterPage extends StatelessWidget {
  const CounterPage({super.key});

  @override
  Widget build(BuildContext context) {
    final controller = Get.put(CounterController());

    return Scaffold(
      appBar: AppBar(title: const Text('GetX Counter')),
      body: Center(
        child: Obx(() => Text('Button tapped ${controller.count.value} times')),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: controller.increment,
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

`Get.put` registers the controller instance the first time this widget builds; `Obx` subscribes only to the `.obs` values actually read inside it, so only the `Text` rebuilds when `count` changes — not the whole `Scaffold`. Navigating away and calling `Get.to(() => OtherPage())` or showing `Get.snackbar('Hi', 'no context needed')` works from anywhere, including from inside `CounterController` itself, without threading a `BuildContext` through.

## How GetX compares to Riverpod, Provider, and Bloc

| | GetX | Riverpod | Provider | Bloc |
|---|---|---|---|---|
| Scope | State + DI + routing | State (DI-adjacent) | State (DI-adjacent) | State only |
| Boilerplate | Very low | Low–medium | Medium | High |
| BuildContext needed | No | No (providers), yes for widgets | Yes | Yes for widgets |
| Compile-time safety | Weaker — relies on runtime lookups (`Get.find`) | Strong — typed providers | Medium | Strong — typed events/states |
| Testability | Good, but globals need care | Very good — easy to override providers | Good | Very good — designed around testing |
| Learning curve | Low | Medium | Low–medium | Medium–high |
| Common criticism | Global service-locator pattern can hide dependencies; "magic" | More setup for trivial cases | Verbose for deeply nested state | Verbose for simple state |

The honest way to frame it: GetX optimizes for speed of writing code and gets you shipping fast, especially for small-to-mid apps or solo projects. Riverpod and Bloc optimize for long-term maintainability and testability in larger codebases — Riverpod by keeping everything explicit and compiler-checked (see our [Riverpod deep dive](/Flutter/riverpod-state-management/)), Bloc by enforcing a strict, predictable event-to-state contract. Provider sits in between as the simpler ancestor Riverpod was built to replace.

None of these are objectively "correct" — a solo developer prototyping an MVP and a team maintaining a banking app have very different priorities, and the Flutter ecosystem deliberately supports both ends of that spectrum.

# Made with ❤️ by Vaibhav Hariramani
