---
title: "Neural Networks: How They Actually Work"
excerpt: "Before transformers, MoE, or Mamba make sense, you need the fundamentals: what a neuron computes, how a network learns, and why 'deep' just means 'more layers.'"
header:
  image: /images/deep_learning/dp3.jpg
  teaser: /images/deep_learning/Deep-Learning.jpg
sidebar:
  - title: "Role"
    text: "MlOps Engineer,Open Source Contributor"
  - title: "Responsibilities"
    text: "Building scalable and reliable infrastructure for ML model training and deployment."
---

Our [Introduction to Deep Learning](/DeepLearning/introduction-deep-learning/) post covered the big picture — neurons, layers, and why more layers means "deep." This post fills in the part that post skipped: what a neural network is actually *computing*, step by step, and how it learns from data.

## The Building Block: A Single Neuron

A single artificial neuron does something almost embarrassingly simple. It takes a set of inputs, multiplies each by a weight, adds a bias, and passes the result through an activation function:

```
z = (w1*x1 + w2*x2 + ... + wn*xn) + b
output = activation(z)
```

That's it. The "learning" in machine learning is entirely about finding good values for those weights (`w`) and the bias (`b`) — nothing else in the neuron is adjustable.

## Why the Activation Function Matters

If you stack layers of neurons but skip the activation function, something disappointing happens: no matter how many layers you add, the whole network collapses mathematically into a single linear function. Stacking linear operations just gives you another linear operation.

The activation function is what breaks that pattern and lets a network approximate genuinely non-linear relationships in data. Common choices:

* **ReLU** (`max(0, z)`) — simple, fast, and the default choice for most hidden layers today.
* **Sigmoid** (`1 / (1 + e^-z)`) — squashes output to (0, 1), historically common but prone to a problem called vanishing gradients in deep networks.
* **Softmax** — turns a vector of numbers into a probability distribution, used almost universally on the final layer of a classifier.

## Layers: Input, Hidden, Output

A network is just neurons arranged in layers:

* **Input layer** — one value per feature in your data (e.g., one per pixel, one per word embedding dimension).
* **Hidden layers** — where the actual pattern-learning happens. "Deep" simply means there's more than one of these.
* **Output layer** — shaped to match the task: one neuron for a yes/no prediction, one per class for classification, thousands for next-token prediction in a language model.

## How a Network Learns: Forward Pass, Loss, Backpropagation

Training a network is a loop with three repeating steps:

1. **Forward pass** — feed input data through the network to produce a prediction.
2. **Loss calculation** — compare the prediction to the actual answer using a loss function (e.g., mean squared error for regression, cross-entropy for classification). This gives a single number: how wrong the network was.
3. **Backpropagation** — work backward through the network computing how much each individual weight contributed to that error, using the chain rule from calculus.

Once you know each weight's contribution to the error, **gradient descent** nudges every weight slightly in the direction that reduces the loss. Repeat this loop over your dataset thousands of times, and the weights gradually converge on values that make good predictions.

## A Minimal Example

```python
import numpy as np

def relu(z):
    return np.maximum(0, z)

# One hidden layer, 3 inputs -> 4 hidden units -> 1 output
W1 = np.random.randn(3, 4) * 0.1
b1 = np.zeros(4)
W2 = np.random.randn(4, 1) * 0.1
b2 = np.zeros(1)

def forward(x):
    hidden = relu(x @ W1 + b1)
    output = hidden @ W2 + b2
    return output

x = np.array([0.5, -1.2, 0.3])
print(forward(x))
```

This is the exact same computation a framework like PyTorch or TensorFlow runs — they just handle the backpropagation math and hardware acceleration for you automatically.

## Where This Goes Next

Everything more advanced on this blog builds on this loop. [Vision Transformers](/DeepLearning/vision-transformers/) replace convolution with attention, but they're still trained with forward passes and backpropagation. [Mixture-of-Experts](/DeepLearning/mixture-of-experts/) changes which neurons activate per input, not the underlying math. And [Large Language Models](/DeepLearning/large-language-models/) are, at their core, an enormous stack of these same layers, trained on text instead of images or tables.

# Made with ❤️ by Vaibhav Hariramani
