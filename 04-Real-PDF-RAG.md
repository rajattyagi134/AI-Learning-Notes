# Project 4 - Real PDF RAG

# Project Goal

Build a Retrieval Augmented Generation (RAG) system capable of answering questions from a real PDF document.

Unlike Document RAG, which used a text file, this project works with an actual PDF and introduces:

* PDF Parsing
* Page Metadata
* Citations
* Source Traceability
* Document-Aware Chunking

---

# What Is PDF RAG?

PDF RAG is a system that:

1. Reads a PDF
2. Extracts text
3. Splits content into chunks
4. Generates embeddings
5. Retrieves relevant chunks
6. Generates answers
7. Provides citations

---

# Architecture

PDF
↓
Page Extraction
↓
Metadata Creation
↓
Chunking
↓
Embeddings
↓
Vector Store
↓
Similarity Search
↓
Top-K Retrieval
↓
Threshold Filtering
↓
Context Assembly
↓
LLM
↓
Answer
↓
Citation

---

# Why Not Use The Entire PDF?

Suppose we have:

500 Page PDF

Creating one embedding for the entire document causes:

* Poor Retrieval Accuracy
* Loss of Semantic Detail
* Context Dilution
* Larger Search Space
* Higher Hallucination Risk

---

# Example

PDF Contains:

* Office Hours
* Vacation Policy
* Maternity Leave
* Remote Work

Question:

How many vacation days do employees get?

If one embedding represents the entire PDF, the retrieval system cannot precisely locate the vacation section.

---

# Solution

Chunk the PDF into smaller meaningful units.

---

# PDF Parsing

## Definition

Extracting text from PDF pages.

Example:

PDF

↓

Page 1 Text

Page 2 Text

Page 3 Text

---

# Why PDF Parsing Is Needed

LLMs cannot directly understand PDF structure.

The PDF must first be converted into text.

---

# Page Extraction

## Definition

Reading PDF content page by page.

Example:

```python
{
    "page": 1,
    "text": "Office Hours..."
}
```

---

# Why Store Pages Separately?

Benefits:

* Better organization
* Source tracking
* Citations
* Explainability

---

# Metadata

## Definition

Additional information stored alongside text.

Example:

```python
{
    "page": 2,
    "chunk_id": 7,
    "text": "...",
    "embedding": [...]
}
```

---

# Metadata vs Content

Content:

```text
Employees receive 25 vacation days annually.
```

Metadata:

```text
Page 2
Chunk 7
Employee Handbook
```

---

# Why Metadata Matters

Metadata answers:

Where did this information come from?

Embeddings answer:

What does this information mean?

---

# Benefits Of Metadata

* Citations
* Debugging
* Filtering
* Auditing
* Compliance
* Explainability

---

# Citations

## Definition

Showing the source of an answer.

Example:

Employees receive 25 vacation days annually.

Source: Page 2

---

# Why Citations Matter

Without Citation:

Employees receive 25 vacation days annually.

User asks:

Where did you find that?

---

With Citation:

Employees receive 25 vacation days annually.

Source: Page 2

Trust increases significantly.

---

# Chunking Strategies

## Strategy 1 - Fixed Character Chunking

Example:

```python
text[0:100]
```

Problem:

Words may be split.

Example:

carried for

ward

Meaning gets damaged.

---

# Strategy 2 - Chunk Overlap

Example:

Chunk 1

0 → 100

Chunk 2

80 → 180

Overlap:

20 characters

---

# Purpose

Preserve context across chunk boundaries.

---

# Chunk Overlap Formula

Step Size:

```python
step = chunk_size - overlap
```

Example:

Chunk Size = 100

Overlap = 20

Step = 80

Chunks:

0 → 100

80 → 180

160 → 260

---

# Strategy 3 - Document-Aware Chunking

## Definition

Using the document's structure instead of arbitrary character limits.

Examples:

* Pages
* Headings
* Sections
* Paragraphs

---

# What We Learned

Our PDF already contained meaningful page boundaries.

Therefore:

1 Page

=

1 Chunk

was the best strategy.

---

# Important Lesson

There is no universal chunking strategy.

Chunking depends on:

* Document Type
* Page Size
* Structure
* Use Case

---

# Chunk ID

## Definition

Unique identifier for each chunk.

Example:

```python
{
    "page": 2,
    "chunk_id": 4
}
```

---

# Why Do We Need Chunk ID?

Multiple chunks may originate from the same page.

Example:

Page 2

↓

Chunk 4

Chunk 5

Chunk 6

Page number alone is not sufficient.

---

# Embeddings

## Definition

Numerical representations of semantic meaning.

Example:

Vacation Policy

↓

[0.12, -0.44, 0.83 ...]

---

# Why Generate Embeddings Per Chunk?

Because each chunk represents a specific topic.

Smaller chunks improve retrieval accuracy.

---

# Why Not Store Only Embeddings?

Retrieval requires embeddings.

Generation requires original text.

Embeddings cannot be sent directly to an LLM.

---

# Why Not Store Only Text?

Text alone cannot support semantic search.

Embeddings are required for similarity matching.

---

# Why Store Both?

```python
{
    "text": "...",
    "embedding": [...]
}
```

Embeddings:

Find information

Text:

Use information

---

# Similarity Search

Question

↓

Question Embedding

↓

Compare Against All Chunk Embeddings

↓

Similarity Score

---

# Example

Question:

How many vacation days do employees get?

Results:

Page 2 → 375

Page 4 → 301

Page 3 → 283

Highest score wins.

---

# Top-K Retrieval

## Definition

Return multiple relevant chunks.

Example:

Top 3 Chunks

Benefits:

* Better context
* More complete answers

---

# Threshold Filtering

## Definition

Remove weak retrieval results.

Example:

Threshold = 300

Scores:

375 ✅

301 ✅

283 ❌

---

# Relative Thresholding

## Definition

Filter relative to best result.

Example:

Top Score = 375

Threshold:

375 × 0.90

=

337.5

Only chunks above 337.5 survive.

---

# Why Relative Thresholding?

More adaptive than fixed thresholds.

---

# Retrieval

## Definition

Finding relevant information.

Responsibilities:

* Generate query embedding
* Compare vectors
* Rank results
* Apply thresholds
* Return chunks

---

# Generation

## Definition

Using retrieved context to create natural language answers.

Responsibilities:

* Read context
* Synthesize information
* Generate response
* Include citations

---

# Retrieval vs Generation

Retrieval

↓

Find Information

Generation

↓

Explain Information

---

# Context Assembly

## Definition

Combining retrieved chunks into a single prompt.

Example:

Page 2:

Vacation Policy...

Page 3:

Maternity Leave...

---

# Why Include Metadata In Context?

Instead of:

Vacation Policy...

Use:

Page 2:

Vacation Policy...

This enables source-aware answers.

---

# Grounding

## Definition

Restricting the LLM to use only retrieved context.

Example Prompt:

Answer ONLY using provided context.

If answer cannot be found:

I don't have enough information to answer that.

---

# Hallucinations

## Definition

Generating information not present in retrieved context.

Example:

Document contains:

Vacation Policy

Model answers:

Medical Insurance

401(k)

Hallucination.

---

# Why Hallucinations Occur

* Weak Retrieval
* Poor Context
* Missing Grounding
* Reasoning Errors

---

# Source Traceability

## Definition

Ability to trace an answer back to its origin.

Example:

Answer:

Employees receive 25 vacation days annually.

Source:

Employee Handbook, Page 2

---

# Enterprise Importance

Source traceability is critical in:

* Banking
* Healthcare
* Legal
* Compliance
* Audit Systems

---

# Final PDF RAG Architecture

PDF
↓
Page Extraction
↓
Metadata
↓
Chunking
↓
Embeddings
↓
Vector Store
↓
Similarity Search
↓
Top-K Retrieval
↓
Threshold Filtering
↓
Context Assembly
↓
LLM
↓
Answer
↓
Citation

---

# Interview Questions

## Q1. What Is PDF RAG?

Answer:

PDF RAG retrieves information from PDF documents and uses an LLM to generate grounded answers based on retrieved content.

---

## Q2. Why Not Create One Embedding For An Entire PDF?

Answer:

A single embedding loses fine-grained topic information, reducing retrieval accuracy. Chunk-level embeddings provide more precise retrieval.

---

## Q3. What Is Metadata?

Answer:

Metadata is additional information stored with a chunk, such as page number, chunk identifier, document name, or section.

---

## Q4. Why Store Page Numbers?

Answer:

Page numbers enable citations, traceability, debugging, and explainable AI responses.

---

## Q5. Why Store Chunk IDs?

Answer:

Multiple chunks may originate from the same page, so a unique identifier is required.

---

## Q6. Why Store Both Text And Embeddings?

Answer:

Embeddings are used for retrieval, while text is used for generation.

---

## Q7. What Is Document-Aware Chunking?

Answer:

Document-aware chunking uses natural document structure such as pages, sections, headings, or paragraphs instead of arbitrary character limits.

---

## Q8. What Is Chunk Overlap?

Answer:

Chunk overlap duplicates a portion of content between adjacent chunks to preserve context across boundaries.

---

## Q9. Why Are Citations Important?

Answer:

Citations increase trust, transparency, auditing capability, and source verification.

---

## Q10. What Is Source Traceability?

Answer:

Source traceability is the ability to identify exactly where retrieved information originated.

---

# Key Lessons Learned

1. PDFs require parsing before retrieval.
2. Metadata is essential for enterprise RAG.
3. Citations improve trust.
4. Document structure should influence chunking strategy.
5. Embeddings support retrieval.
6. Original text supports generation.
7. Retrieval and generation are separate systems.
8. Grounding reduces hallucinations.
9. Thresholding improves retrieval quality.
10. Source traceability is a critical enterprise requirement.
