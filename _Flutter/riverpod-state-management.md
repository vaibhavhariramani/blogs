---
title: "Riverpod: Modern State Management for Flutter"
excerpt: "A compile-safe, testable way to share and rebuild state in Flutter that fixes the pain points of setState and Provider."
header:
  image: /assets/images/flutter/4.jpg
  teaser: /assets/images/flutter/1.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

If you've followed our [Flutter installation guide](/Flutter/Configuring-&-Installing-Flutter/) and built the classic counter app, you've already met `setState`. It works, but only for a single widget's own private state. The moment two widgets on opposite sides of your app need the same piece of data, `setState` stops being enough — and that's where state management libraries like Riverpod come in.

## The problem with setState and Provider

`setState` rebuilds the widget it's called in (and its children). That's fine for a button's ripple effect, but terrible for something like "is the user logged in?" which dozens of widgets across the app need to know about.

`Provider`, the library Riverpod grew out of, solved sharing state by placing values in the widget tree using `InheritedWidget` under the hood. It was a huge improvement, but it has a few sharp edges:

- It depends on `BuildContext`, so you can't read a provider outside the widget tree (e.g., inside a plain Dart class or a test easily).
- If you forget to place a `Provider` above the widget that needs it, you get a runtime exception — the compiler can't catch it for you.
- Combining multiple providers (`ProxyProvider` chains) gets verbose fast.

Riverpod, built by the same author (Remi Rousselet), keeps the good ideas from Provider — declarative, composable state — and removes the `BuildContext` dependency entirely. Providers become plain global objects, checked by the Dart compiler, readable from anywhere, and trivially mockable in tests.

## Core concepts

**Providers** are the building blocks. A provider is just a function that produces a value — and Riverpod caches that value until it's told to recompute:

- `Provider` — exposes a read-only value (e.g., a repository or config object).
- `StateProvider` — a small mutable value, handy for simple flags or filters.
- `NotifierProvider` / `StateNotifierProvider` — the workhorse for real app state; you mutate state through methods on a `Notifier` class rather than mutating it directly.
- `FutureProvider` / `StreamProvider` — wrap async data (API calls, Firestore streams) and expose loading/error/data states automatically.

**ProviderScope** wraps your whole app (`runApp(ProviderScope(child: MyApp()))`) and is what actually stores provider state.

**`ref.watch` vs `ref.read`** is the distinction beginners trip over most:

- `ref.watch(provider)` — call this inside `build()`. It subscribes the widget to the provider, so the widget rebuilds whenever the value changes.
- `ref.read(provider)` — call this inside callbacks (`onPressed`, `initState`, etc.). It reads the current value once without subscribing — you don't want a button's `onPressed` handler rebuilding the widget every time state changes.
- `ref.listen(provider, callback)` — for reacting to changes with side effects, like showing a `SnackBar`, without rebuilding the UI.

Because everything is expressed as typed Dart objects, misspelling a provider name or reading the wrong type is a compile error, not a crash three taps into a demo.

## A counter app, the Riverpod way

Add the dependency first:

```yaml
dependencies:
  flutter_riverpod: ^2.5.1
```

Define a `Notifier` that owns the counter state:

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

class Counter extends Notifier<int> {
  @override
  int build() => 0; // initial state

  void increment() => state++;
  void decrement() => state--;
}

final counterProvider = NotifierProvider<Counter, int>(Counter.new);
```

Wrap the app in a `ProviderScope`:

```dart
void main() {
  runApp(const ProviderScope(child: MyApp()));
}
```

Consume it from a `ConsumerWidget` (the Riverpod equivalent of `StatelessWidget`):

```dart
class CounterPage extends ConsumerWidget {
  const CounterPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider); // rebuilds on change

    return Scaffold(
      appBar: AppBar(title: const Text('Riverpod Counter')),
      body: Center(child: Text('Button tapped $count times')),
      floatingActionButton: FloatingActionButton(
        onPressed: () => ref.read(counterProvider.notifier).increment(),
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

Notice the split: `ref.watch` in `build()` to rebuild the `Text`, and `ref.read(...).notifier` inside `onPressed` so the button itself doesn't rebuild. This is the whole mental model of Riverpod in one example — state lives outside the widget, widgets subscribe to exactly the slice they need, and mutation happens through explicit methods you can unit test without touching the UI at all.

## Where it fits

Riverpod shines when you want compile-time safety, easy testing, and fine-grained rebuilds without dragging in a full architectural framework. If you want to see a more batteries-included alternative that bundles state management with dependency injection and routing in one package, check out our [GetX write-up](/Flutter/getx-state-management/) and compare the trade-offs for yourself.

# Made with ❤️ by Vaibhav Hariramani
