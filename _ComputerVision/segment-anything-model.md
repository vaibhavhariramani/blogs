---
title: "Segment Anything Model (SAM): Segmentation Without Fixed Classes"
excerpt: "How SAM lets you segment any object in an image using a point, a box, or a prompt — no retraining, no predefined class list."
header:
  image: /assets/images/unsplash-gallery-image-3.jpg
  teaser: /assets/images/unsplash-gallery-image-3-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Every classical detection or segmentation model you've seen so far on this blog has one thing baked in from the start: a fixed list of classes. [Haar cascades](/ComputerVision/face-detection-using-haar-cascade-theory/) only know "face." [YOLO](/ComputerVision/yolo-object-detection/) only knows the ~80 categories it was trained on (person, car, dog, ...). If you want to detect something outside that list, you need new labeled data and retraining.

Meta AI's **Segment Anything Model (SAM)**, released in 2023, breaks that assumption. SAM doesn't try to know what a "car" or "dog" is. Instead, it answers a much simpler, more general question: **"given this hint about where to look, what is the full outline of the object there?"** That hint is called a **prompt**, and it's the core idea behind **promptable segmentation**.

## Object detection vs. segmentation, quickly

Before SAM, it's worth being precise about what "segmentation" even means, since it's a step beyond detection:

- **Detection** (what YOLO does) draws a rectangular bounding box around an object: "there's a dog roughly in this box."
- **Segmentation** goes further and labels every individual pixel that belongs to the object, producing a precise mask that follows the object's actual silhouette, not just a box around it.

Segmentation is harder and more expensive to get labeled training data for — someone has to trace pixel-accurate outlines, not just draw boxes. That's exactly the bottleneck SAM was built to sidestep.

## What "promptable" segmentation means

Instead of training a separate segmentation model per class, SAM is trained to take an image plus a lightweight **prompt** and return a mask matching that prompt. Prompts can be:

- **A point** — click once on a pixel that's part of the object you want ("here, this pixel is part of the object I care about").
- **A box** — draw a rough bounding box, and SAM will find the precise mask that fits inside it.
- **A set of points** — including negative points ("not this region") to refine an ambiguous mask.
- **Text** (in extensions built on top of SAM, combined with a vision-language model like CLIP) — describe the object in words, and a grounding step converts that to points/boxes SAM can consume.

Given any of these, SAM outputs one or more candidate masks with confidence scores — because a single point can be ambiguous (does clicking on a shirt mean "segment the shirt" or "segment the whole person"?), SAM often returns multiple valid interpretations and lets the confidence score or the user pick.

## Why SAM generalizes to unseen objects

The reason SAM works on objects it has never explicitly been taught the name of comes down to how it was trained:

1. **Massive, diverse training data.** SAM was trained on the SA-1B dataset — over 1 billion masks across 11 million images, covering an enormous range of object types, textures, and scenes, far beyond what any fixed-class dataset like COCO (80 classes) provides.
2. **Class-agnostic objective.** SAM was never taught to output a class label at all — just "the boundary of the thing near this prompt." Because it never learned "this shape = dog," it also never learned to *fail* on shapes it hasn't seen labeled as a class before. It generalizes to shapes and boundaries in general, not to a memorized list of categories.
3. **A model architecture built for prompts.** SAM's image encoder (a Vision Transformer) processes the image once into a rich embedding. A lightweight, fast prompt encoder + mask decoder then combines that embedding with your point/box prompt to produce a mask in a fraction of a second — fast enough to click around an image interactively and get near-instant mask previews.

## Practical use cases

- **Interactive photo/video editing** — click an object once to cut it out, blur the background, or swap it, without manually tracing an outline.
- **Data annotation acceleration** — instead of a human tracing pixel-perfect masks by hand for a new dataset, they click once per object and let SAM produce the mask, cutting annotation time dramatically.
- **Medical and scientific imaging** — segmenting structures in scans where a fixed "class list" doesn't exist or varies by study, using a point or box prompt from a domain expert.
- **Robotics and AR** — letting a robot or AR headset segment "whatever is closest to where the user is pointing," useful when the set of possible objects can't be predicted in advance.
- **Combining with detectors** — running [YOLO](/ComputerVision/yolo-object-detection/) first to get boxes around known classes, then feeding those boxes into SAM as prompts to get pixel-accurate masks instead of just boxes.

## A minimal working example

```bash
pip install segment-anything torch torchvision opencv-python
```

```python
import cv2
from segment_anything import sam_model_registry, SamPredictor

# Load a pretrained SAM checkpoint (download from Meta's release)
sam = sam_model_registry["vit_b"](checkpoint="sam_vit_b_01ec64.pth")
predictor = SamPredictor(sam)

image = cv2.imread("street_scene.jpg")
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
predictor.set_image(image_rgb)

# Prompt: a single point roughly on the object we want segmented (x, y)
input_point = [[450, 300]]
input_label = [1]  # 1 = foreground point

masks, scores, _ = predictor.predict(
    point_coords=input_point,
    point_labels=input_label,
    multimask_output=True,
)

# masks[i] is a boolean pixel mask the same size as the image;
# scores[i] is SAM's confidence for that mask interpretation
best_mask = masks[scores.argmax()]
```

No class list, no retraining, no dataset of "things that look like this" — just one point, and a pixel-accurate mask comes back.

## The trade-off

SAM is powerful precisely because it's general, but that generality means it doesn't know or care *what* it segmented — it will happily hand you a perfect mask of an object it has no name for. If you need both "where exactly are the pixels" and "what is this called," you typically pair SAM with a classifier, a detector like [YOLO](/ComputerVision/yolo-object-detection/), or a [vision-language model](/ComputerVision/vision-language-models/) that can label the region SAM outlined.

# Made with ❤️ by Vaibhav Hariramani
