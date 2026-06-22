# Project 06 - Vector Database (ChromaDB) + Website RAG

## Project Objective

The goal of this project is to replace the in-memory Python list used in Website RAG with a real Vector Database (ChromaDB) while keeping the retrieval and generation workflow unchanged.

### Previous Architecture

```text
Website
↓
Sections
↓
Embeddings
↓
Python List
↓
Retrieval
↓
LLM
```

### New Architecture

```text
Website
↓
Sections
↓
Embeddings
↓
ChromaDB
↓
Retrieval
↓
LLM
```

---

# What is a Vector Database?

A Vector Database stores:

* Embeddings
* Documents
* Metadata

and provides:

* Similarity Search
* Semantic Retrieval

Example:

```python
{
    "id": "projects",

    "document":
    "Payment Validation AI Agent",

    "embedding":
    [0.81, 0.22, 0.57, ...],

    "metadata":
    {
        "section": "Projects",
        "url": "portfolio_website"
    }
}
```

---

# Why Use a Vector Database?

## Python List Approach

```python
documents = []
```

Problems:

* Data lost after restart
* Not scalable
* Manual similarity search
* Difficult to maintain

---

## ChromaDB Approach

Benefits:

* Persistent Storage
* Fast Similarity Search
* Scalable Architecture
* Production Ready

---

# ChromaDB Concepts

## 1. Client

Connection to ChromaDB.

```python
client = chromadb.PersistentClient(
    path="./chroma_db"
)
```

Purpose:

* Connect to database
* Store data on disk
* Persist after restart

---

## 2. Collection

Equivalent of a SQL Table.

```python
collection = client.get_or_create_collection(
    name="portfolio_website"
)
```

Stores:

* Documents
* Embeddings
* Metadata
* IDs

---

## 3. Documents

Actual text content.

Example:

```text
Payment Validation AI Agent
```

---

## 4. Metadata

Additional information attached to chunks.

Example:

```python
{
    "section": "Projects",
    "page": "Home",
    "url": "portfolio_url"
}
```

Purpose:

* Source tracking
* Filtering
* Citations
* Context extension

---

## 5. IDs

Unique record identifiers.

Bad:

```python
"1"
"2"
"3"
```

Good:

```python
"projects"
"skills"
"contact"
```

Benefits:

* Readable
* Maintainable
* Easy debugging

---

# Collection Operations

## Create Collection

```python
collection = client.get_or_create_collection(
    name="portfolio_website"
)
```

---

## Insert Data

```python
collection.upsert(
    ids=[section_id],
    documents=[text],
    embeddings=[embedding],
    metadatas=[metadata]
)
```

---

## Query Data

```python
collection.query(
    query_embeddings=[query_embedding],
    n_results=3
)
```

---

# Upsert vs Add

## Add

```python
collection.add(...)
```

Problem:

```text
Fails if record already exists
```

---

## Upsert

```python
collection.upsert(...)
```

Behavior:

```text
Exists?
→ Update

Doesn't Exist?
→ Insert
```

Preferred during development.

---

# Manual Embeddings vs Automatic Embeddings

## Automatic Embeddings

```python
collection.upsert(
    documents=[text]
)
```

ChromaDB generates embeddings automatically.

Advantages:

* Less code
* Quick setup

Disadvantages:

* Less control
* Unknown embedding model
* Retrieval quality may vary

---

## Manual Embeddings

```python
embedding = get_embedding(text)

collection.upsert(
    embeddings=[embedding]
)
```

Advantages:

* Full control
* Consistent retrieval
* Easy model switching

Used in this project.

---

# Embedding Model Used

```text
nomic-embed-text
```

Purpose:

```text
Text
↓
Embedding
↓
Vector Search
```

---

# Important Concept

## Embedding Model ≠ Vector Database

Wrong:

```text
ChromaDB = Embedding Model
```

Correct:

```text
Embedding Model
↓
Creates Vectors

ChromaDB
↓
Stores Vectors
```

Examples:

```text
OpenAI Embeddings + Pinecone

BGE + Qdrant

Nomic + ChromaDB
```

---

# Embedding Dimension Issue

Error Encountered:

```text
Collection expecting embedding dimension 768
got 384
```

Reason:

```text
Stored vectors = 768

Query vectors = 384
```

Cause:

Different embedding dimensions inside the same collection.

---

## Solution

Delete:

```text
chroma_db/
```

Recreate collection and store embeddings again using the same embedding model.

---

# Important Rule

Inside one collection:

```text
All embeddings must have same dimension
```

Valid:

```text
384
384
384
384
```

Invalid:

```text
384
768
384
768
```

---

# Website Chunking Strategy

Sections extracted:

* About
* Experience
* Business Impact
* AI Architecture
* Projects
* Skills
* Certifications
* Contact

Each section stored as:

```text
1 Section = 1 Chunk = 1 Embedding
```

Benefits:

* Better retrieval
* Focused context
* Smaller chunks

---

# Retrieval Workflow

Question:

```text
What AI projects has Rajat built?
```

Process:

```text
Question
↓
Embedding
↓
ChromaDB Query
↓
Top-K Results
↓
Context Assembly
↓
LLM
```

---

# Top-K Retrieval

```python
n_results = 3
```

Example:

```text
Projects
AI Architecture
Business Impact
```

Purpose:

* More context
* Better answer quality

---

# Distance Score

ChromaDB returns:

```python
distances
```

---

## Similarity Score

Higher is better.

```text
350
300
250
```

---

## Distance Score

Lower is better.

```text
0.10
0.20
0.50
```

---

# Context Assembly

Retrieved chunks:

```text
Projects
Skills
Experience
```

Combined into:

```python
context += chunk_text
```

Purpose:

```text
Create a single context block for the LLM
```

---

# Major Bug Discovered

Wrong:

```python
for doc in retrieved_docs:

    context += ...

    response = ollama.chat(...)

    return answer
```

Problem:

```text
Only first chunk reaches LLM
```

---

Correct:

```python
for doc in retrieved_docs:

    context += ...

response = ollama.chat(...)
```

Result:

```text
All chunks reach LLM
```

---

# Generation Layer

Input:

```text
Question
+
Retrieved Context
```

↓

```text
LLM
```

↓

```text
Answer
```

Example:

Question:

```text
What AI projects has Rajat built?
```

Retrieved Context:

```text
Payment Validation AI Agent

RAG Compliance Assistant

Smart Parking Predictor
```

Generated Answer:

```text
Rajat has worked on:

1. Payment Validation AI Agent
2. RAG Compliance Assistant
3. Smart Parking Predictor
```

---

# Citation

Metadata used for:

```text
Source Tracking
```

Example:

```text
Section: Projects
URL: Portfolio Website
```

Benefits:

* Transparency
* Trust
* Verification

---

# Final Architecture

```text
Website
↓
Playwright
↓
Section Chunking
↓
Metadata
↓
Embeddings
↓
ChromaDB
↓
Top-K Retrieval
↓
Context Assembly
↓
LLM
↓
Answer
↓
Citation
```

---

# Interview Questions

## What is a Vector Database?

A Vector Database stores embeddings, documents and metadata and performs semantic similarity search.

---

## What is a Collection in ChromaDB?

A Collection is similar to a SQL table that stores documents, embeddings, metadata and IDs.

---

## Difference Between Similarity and Distance?

Similarity:

```text
Higher = Better
```

Distance:

```text
Lower = Better
```

---

## Why Use Metadata?

Metadata improves:

* Source tracking
* Filtering
* Citations
* Context extension

---

## Why Use Semantic IDs?

Examples:

```text
projects
skills
contact
```

Benefits:

* Easy debugging
* Better maintainability
* Self-documenting records

---

## Why Did 768 vs 384 Error Occur?

Different embedding dimensions were used between stored vectors and query vectors.

---

## Why Is Chunking Still Important With ChromaDB?

A vector database improves storage and retrieval, but cannot fix poor chunk quality.

Good chunks → Better embeddings → Better retrieval.

---

## What Is Context Assembly?

Combining retrieved chunks into a single context before sending them to the LLM.

---

# Key Learnings

* ChromaDB replaces Python list storage.
* Embedding model and vector database are separate layers.
* Metadata improves retrieval quality and traceability.
* Retrieval quality depends on chunk quality and embedding quality.
* Vector databases do not eliminate the need for chunking.
* Context assembly is critical for answer generation.
* Persistence allows data to survive application restarts.

---

# Project Outcome

```text
Website Extraction           ✅
Section Chunking             ✅
Metadata                     ✅
Embeddings                   ✅
ChromaDB                     ✅
Persistent Storage           ✅
Semantic Retrieval           ✅
Context Assembly             ✅
LLM Generation               ✅
Source Citation              ✅
Production-style Website RAG ✅
```

🏆 Successfully built an end-to-end Website RAG system using ChromaDB, manual embeddings, semantic retrieval, context assembly, and LLM-based answer generation.
