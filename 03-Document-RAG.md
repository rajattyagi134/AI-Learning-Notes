# Project 3 - Document RAG (Document Retrieval Augmented Generation)

# Project Goal

Build a chatbot capable of answering questions from a document.

Unlike FAQ RAG, the knowledge source is now a document rather than manually created FAQs.

The system should:

* Read a document
* Split it into chunks
* Generate embeddings
* Retrieve relevant chunks
* Generate answers from retrieved context

---

# Problem Statement

Suppose we have an Employee Handbook:

* Office Hours
* Vacation Policy
* Maternity Leave
* Paternity Leave
* Remote Work

Users should be able to ask:

How many vacation days do employees get?

How long is maternity leave?

Can employees work from home?

and receive accurate answers from the document.

---

# Why Not Use The Entire Document?

Imagine:

500 Page PDF

Sending the entire document to the LLM for every question is inefficient because:

1. Context Window Limits
2. High Cost
3. Slow Performance
4. Poor Retrieval Accuracy
5. Increased Hallucinations

---

# Solution

Use Retrieval Augmented Generation (RAG).

Question
↓
Retrieve Relevant Information
↓
Generate Answer

---

# Architecture

Document
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
Context Assembly
↓
LLM
↓
Answer

---

# Document Loading

## Definition

Reading the document into memory.

Example:

Employee Handbook

↓

Raw Text

---

# Chunking

## Definition

Splitting a large document into smaller meaningful sections.

---

# Why Chunking?

One embedding for an entire document is usually too broad.

Example:

Employee Handbook

Contains:

* Vacation Policy
* Maternity Leave
* Remote Work

A single embedding mixes all topics.

Retrieval becomes less accurate.

---

# Better Approach

Create separate chunks.

Chunk 1

Office Hours

Chunk 2

Vacation Policy

Chunk 3

Maternity Leave

Chunk 4

Remote Work

Each chunk gets its own embedding.

---

# Key Principle

Small Meaningful Chunks

↓

Better Retrieval

---

# Naive Chunking

## Definition

Splitting based on character count.

Example:

```python
text[i:i+200]
```

Problem:

Words and sentences get split.

Example:

Unused vacat

ion expires after 2 years

Meaning is damaged.

---

# Problems With Naive Chunking

* Broken words
* Broken sentences
* Broken context
* Poor retrieval quality

---

# Semantic Chunking

## Definition

Splitting based on meaning boundaries.

Examples:

* Paragraphs
* Sections
* Headings
* Sentences

---

# Example

Vacation Policy

Employees receive 25 vacation days.

Unused vacation expires after 2 years.

Entire section remains together.

---

# Benefits

* Better context preservation
* Better embeddings
* Better retrieval

---

# Embeddings

## Definition

Numerical vector representations of text meaning.

Example:

Vacation Policy

↓

[0.12, -0.45, 0.88, ...]

---

# Purpose

Allow semantic comparison between chunks and questions.

---

# Key Principle

Similar Meaning

↓

Similar Embeddings

---

# Vector Store

## Definition

Storage containing:

Chunk Text
+
Embedding Vector

Example:

{
"text": "Vacation Policy...",
"embedding": [...]
}

---

# Simple Vector Store Used

```python
documents = [
    {
        "text": "...",
        "embedding": [...]
    }
]
```

---

# Similarity Search

## Definition

Comparing question embeddings with document embeddings.

---

# Process

Question

↓

Embedding

↓

Compare Against Every Chunk

↓

Similarity Score

---

# Example

Question:

How many vacation days do employees get?

Scores:

Vacation Policy → 375

Remote Work → 301

Support Contact → 180

Highest score wins.

---

# Top-K Retrieval

## Definition

Retrieve multiple relevant chunks.

---

# Why?

Information may be distributed across several chunks.

---

# Example

Question:

Explain employee leave policies.

Retrieved:

Vacation Policy

Maternity Leave

Paternity Leave

---

# Benefit

Provides richer context to the LLM.

---

# Threshold Filtering

## Definition

Reject low-quality retrieval results.

---

# Example

Question:

How do I apply for a visa?

Retrieved Score:

120

Threshold:

250

Result:

I don't have enough information to answer that.

---

# Why Thresholds Matter

Prevent:

* Hallucinations
* Wrong answers
* Irrelevant context

---

# Absolute Threshold

Example:

```python
SIMILARITY_THRESHOLD = 300
```

Only chunks above 300 are accepted.

---

# Relative Threshold

## Definition

Compare chunks against the best retrieved score.

Example:

Top Score:

420

Relative Threshold:

420 × 0.90

=

378

Only chunks scoring above 378 are kept.

---

# Why Relative Thresholds?

Adaptive filtering.

Different questions produce different score ranges.

---

# Context Assembly

## Definition

Combining retrieved chunks into a single context.

---

# Example

Retrieved:

Vacation Policy

Maternity Leave

Paternity Leave

---

Combined Context:

Vacation Policy...

Maternity Leave...

Paternity Leave...

---

# Purpose

Provide the LLM with all relevant information.

---

# Retrieval

## Definition

Finding relevant information.

---

# Responsibilities

* Generate query embedding
* Compare embeddings
* Rank results
* Filter results
* Retrieve relevant chunks

---

# Generation

## Definition

Creating a natural language answer using retrieved context.

---

# Responsibilities

* Read retrieved context
* Synthesize information
* Generate answer
* Avoid hallucination

---

# Retrieval vs Generation

Retrieval

↓

Find Information

Generation

↓

Explain Information

---

# Example

Question:

How long is maternity leave?

Retrieval:

Maternity Leave Chunk

Generation:

Employees are entitled to 12 months of maternity leave.

---

# Grounding

## Definition

Restricting the LLM to answer only from retrieved context.

---

# Example Prompt

Answer ONLY using the provided context.

Do not use outside knowledge.

If information is unavailable, reply:

"I don't have enough information to answer that."

---

# Why Grounding?

Reduces hallucinations.

Improves trustworthiness.

---

# Hallucinations

## Definition

Generating information not present in retrieved context.

---

# Example

Question:

What employee benefits exist?

Context:

EMPLOYEE HANDBOOK

Bad Answer:

Medical Insurance
401(k)
Dental Coverage

Hallucination.

---

# Root Cause

Weak retrieval.

Poor context.

Insufficient grounding.

---

# RAG Failure Modes

## Failure 1

Bad Chunking

↓

Poor Context

---

## Failure 2

Bad Retrieval

↓

Wrong Chunks

---

## Failure 3

Bad Generation

↓

Incorrect Answer

---

# Precision vs Recall

## Precision

Retrieve highly relevant chunks only.

Pros:

* Cleaner context

Cons:

* May miss information

---

## Recall

Retrieve more chunks.

Pros:

* More complete information

Cons:

* More noise

---

# Tradeoff

Enterprise RAG systems continuously tune:

* Chunk Size
* Top-K
* Thresholds
* Reranking

to balance Precision and Recall.

---

# Key Lessons Learned

1. Better chunking improves retrieval.
2. Semantic chunking is superior to fixed-size chunking.
3. Embeddings capture meaning.
4. Similarity search powers retrieval.
5. Retrieval quality strongly affects answer quality.
6. Top-K retrieval improves context coverage.
7. Thresholds reduce hallucinations.
8. Grounding improves reliability.
9. Retrieval and Generation are separate systems.
10. RAG = Retrieval + Generation.

---

# Interview Questions

## Q1. What is Document RAG?

Answer:

Document RAG is a Retrieval Augmented Generation system that retrieves relevant information from documents and uses an LLM to generate grounded answers.

---

## Q2. Why Do We Need Chunking?

Answer:

Large documents contain multiple topics. Chunking creates smaller meaningful sections that improve retrieval accuracy.

---

## Q3. What Is Semantic Chunking?

Answer:

Semantic chunking splits documents based on meaning boundaries such as sections, paragraphs, or headings rather than fixed character counts.

---

## Q4. What Is Top-K Retrieval?

Answer:

Top-K retrieval returns multiple relevant chunks so the LLM can answer using broader context.

---

## Q5. What Is Grounding?

Answer:

Grounding restricts the model to answer only from retrieved context, reducing hallucinations.

---

## Q6. What Is The Difference Between Retrieval And Generation?

Answer:

Retrieval finds relevant information. Generation converts retrieved information into natural language answers.

---

## Q7. Why Use Thresholds?

Answer:

Thresholds filter out weak retrieval results and prevent irrelevant information from reaching the LLM.

---

## Q8. What Causes Hallucinations In RAG?

Answer:

Poor retrieval, weak context, inadequate grounding, or model reasoning errors can cause hallucinations.

---

# Final Architecture

Document
↓
Semantic Chunking
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

This architecture forms the foundation of:

* PDF Chatbots
* Enterprise Knowledge Assistants
* HR Assistants
* Compliance Systems
* Legal Research Assistants
* Internal Company Copilots
