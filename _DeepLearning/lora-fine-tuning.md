---
title: "LoRA: Fine-tuning Giant Models Without Retraining Them"
excerpt: "Why updating billions of frozen weights is wasteful, and how a couple of small low-rank matrices can do the job instead."
header:
  image: images/deep_learning/4.jpg
  teaser: images/deep_learning/4.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

## The problem: full fine-tuning doesn't scale

Once a large model has been pretrained — whether it's a language model or a [Vision Transformer](/DeepLearning/vision-transformers/) — adapting it to a specific task traditionally meant **full fine-tuning**: running backpropagation through the entire network and updating every single weight. For a model with a few million parameters, that's fine. For a modern model with billions of parameters, it becomes a serious problem:

- **Memory.** Training requires storing gradients and optimizer states (like Adam's momentum terms) for every parameter, often 2-4x the memory of the weights themselves. A 7-billion-parameter model can need well over 50GB of GPU memory just to fine-tune, even before accounting for activations.
- **Storage.** If you want a separate fine-tuned model per task or per customer, full fine-tuning means storing a full copy of the entire model for each one — impractical at scale.
- **Risk of catastrophic forgetting.** Updating every weight can erode general capabilities the base model learned during pretraining, especially with small task-specific datasets.

**LoRA (Low-Rank Adaptation)**, introduced in the paper ["LoRA: Low-Rank Adaptation of Large Language Models"](https://arxiv.org/abs/2106.09685), addresses all three problems with a simple observation: the *change* needed to adapt a pretrained weight matrix to a new task tends to have low "intrinsic rank" — meaning it can be well-approximated by a much smaller matrix decomposition, even though the original weight matrix is huge.

## The core idea: freeze the big matrix, train a small delta

For a pretrained weight matrix `W` (say, of shape d x d) inside a transformer layer, full fine-tuning learns an update `ΔW` of the same shape, added to `W`. LoRA instead freezes `W` entirely and represents `ΔW` as the product of two much smaller matrices:

```
ΔW = A x B

W:   shape (d, d)         -- frozen, never updated
A:   shape (d, r)          -- trainable
B:   shape (r, d)          -- trainable, r << d
```

`r` is the **rank** of the adaptation — often something small like 4, 8, or 16, compared to a `d` that might be 4096 or larger. During the forward pass, the output becomes:

```
output = x @ W + x @ A @ B    # W frozen, only A and B receive gradients
```

Because `r` is tiny relative to `d`, the number of trainable parameters drops dramatically. For a 4096x4096 matrix, full fine-tuning has about 16.8 million trainable parameters; LoRA with r=8 has only about 65,000 — roughly a 250x reduction, for that matrix alone. Across a whole model, LoRA commonly reduces trainable parameters by 99% or more while recovering most or all of the task performance of full fine-tuning.

## Why this works: low intrinsic rank

The claim isn't that any random small matrix works — it's that the *specific* update a pretrained model needs for a downstream task doesn't require full-rank freedom. Pretrained weights already encode broad, general knowledge; adapting them to a narrower task is more like a small nudge in a low-dimensional subspace than a wholesale rewrite. Empirically, this holds up well across a wide range of tasks, which is why LoRA has become the default approach for efficient fine-tuning rather than a niche trick.

A practical bonus: at inference time you can merge `A @ B` back into `W` (`W_new = W + A @ B`) and get a model with the exact same architecture and speed as the original — no added latency from the adaptation.

## What this looks like with Hugging Face `peft`

The `peft` library makes LoRA fine-tuning a matter of wrapping a base model rather than restructuring it:

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import LoraConfig, get_peft_model, TaskType

base_model = AutoModelForCausalLM.from_pretrained("some-base-model")
tokenizer = AutoTokenizer.from_pretrained("some-base-model")

lora_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=8,                       # rank of the adapter matrices
    lora_alpha=16,             # scaling factor applied to the LoRA update
    lora_dropout=0.05,
    target_modules=["q_proj", "v_proj"],  # which layers get adapters
)

model = get_peft_model(base_model, lora_config)
model.print_trainable_parameters()
# trainable params: ~0.1-1% of total params -- everything else is frozen

# From here, train `model` with a normal Trainer/training loop.
# Only the injected A/B matrices receive gradient updates.
```

`target_modules` tells `peft` which existing layers (commonly the query/value projections inside attention) to attach LoRA adapters to. Everything else in the base model stays frozen and untouched.

## Where LoRA fits in the bigger picture

LoRA is one of several **parameter-efficient fine-tuning (PEFT)** methods, alongside approaches like prefix-tuning and adapters, but it's become the most widely adopted because it adds no inference latency once merged and needs no architectural changes to the base model. It's also complementary to other efficiency techniques covered in this series: you can LoRA-fine-tune a [Mixture-of-Experts](/DeepLearning/mixture-of-experts/) model's routed experts, or a [Vision Transformer](/DeepLearning/vision-transformers/) for a new visual task, without touching the vast majority of either model's weights.

## Key takeaways

- Full fine-tuning updates every weight and requires storing gradients/optimizer state for the entire model — expensive in memory and storage.
- LoRA freezes the pretrained weights and learns a low-rank decomposition (`A x B`) that approximates the needed update.
- A small rank `r` (e.g., 8) can capture most of the useful adaptation, cutting trainable parameters by 99%+ in many cases.
- Libraries like Hugging Face `peft` make attaching LoRA adapters to an existing model a few lines of code.

# Made with ❤️ by Vaibhav Hariramani
