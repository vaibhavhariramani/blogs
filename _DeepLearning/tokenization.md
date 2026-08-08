---
title: "Tokenization: How Neural Networks Read Text"
excerpt: "Neural networks only understand numbers. Tokenization is the step that turns 'Hello world' into something a model can actually compute on — and the choices made here shape everything downstream."
header:
  image: /images/deep_learning/5.jpg
  teaser: /images/deep_learning/bot4.png
sidebar:
  - title: "Role"
    text: "MlOps Engineer,Open Source Contributor"
  - title: "Responsibilities"
    text: "Building scalable and reliable infrastructure for ML model training and deployment."
---

Every [neural network](/DeepLearning/neural-networks/) computes on numbers — vectors, matrices, dot products. But language models are fed sentences. Tokenization is the translation layer between the two: it converts raw text into a sequence of integers a model can actually process.

## Why Not Just Use Words?

The obvious first idea — one token per word — runs into real problems fast:

* **Vocabulary explosion.** English alone has hundreds of thousands of word forms once you count plurals, tenses, and typos. Every language you add multiplies that.
* **Out-of-vocabulary words.** A word-level tokenizer has no way to represent a word it's never seen — a made-up brand name, a typo, a rare technical term — other than an `<UNK>` placeholder that throws away information.
* **No shared structure.** "run," "running," and "runner" would be three completely unrelated tokens, even though they obviously share meaning.

Character-level tokenization solves the vocabulary problem (there are only ~100 characters to worry about) but creates a new one: sequences become extremely long, and the model has to work much harder to learn that "c-a-t" means something as a unit.

## The Middle Ground: Subword Tokenization

Modern models split the difference with **subword tokenization** — common words stay as a single token, while rare or complex words get broken into meaningful pieces:

```
"tokenization" → ["token", "ization"]
"unhappiness"  → ["un", "happi", "ness"]
```

This keeps the vocabulary manageable (typically 30,000–100,000+ tokens) while still being able to represent *any* string, since in the worst case a word can always fall back to individual characters or bytes.

### Byte-Pair Encoding (BPE)

BPE, used by GPT-family models, builds its vocabulary bottom-up: start with individual characters, then repeatedly merge the most frequently co-occurring pair into a new token, until you hit your target vocabulary size.

### WordPiece

Used by BERT, WordPiece is conceptually similar to BPE but chooses merges based on what maximizes the likelihood of the training data, rather than raw frequency.

### SentencePiece

Both BPE and WordPiece traditionally assume text is already split on whitespace — which breaks down for languages like Japanese or Chinese that don't use spaces between words. SentencePiece treats the input as a raw stream of characters (including spaces) and learns tokenization directly from that, making it language-agnostic. It's the tokenizer behind models like Llama and T5.

## Seeing It in Action

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("gpt2")

text = "Tokenization isn't always intuitive!"
tokens = tokenizer.tokenize(text)
ids = tokenizer.encode(text)

print(tokens)  # ['Token', 'ization', 'Ġisn', "'t", 'Ġalways', 'Ġintuitive', '!']
print(ids)     # [30642, 1634, 2125, 470, 1464, 19259, 0]
```

Notice "Tokenization" splits into two pieces while common short words stay whole — exactly the subword behavior described above. That `Ġ` character marks "this token starts with a space" — a detail BPE tokenizers need since whitespace itself has to be encoded somehow.

## Special Tokens

Every tokenizer also reserves a handful of special tokens beyond regular vocabulary: `[CLS]`/`[SEP]` to mark sequence boundaries in BERT-style models, `<bos>`/`<eos>` for beginning/end of sequence, and `<pad>` to pad shorter sequences up to a fixed batch length. These aren't optional extras — a lot of subtle model bugs trace back to a mismatch between how a model was trained to use these tokens and how it's being prompted at inference time.

## Why This Matters for LLMs

Tokenization directly determines a model's **context window** in practice — "8,000 tokens" isn't 8,000 words, and depending on the tokenizer and language, it might be anywhere from 4,000 to 10,000+ words' worth of text. It also explains some famously weird LLM behavior (like struggling to count letters in a word) — the model never actually sees individual characters, only the token IDs they got grouped into.

Once text is tokenized, it's ready to feed into the architecture that actually does the reasoning — which is exactly where our [Large Language Models](/DeepLearning/large-language-models/) post picks up.

# Made with ❤️ by Vaibhav Hariramani
