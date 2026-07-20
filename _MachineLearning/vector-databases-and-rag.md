---
title: "Vector Databases & RAG: Giving LLMs a Memory They Can Trust"
excerpt: "How embeddings, vector search, and Retrieval-Augmented Generation combine to ground LLM answers in real data and cut down hallucination."
header:
  image: /assets/images/machine_learning/machine-learning.jpg
  teaser: /assets/images/machine_learning/t2.jpg
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
---

Large Language Models are excellent at fluent text generation but they only "know" what was baked into their training data, and they will confidently generate plausible-sounding but false statements — a problem known as **hallucination**. Retrieval-Augmented Generation (RAG), built on top of **vector databases**, is the most widely used technique to fix this: instead of relying purely on the model's memorized knowledge, you retrieve relevant facts from your own data at query time and hand them to the LLM as context.

## Embeddings: Turning Meaning Into Numbers

An **embedding** is a numerical vector (typically a few hundred to a few thousand floating-point numbers) that represents the *meaning* of a piece of text, image, or other data. Embedding models are trained so that semantically similar inputs end up close together in this vector space, and dissimilar inputs end up far apart.

For example, the sentences "How do I reset my password?" and "I forgot my login credentials" would produce embedding vectors that are close together, even though they don't share many words — because an embedding model captures meaning, not just keywords.

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-MiniLM-L6-v2")
vec1 = model.encode("How do I reset my password?")
vec2 = model.encode("I forgot my login credentials")
# cosine similarity between vec1 and vec2 will be high
```

## Vector Search: Finding "Nearby" Meaning

Once text is converted to vectors, finding relevant information becomes a geometry problem: given a query vector, find the stored vectors closest to it, usually measured with **cosine similarity** or **Euclidean distance**. This is conceptually related to the clustering ideas from our [Essentials of Machine Learning](/MachineLearning/Essentials-of-the-Machine-Learning/) post — both rely on distance in a feature space — except here the goal is nearest-neighbor retrieval rather than grouping.

Doing this search naively (comparing a query against every stored vector) doesn't scale past a few thousand items, so **vector databases** use approximate nearest neighbor (ANN) indexing algorithms — such as HNSW (Hierarchical Navigable Small World graphs) or IVF (Inverted File indexes) — to search millions or billions of vectors in milliseconds, trading a small amount of accuracy for a large amount of speed.

## Popular Vector Databases

| Tool | Notes |
|---|---|
| **Pinecone** | Fully managed, cloud-native vector database; popular for production RAG systems that don't want to run their own infrastructure. |
| **Weaviate** | Open-source, supports hybrid search (combining vector similarity with traditional keyword/BM25 search) and can be self-hosted or used as a managed cloud service. |
| **Chroma** | Lightweight, open-source, developer-friendly — often used for prototyping RAG applications locally before scaling up. |
| **FAISS** (Facebook AI Similarity Search) | A library (not a full database) for efficient similarity search; widely used as the underlying ANN engine inside other tools and for in-memory research/prototyping. |
| **Milvus / Qdrant / pgvector** | Other common choices — Milvus for large-scale open-source deployments, Qdrant for a Rust-based performance focus, and pgvector for teams that want vector search as an extension inside PostgreSQL rather than a separate system. |

## How a RAG Pipeline Works

A typical RAG system has two phases:

**Indexing (done ahead of time):**
1. Split your source documents into chunks (e.g., 300–500 word passages).
2. Convert each chunk into an embedding vector.
3. Store the vector, plus the original text and metadata, in a vector database.

**Querying (done at request time):**
1. Convert the user's question into an embedding using the same embedding model.
2. Search the vector database for the top-k most similar chunks.
3. Insert those retrieved chunks into the LLM's prompt as context.
4. Ask the LLM to answer the question *using only the provided context*.

```python
# Simplified RAG query using Chroma + an embedding model + an LLM call
query = "What is our refund policy for annual subscriptions?"
query_vector = embed_model.encode(query)

results = collection.query(
    query_embeddings=[query_vector],
    n_results=3
)
context = "\n\n".join(results["documents"][0])

prompt = f"""Answer the question using only the context below.
If the answer isn't in the context, say you don't know.

Context:
{context}

Question: {query}
"""
answer = llm.generate(prompt)
```

## Why This Reduces Hallucination

The LLM is no longer answering purely from its training-time memory — it is answering from text you handed it moments ago, retrieved specifically because it's relevant to the question. This has two big benefits:

1. **Grounding**: the answer can cite or closely track real source material, rather than being invented.
2. **Freshness**: you can update the vector database with new documents at any time without retraining the LLM itself — the model's "knowledge" of your data is only as stale as your last indexing run.

RAG doesn't eliminate hallucination entirely (the LLM can still misread or ignore the retrieved context), but it dramatically narrows the gap between "the model's best guess" and "what our own documents actually say."

## Where This Fits in a Real ML Workflow

RAG sits downstream of the same data understanding work described in our [Exploratory Data Analysis](/MachineLearning/Exploratory-Data-Analysis/) post — you still need to know your data, clean it, and decide how to chunk it sensibly. The pipeline typically looks like:

1. Collect and clean source documents (support docs, wikis, transcripts, PDFs).
2. Chunk and embed them into a vector database.
3. Build a retrieval step that turns a user query into relevant chunks.
4. Feed retrieved chunks + query into an LLM prompt.
5. Evaluate answer quality — precision of retrieval (did we fetch the right chunks?) and faithfulness of generation (did the LLM stick to the provided context?).
6. Re-index continuously as source documents change.

RAG is now the default architecture for building chatbots, internal knowledge assistants, and customer support tools on top of proprietary data — it lets you combine the fluency of a general-purpose LLM with the accuracy of your own, constantly updated knowledge base.

# Made with ❤️ by Vaibhav Hariramani
