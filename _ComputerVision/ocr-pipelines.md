---
title: "Modern OCR Pipelines: From Tesseract to Layout-Aware Document AI"
excerpt: "Why classic OCR struggles with real-world documents, and how deep-learning-based pipelines read text, tables, and forms in context."
header:
  image: /assets/images/unsplash-gallery-image-2.jpg
  teaser: /assets/images/unsplash-gallery-image-2-th.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

**Optical Character Recognition (OCR)** is one of the oldest problems in computer vision — turning pixels of text into actual, usable characters and words. It sounds simple until you try it on a real invoice, a photographed receipt, or a form with checkboxes and tables, and realize that "reading the text" is only half the job. Knowing *where* each piece of text sits relative to everything else is often just as important as reading it correctly.

## Classic OCR: Tesseract-style pipelines

Traditional OCR engines like **Tesseract** follow a fairly mechanical, multi-stage pipeline:

1. **Preprocessing** — binarize the image (convert to black and white), deskew it if the page is tilted, remove noise.
2. **Layout/line segmentation** — find lines of text, then segment each line into individual characters or connected components, largely using pixel projections and geometric heuristics.
3. **Character recognition** — classify each isolated character glyph against a trained model of what letters/digits look like.
4. **Post-processing** — use a dictionary or language model to fix obviously wrong character sequences (e.g., "rnodel" → "model").

This works reasonably well on clean, high-contrast, single-column scanned text — think a well-scanned book page in a standard font. It falls apart quickly on:

- Skewed or low-quality photos (phone pictures of receipts, warped pages).
- Unusual fonts, handwriting, or low contrast.
- Anything with **structure** — tables, multi-column layouts, forms with labels and values — because character-by-character recognition has no concept of "this text is a table cell" or "this label belongs to that value."

## Modern deep-learning-based OCR

Modern OCR systems replace the hand-engineered segmentation-then-classification pipeline with end-to-end trained neural networks, typically split into two learned stages:

1. **Text detection.** A neural network (often similar in spirit to object detectors like [YOLO](/ComputerVision/yolo-object-detection/)) scans the image and predicts bounding boxes or polygons around **regions containing text**, regardless of orientation, font, or curvature. This is a detection problem, not a character-recognition problem.
2. **Text recognition.** For each detected text region, a second network — typically a CNN feature extractor feeding into a sequence model (an RNN, transformer, or CTC-based decoder) — reads the sequence of characters in that crop directly, without needing to segment individual letters first. This end-to-end sequence approach is far more robust to touching characters, unusual fonts, and varied spacing than classic per-character segmentation.

Because both stages are learned from large, diverse datasets of real-world text images (rather than hand-coded heuristics), modern OCR handles curved text, low-quality photos, and mixed fonts dramatically better than classic pipelines like Tesseract's older engine (Tesseract 4+ actually added an LSTM-based recognition mode itself, blurring the line, but the detection step is still typically the weaker link compared to purpose-built deep detectors).

## Layout-aware extraction: reading structure, not just characters

Recognizing the raw text is often not the actual goal — the goal is *structured* data: "what's the invoice total," "what's in row 3, column 2 of this table," "what value is filled into the 'Date of Birth' field." This is where **document understanding models** go a step beyond plain OCR:

- **Table extraction** — models detect table boundaries, then rows and columns, then map each recognized text span into a specific (row, column) cell, reconstructing the table as structured data (e.g., a CSV or JSON grid) instead of a flat wall of text.
- **Form/key-value extraction** — models like **LayoutLM** and its successors combine the recognized text with its **2D position on the page** and even visual features (fonts, boxes, lines) so the model can learn associations like "the text immediately to the right of the label 'Total:' is the value we want," rather than just reading top-to-bottom.
- **Document VLMs** — increasingly, general [vision-language models](/ComputerVision/vision-language-models/) are used directly on document images, asked plain-English questions like "what is the invoice total?" and answering by jointly reasoning over the text and its layout, skipping a separate structured-extraction step entirely for many use cases.

The common thread: layout-aware systems don't throw away spatial position once text is read — they treat "where is it on the page" as a first-class signal, because meaning in real documents is often carried by position (label-left, value-right; header row, data rows) as much as by the words themselves.

## A minimal working example

Classic OCR with Tesseract, for comparison:

```bash
pip install pytesseract pillow
```

```python
import pytesseract
from PIL import Image

image = Image.open("receipt.jpg")
text = pytesseract.image_to_string(image)
print(text)
```

This gives you a flat string of recognized text — no idea which line was the total, which was the date, or how they relate.

A layout-aware approach using EasyOCR (a deep-learning-based OCR engine) that also returns bounding boxes for each text region, preserving position:

```bash
pip install easyocr
```

```python
import easyocr

reader = easyocr.Reader(["en"])
results = reader.readtext("receipt.jpg")

for (box, text, confidence) in results:
    print(f"'{text}' (confidence {confidence:.2f}) at {box}")
```

Because each result includes the text's bounding box, you can now reconstruct structure yourself — group text by vertical position into rows, by horizontal position into columns — or feed the boxes plus text into a layout-aware model like LayoutLM for proper table/form extraction.

## Choosing an approach

- **Clean, single-column scanned text, tight resource constraints:** classic Tesseract-style OCR is still fast and good enough.
- **Real-world photos, receipts, curved or rotated text, mixed fonts:** deep-learning detection + recognition pipelines (EasyOCR, PaddleOCR, cloud document AI APIs) are far more robust.
- **Tables, forms, invoices where structure matters:** layout-aware models (LayoutLM-style, or a document-focused [vision-language model](/ComputerVision/vision-language-models/)) that reason over text position, not just text content.

OCR is a good reminder that "reading" an image, like detecting or segmenting one, has moved from hand-engineered, single-purpose pipelines toward general, learned systems — the same broader shift covered across [YOLO](/ComputerVision/yolo-object-detection/), [SAM](/ComputerVision/segment-anything-model/), and [vision-language models](/ComputerVision/vision-language-models/) on this blog.

# Made with ❤️ by Vaibhav Hariramani
