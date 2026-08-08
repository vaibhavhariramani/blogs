---
title: "Large Language Models: From Tokens to Text"
excerpt: "LLMs are, mechanically, a stack of transformer layers predicting one token at a time. Here's how that simple idea, scaled up, produces something that can hold a conversation."
header:
  image: /images/deep_learning/2.jpg
  teaser: /images/deep_learning/dp1.png
sidebar:
  - title: "Role"
    text: "MlOps Engineer,Open Source Contributor"
  - title: "Responsibilities"
    text: "Building scalable and reliable infrastructure for ML model training and deployment."
---

We've covered how a [neural network learns](/DeepLearning/neural-networks/) and how [tokenization](/DeepLearning/tokenization/) turns text into numbers a model can process. A Large Language Model (LLM) is what you get when you combine those with one more idea: predict the next token, over and over, at a massive scale.

## The Core Task: Next-Token Prediction

Strip away the scale and the impressive demos, and an LLM's training objective is almost boringly simple: given a sequence of tokens, predict the probability distribution over what token comes next.

```
Input:  "The capital of France is"
Output: probability distribution over the entire vocabulary
        (e.g. "Paris": 0.87, "a": 0.02, "located": 0.01, ...)
```

Train that objective on a large enough slice of the internet, books, and code, and the model is forced to implicitly learn grammar, facts, reasoning patterns, and style — not because anyone labeled those things, but because getting good at "predict the next word" requires understanding all of it.

## The Architecture: Decoder-Only Transformers

Most LLMs (GPT, Llama, Claude, Gemini) use a **decoder-only transformer** — a stack of layers, each built from two pieces:

1. **Self-attention** — for each token, look back at every previous token in the sequence and decide how much each one matters for predicting what comes next. This is what lets a model connect "it" back to the right noun three sentences earlier.
2. **Feed-forward network** — a small [neural network](/DeepLearning/neural-networks/) applied to each token's representation independently, adding capacity to transform the information attention gathered.

Stack dozens of these layers, and by the final layer the model has built up a rich representation of "everything relevant to predicting the next token," which gets converted into that probability distribution over the vocabulary.

The **causal** part of "causal self-attention" matters: at generation time, a token can only attend to tokens before it, never after — otherwise predicting the next word would just mean copying the answer.

## Pretraining, Fine-tuning, and Alignment

An LLM goes through several distinct training stages, not just one:

* **Pretraining** — next-token prediction on a huge, mostly unlabeled text corpus. This is where the bulk of the compute cost and the model's raw knowledge comes from.
* **Supervised fine-tuning (SFT)** — training on curated examples of good instruction-following behavior, to shape a raw pretrained model into something that responds like an assistant rather than just continuing text.
* **RLHF / preference tuning** — using human (or AI) preference judgments to further steer the model toward helpful, harmless responses — this is the stage most responsible for a model "feeling" aligned rather than just fluent.

[LoRA fine-tuning](/DeepLearning/lora-fine-tuning/) is one popular technique for that second and third stage — adapting a pretrained model cheaply instead of retraining it from scratch.

## Generating Text: Autoregression

At inference time, an LLM generates one token at a time, feeding each output back in as input for the next step:

```python
tokens = tokenizer.encode("The weather today is")
for _ in range(20):
    next_token_probs = model(tokens)          # forward pass
    next_token = sample(next_token_probs)      # e.g. greedy, top-k, or nucleus sampling
    tokens.append(next_token)
```

This is also why LLMs can feel slow on long responses — generating token 500 requires having already generated tokens 1 through 499, one forward pass at a time.

## Scale, Cost, and Efficiency Tricks

Naive attention gets expensive fast because its cost grows quadratically with sequence length, which is why techniques like [Mixture-of-Experts](/DeepLearning/mixture-of-experts/) (activate only a fraction of parameters per token) and [state space models like Mamba](/DeepLearning/state-space-models-mamba/) (process long sequences without quadratic cost) exist — both are attempts to keep scaling LLMs without scaling inference cost at the same rate.

## Where LLMs Fall Short on Their Own

A model trained purely on next-token prediction only "knows" what was in its training data, frozen at whatever point that data was collected — it can't look anything up, and it will confidently generate plausible-sounding but wrong answers (hallucination) when it doesn't actually know something. That gap is exactly what [Retrieval-Augmented Generation](/DeepLearning/retrieval-augmented-generation/) is built to close.

# Made with ❤️ by Vaibhav Hariramani
