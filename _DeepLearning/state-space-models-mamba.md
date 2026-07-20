---
title: "State Space Models and Mamba: Beyond Quadratic Attention"
excerpt: "Why attention gets expensive as sequences grow, and how Mamba borrows ideas from control theory to process long sequences in linear time."
header:
  image: images/deep_learning/6.jpg
  teaser: images/deep_learning/6.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

## The problem: attention's cost grows quadratically

Self-attention, the mechanism at the heart of the transformer (and of the [Vision Transformer](/DeepLearning/vision-transformers/) discussed elsewhere in this series), lets every token attend to every other token. That's exactly what makes it powerful — but it's also what makes it expensive. For a sequence of length `n`, computing all pairwise attention scores takes `O(n^2)` time and memory.

Double the sequence length, and attention gets roughly four times more expensive. This is manageable for a few thousand tokens, but it becomes a real bottleneck for tasks that need very long context: whole books, long audio recordings, DNA sequences, or hour-long video. At some point, the quadratic cost dominates everything else about training and running the model.

Researchers have tried many fixes — sparse attention, sliding windows, linear attention approximations — but a different family of architectures took a more fundamental detour: **State Space Models (SSMs)**, with **Mamba** as the most prominent recent example.

## Borrowing from control theory: what a state space model is

State space models originate in classical control theory and signal processing, where they're used to describe systems that evolve over time. The core idea is simple: maintain a **hidden state** that gets updated at each time step, summarizing everything relevant seen so far, and produce an output based on that state.

```
h(t) = A * h(t-1) + B * x(t)     # update the hidden state
y(t) = C * h(t)                    # produce output from the state
```

Here `x(t)` is the input at time `t`, `h(t)` is the hidden state, and `y(t)` is the output. `A`, `B`, and `C` are matrices that define how the state evolves and how outputs are read from it. If this looks familiar, it's because it's structurally similar to a recurrent neural network (RNN) — a hidden state carried forward through time, updated one step at a time.

The key difference from old-school RNNs is *how* these models are trained and computed. Classic RNNs process a sequence strictly step-by-step during training, which is slow. SSMs are designed so the recurrence can be reformulated as a **convolution** over the sequence, which can be computed in parallel — giving RNN-like linear-time inference with much more transformer-like parallel training.

## What Mamba adds: input-dependent (selective) dynamics

Early SSMs like S4 used fixed `A`, `B`, `C` matrices — the same dynamics applied uniformly regardless of the input content. That's efficient but limited: the model can't decide "this token matters a lot, remember it strongly" versus "this token is filler, mostly ignore it," because the update rule never looks at the actual input.

[Mamba](https://arxiv.org/abs/2312.00752) introduces **selective state spaces**: the `A`, `B`, `C` matrices (or more precisely, the discretization parameters controlling them) become functions of the current input, not fixed constants. This lets the model dynamically decide, token by token, how much to update or preserve its state — closer to how attention can choose to focus on some tokens over others, but without computing all pairwise interactions.

To make this input-dependent version still fast, Mamba uses a **hardware-aware parallel scan algorithm**, which computes the recurrence efficiently on GPUs despite the matrices changing at every step. The upshot: linear-time (`O(n)`) sequence processing that still captures content-dependent, selective behavior.

## Mamba versus transformers: the trade-offs

| | Transformer (attention) | Mamba (SSM) |
|---|---|---|
| Time complexity | O(n^2) | O(n) |
| Inference memory | grows with sequence length (KV cache) | constant-size hidden state |
| Long-context behavior | expensive but very direct (any token to any token) | efficient, but information is compressed into a fixed-size state |
| Parallel training | fully parallel across sequence | parallel via scan algorithm |
| Maturity / tooling | extremely mature ecosystem | newer, rapidly evolving |

The practical trade-off is compression versus directness. Attention keeps every past token individually accessible, at quadratic cost. An SSM compresses history into a fixed-size hidden state that's cheap to carry forward, but by definition it's a summary — extremely long-range, highly specific recall (like an exact quote from thousands of tokens back) can be harder for a pure SSM than for attention, which can always look directly at the original token.

This is why some recent architectures are hybrids, mixing SSM layers with a handful of attention layers to get the efficiency of one and the precise recall of the other, rather than treating it as an all-or-nothing choice.

## Where this fits in the landscape

Mamba and SSMs represent one of the more serious architectural challenges to the transformer's dominance in sequence modeling, particularly for domains with naturally long sequences. They're also efficient to adapt: the same low-rank fine-tuning ideas behind [LoRA](/DeepLearning/lora-fine-tuning/) apply to SSM-based models too, since the underlying problem — adapting a large pretrained model cheaply — doesn't depend on whether the backbone uses attention or state updates. And just as [Mixture-of-Experts](/DeepLearning/mixture-of-experts/) decouples parameter count from compute cost, SSMs decouple sequence length from quadratic cost — both are, in different ways, about making very large or very long-context models practical to actually run.

For a refresher on the deep learning fundamentals these architectures build on, see the [Introduction to Deep Learning](/DeepLearning/introduction-deep-learning/) post.

## Key takeaways

- Self-attention's O(n^2) cost with sequence length becomes a real bottleneck for very long sequences.
- State Space Models maintain a fixed-size hidden state updated over time, computable as a parallelizable convolution rather than a strict step-by-step loop.
- Mamba's contribution is making the state-update matrices input-dependent ("selective"), giving content-aware behavior while staying at linear time complexity.
- The trade-off versus transformers is compression (cheap, fixed-size state) versus directness (expensive, but exact access to every past token).

# Made with ❤️ by Vaibhav Hariramani
