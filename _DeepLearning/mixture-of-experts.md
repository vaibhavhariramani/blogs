---
title: "Mixture-of-Experts (MoE): Huge Models, Cheap Inference"
excerpt: "How routing each input to only a few 'expert' sub-networks lets models grow to trillions of parameters without a proportional compute bill."
header:
  image: images/deep_learning/3.jpg
  teaser: images/deep_learning/3.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

## The problem: bigger models cost more, always

In a standard "dense" neural network, every parameter is used for every input. If you double the size of a feed-forward layer, you roughly double the compute needed to process each token, at training time and at inference time. This is the core tension behind scaling deep learning models: more parameters generally means better performance, but it also means proportionally more FLOPs, more latency, and more hardware cost per prediction.

**Mixture-of-Experts (MoE)** breaks that link. It lets a model have an enormous total parameter count while only activating a small fraction of those parameters for any single input. The model gets bigger; the inference cost per token doesn't grow at the same rate.

## The core idea: many experts, few chosen per input

An MoE layer replaces a single large feed-forward block with:

- A set of **experts** — typically several independent feed-forward networks (E1, E2, E3, ... En), each with its own weights.
- A **gating network** (also called a router) — a small learned function that looks at the input and decides which expert(s) should process it.

Instead of running the input through all N experts and combining everything, the router picks only the **top-k** experts (often k=1 or k=2) for each input token. Only those chosen experts do any computation. The rest sit idle for that token.

```python
# Conceptual MoE layer forward pass (top-2 routing)
def moe_layer(x, experts, router, k=2):
    scores = router(x)                     # shape: (num_experts,)
    top_k_idx = top_k(scores, k)            # pick the k highest-scoring experts
    weights = softmax(scores[top_k_idx])    # normalize their scores into weights

    output = 0
    for idx, weight in zip(top_k_idx, weights):
        output += weight * experts[idx](x)  # only k experts run, not all n
    return output
```

If a model has 64 experts and routes each token to 2 of them, the *active* compute per token is similar to a model 32 times smaller — even though the *total* parameter count includes all 64 experts' weights. This is why MoE models are often described by two separate numbers: total parameters (everything stored) and active parameters (what's actually computed per token).

## Why route at all? Specialization

The intuition behind MoE isn't just "compute less" — it's that different experts can specialize. One expert might become good at handling code-like tokens, another at a particular language, another at factual recall patterns, without anyone explicitly telling them to. The router learns, through training, which expert tends to handle which kind of input well, and increasingly sends similar inputs to the same expert.

This is conceptually similar to how a company doesn't send every problem to every employee — it routes the tax question to the accountant and the plumbing question to the plumber. The "employees" here are sub-networks, and the "routing memo" is a learned gating function, trained end-to-end alongside everything else via backpropagation.

## The load-balancing problem

A naive router can collapse: if one expert happens to be slightly better early in training, the router sends it more inputs, which makes it get even better (more gradient updates), which makes the router favor it even more. Eventually a handful of experts do all the work and the rest are never trained — wasting most of the model's capacity.

To prevent this, MoE training adds an **auxiliary load-balancing loss** that penalizes the router for sending too many inputs to too few experts, encouraging roughly even usage across all experts. Getting this balance right is one of the trickiest parts of training MoE models in practice — it directly determines whether you actually get N experts' worth of useful capacity or just one expert doing all the work behind a lot of dead weight.

## Where you'll see MoE in practice

MoE layers are typically swapped in for the feed-forward sub-layer inside a transformer block, while the self-attention sub-layer (the same mechanism used in the [Vision Transformer post](/DeepLearning/vision-transformers/)) stays dense. This hybrid — dense attention, sparse feed-forward — is the pattern used by models like Google's Switch Transformer and GLaM, and it's part of why some of the largest modern language models can have parameter counts in the hundreds of billions or trillions while keeping inference cost closer to that of a much smaller dense model.

MoE also pairs naturally with efficient fine-tuning techniques: once you have a large pretrained MoE backbone, adapting it to a new task cheaply is exactly the problem [LoRA](/DeepLearning/lora-fine-tuning/) is designed to solve.

## Key takeaways

- MoE splits a layer into multiple expert sub-networks plus a learned router.
- Only the top-k experts run for any given input, decoupling total parameter count from per-input compute cost.
- Routing lets experts specialize, but requires a load-balancing loss to avoid collapsing onto a few favored experts.
- MoE is usually applied to the feed-forward layers of a transformer, leaving attention dense.

For the broader context of where these architectural building blocks sit within deep learning as a field, see the [Introduction to Deep Learning](/DeepLearning/introduction-deep-learning/) post.

# Made with ❤️ by Vaibhav Hariramani
