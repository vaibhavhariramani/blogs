---
title: "Vision Transformers (ViT): Attention Instead of Convolutions"
excerpt: "How the transformer architecture that took over NLP learned to see images, one patch at a time."
header:
  image: images/deep_learning/computer-vision-abstract-1.png
  teaser: images/deep_learning/computer-vision-abstract-1.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

## Why bother replacing convolutions?

For almost a decade, Convolutional Neural Networks (CNNs) were the default answer to "how do I get a computer to understand images?" They work by sliding small filters over an image, building up from edges and textures in early layers to whole objects in deeper layers. That built-in assumption — that nearby pixels matter more than far-away ones — is called an **inductive bias**, and it's the reason CNNs learn efficiently from relatively modest amounts of image data.

Meanwhile, in NLP, the [Transformer](https://arxiv.org/abs/1706.03762) architecture had already proven that **self-attention** — letting every token look at every other token — could outperform architectures with hand-designed structure, as long as there was enough data and compute. The natural question was: if we strip away the convolutional assumptions and just feed an image into a plain transformer, does it still work?

The **Vision Transformer (ViT)**, introduced by Google Research in the paper ["An Image is Worth 16x16 Words"](https://arxiv.org/abs/2010.11929), showed the answer is yes — and at scale, ViT matches or beats CNNs like ResNet, with a simpler architecture.

## The core trick: turn an image into a sentence

Transformers were built for sequences of tokens (words). Images are 2D grids of pixels, not sequences. ViT's central idea is to make an image *look* like a sequence:

1. **Split the image into patches.** A 224x224 image might be cut into a grid of 16x16-pixel patches, giving 14x14 = 196 patches.
2. **Flatten and embed each patch.** Each patch (16x16x3 numbers, for RGB) is flattened into a vector and passed through a learned linear projection — the **patch embedding** — to produce a fixed-size vector, exactly like a word embedding.
3. **Add position embeddings.** Since attention has no inherent sense of order or spatial layout, a learned position embedding is added to each patch embedding so the model knows *where* each patch came from.
4. **Prepend a classification token.** A special learnable `[CLS]` token is added to the sequence, borrowed straight from BERT. After passing through the transformer, this token's final representation is used for classification.
5. **Feed the sequence into a standard transformer encoder.** From here on, it's exactly the same self-attention + feed-forward stack used in NLP transformers.

```python
# Conceptual shape walkthrough for a 224x224x3 image, 16x16 patches
image.shape            # (224, 224, 3)
patches.shape          # (196, 16*16*3) = (196, 768)
patch_embeddings.shape # (196, D)   -- D = model hidden size, e.g. 768
tokens.shape           # (197, D)   -- CLS token prepended
# tokens + position_embeddings -> transformer encoder -> classification head
```

## Self-attention over patches, in plain terms

Once the image is a sequence of patch tokens, self-attention works the same way it does for words. For every patch, the model computes three vectors: a **query**, a **key**, and a **value**. The attention score between patch A and patch B is roughly "how relevant is B's content to what A is looking for," computed as a dot product between A's query and B's key. Those scores are turned into weights (via softmax), and each patch's output is a weighted sum of all patches' value vectors.

The practical effect: a patch showing part of a dog's ear can directly attend to a patch showing part of the dog's tail on the other side of the image, in a single layer — no need to stack many convolutional layers to grow the receptive field. This **global receptive field from layer one** is the biggest structural difference from CNNs, where information only spreads to distant pixels gradually, layer by layer.

## Why ViT can compete with CNNs (with a catch)

Because ViT drops the convolutional inductive bias, it doesn't get built-in help with locality and translation invariance — it has to learn useful visual structure entirely from data. That means:

- **With small datasets, ViT tends to underperform CNNs.** There's nothing nudging it toward "nearby pixels are related," so it needs enough examples to discover that pattern itself.
- **With large-scale pretraining** (the original paper used datasets with hundreds of millions of images), ViT matches or exceeds CNN accuracy while being architecturally simpler and more compute-efficient to scale.
- **Transfer learning works well.** Once pretrained, ViT fine-tunes efficiently on smaller downstream datasets — similar to how BERT is pretrained once and fine-tuned many times, a topic covered in this blog's [LoRA fine-tuning post](/DeepLearning/lora-fine-tuning/).

Later variants (DeiT, Swin Transformer, hybrid CNN-ViT models) reintroduce some structure — hierarchical patches, distillation, local attention windows — to make ViTs competitive even without massive pretraining datasets.

## Where ViT fits in the landscape

ViT is part of a larger trend of transformers absorbing tasks that used to need specialized architectures — first language, then vision, and now increasingly audio, video, and multimodal models (CLIP, GPT-4V-style systems) that mix patch tokens with word tokens in the same attention mechanism. If you're new to the transformer's core mechanism, it's worth reading up on self-attention in the NLP context first — the mechanics are identical, only the tokens change from words to patches.

For a broader grounding in what deep learning covers before diving into transformers specifically, see this blog's [Introduction to Deep Learning](/DeepLearning/introduction-deep-learning/) post and the [Deep Learning series overview](/DeepLearning/dl-series/).

## Key takeaways

- ViT treats an image as a sequence of flattened patches, embedded and positioned just like word tokens.
- Self-attention gives every patch a global view of every other patch from the very first layer.
- ViT needs large-scale pretraining data to overcome the lack of built-in locality bias that CNNs get for free.
- It's now a foundational building block for modern multimodal and vision-language models.

# Made with ❤️ by Vaibhav Hariramani
