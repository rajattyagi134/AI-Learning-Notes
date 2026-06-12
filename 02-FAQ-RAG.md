# Project 2 - FAQ Chatbot (RAG Foundations)

# Project Goal

Build a chatbot capable of:

* Understanding user questions
* Finding semantically similar FAQs
* Retrieving relevant information
* Generating grounded answers
* Avoiding keyword-based search limitations

This project introduces the foundations of Retrieval Augmented Generation (RAG).

---

# Problem Statement

Traditional search relies on exact keywords.

Example:

FAQ:

What are your office hours?

User:

When do you open?

Keyword search:

No Match

Because words differ.

Humans understand the meaning is similar.

We need a system that understands meaning rather than exact words.

---

# Solution

Semantic Search using Embeddings.

---

# Initial Architecture

User Question
↓
Embedding
↓
Similarity Search
↓
Best FAQ
↓
Answer

This is Semantic Search.

---

# Final Architecture (RAG)

User Question
↓
Embedding
↓
Top-K Retrieval
↓
Context Assembly
↓
LLM
↓
Generated Answer

This is Retrieval Augmented Generation (RAG).

---

# Embeddings

## Definition

An embedding is a numerical vector representation of text meaning.

Example:

Text:

What are your office hours?

Embedding:

[0.12, -0.45, 0.88, ...]

The actual vector may contain hundreds or thousands of numbers.

---

# Purpose

Convert text into mathematical representations.

This allows computers to compare meaning.

---

# Key Principle

Similar Meaning
↓
Similar Vectors

---

# Example

Question A:

What are your office hours?

Question B:

When do you open?

The embeddings will be close together.

---

# Why Embeddings Matter

Embeddings power:

* Semantic Search
* RAG
* FAQ Chatbots
* PDF Q&A
* Website Chatbots
* Recommendation Systems
* Vector Databases

---

# Embedding Models

## Purpose

Generate embeddings instead of text.

---

## Example Models

* nomic-embed-text
* mxbai-embed-large
* OpenAI Embeddings

---

## Important Distinction

LLM:

Generates Text

Embedding Model:

Generates Vectors

Different responsibilities.

---

# Semantic Search

## Definition

Finding information based on meaning rather than exact keywords.

---

## Traditional Search

Keyword Matching

Example:

Office Hours

≠

When do you open?

No match.

---

## Semantic Search

Meaning Matching

Example:

Office Hours

≈

When do you open?

Match Found.

---

# Benefits

* Better retrieval
* Understands paraphrases
* More natural user interactions

---

# Similarity Search

## Definition

Comparing embedding vectors to determine semantic closeness.

---

## Goal

Find the most relevant information.

---

# Similarity Score

Higher Score

↓

More Similar Meaning

Lower Score

↓

Less Similar Meaning

---

# Example

Question:

When do you open?

FAQ A:

Office Hours

Score = 300

FAQ B:

Reset Password

Score = 180

FAQ A wins.

---

# Cosine Similarity

## Definition

A mathematical technique used to compare vector similarity.

---

## Why Use It?

Normalizes vector lengths.

Produces more reliable comparisons.

---

## Purpose

Measure semantic similarity between texts.

---

# Retrieval

## Definition

Finding relevant information before generation.

---

## Goal

Retrieve useful context.

---

# Example

Question:

How long is maternity leave?

Retrieved FAQ:

Employees are entitled to 12 months of maternity leave.

---

# Important Principle

Retrieval Quality

>

Model Quality

Poor retrieval often causes poor answers.

---

# Threshold

## Definition

Minimum similarity score required to accept a match.

---

## Why Needed?

Not every match is good.

---

## Example

Question:

How do I apply for a visa?

Best Score = 120

Threshold = 250

Result:

I don't have enough information.

---

## Benefits

* Prevents incorrect answers
* Improves reliability
* Reduces hallucinations

---

# Top-K Retrieval

## Definition

Retrieve multiple relevant documents instead of only one.

---

## Top-1 Retrieval

Retrieve:

Best Match Only

Problem:

Information may be incomplete.

---

## Top-K Retrieval

Retrieve:

Top 3
Top 5
Top 10

Most relevant results.

---

# Example

Question:

Explain vacation policy.

Retrieved:

Chunk A:
25 vacation days

Chunk B:
Can be carried forward

Chunk C:
Expire after 2 years

The answer requires all three.

---

# Why Top-K Works

Provides broader context.

Improves answer completeness.

---

# Context Assembly

## Definition

Combining retrieved information into a single context.

---

## Example

Retrieved FAQ 1:

25 vacation days

Retrieved FAQ 2:

Can be carried forward

Retrieved FAQ 3:

Expire after 2 years

---

Combined Context:

Employees receive 25 vacation days.
Unused days can be carried forward.
Unused days expire after 2 years.

---

# Purpose

Provide complete information to the LLM.

---

# Generation

## Definition

Creating a natural language response using an LLM.

---

## Example

Context:

Employees receive 25 vacation days.
Unused days can be carried forward.
Unused days expire after 2 years.

Question:

Explain vacation policy.

Generated Answer:

Employees receive 25 vacation days annually. Unused vacation days can be carried forward and expire after two years.

---

# Retrieval vs Generation

## Retrieval

Find Information

---

## Generation

Explain Information

---

# Important Difference

Retrieval:

Search

Generation:

Reason + Explain

---

# Retrieval Augmented Generation (RAG)

## Definition

Combining retrieval and generation.

Formula:

RAG

=

Retrieval

*

Generation

---

# Retrieval Stage

Question
↓
Embedding
↓
Similarity Search
↓
Top-K Retrieval

---

# Generation Stage

Retrieved Context
↓
LLM
↓
Natural Language Answer

---

# Why RAG Exists

LLMs do not know company-specific information.

RAG allows them to access external knowledge.

---

# Benefits of RAG

* More accurate answers
* Uses private knowledge
* Reduces hallucinations
* Works with PDFs and databases

---

# Grounding

## Definition

Forcing the model to answer only using retrieved information.

---

## Example Prompt

Answer only using the provided context.

If information is unavailable, say:

"I don't have enough information."

---

# Why Grounding Matters

Without grounding:

Model may answer using general knowledge.

With grounding:

Model stays aligned with retrieved data.

---

# Hallucination

## Definition

An answer that sounds correct but is unsupported by retrieved information.

---

## Example

Question:

How do I apply for a visa?

Context:

Office located in Oslo.

Bad Answer:

Visa applications require...

Hallucination.

---

# Key Insight

Bad Retrieval

*

LLM

=

Bad Answer

---

# Enterprise RAG Architecture

User Question
↓
Embedding Model
↓
Vector Search
↓
Top-K Retrieval
↓
Context Assembly
↓
LLM
↓
Answer

---

# FAQ Project Evolution

Version 1

User
↓
Embedding Search
↓
Best FAQ
↓
Return Stored Answer

---

Version 2

User
↓
Embedding Search
↓
Top-K Retrieval
↓
Context
↓
LLM
↓
Generated Answer

---

# Lessons Learned

1. Similar meaning produces similar embeddings.
2. Semantic search is better than keyword matching.
3. Embeddings are the foundation of RAG.
4. Retrieval quality is critical.
5. Top-K retrieval improves context.
6. Thresholds improve reliability.
7. RAG = Retrieval + Generation.
8. Grounding reduces hallucinations.
9. More context does not always mean better context.
10. LLMs require relevant context to generate accurate answers.

---

# Interview Questions

## Q1: What is an Embedding?

Answer:

An embedding is a numerical vector representation of text meaning that allows semantic comparison between texts.

---

## Q2: What is Semantic Search?

Answer:

Semantic search retrieves information based on meaning rather than exact keyword matches.

---

## Q3: What is Similarity Search?

Answer:

Similarity search compares embeddings to identify semantically similar content.

---

## Q4: What is RAG?

Answer:

Retrieval Augmented Generation combines retrieval of relevant information with LLM-based answer generation.

---

## Q5: Why Use Top-K Retrieval?

Answer:

Information may be distributed across multiple documents or chunks. Top-K retrieval provides richer context for answer generation.

---

## Q6: Why Use Thresholds?

Answer:

Thresholds prevent poor retrieval results from being treated as valid answers.

---

## Q7: What Causes Hallucinations in RAG?

Answer:

Hallucinations often occur due to poor retrieval, insufficient context, or lack of grounding instructions.

---

## Q8: Why Are Embeddings Important?

Answer:

Embeddings enable semantic understanding and form the foundation of semantic search and RAG systems.

---

# Real World Applications

* Enterprise Knowledge Assistants
* HR Chatbots
* Banking Compliance Assistants
* Legal Research Assistants
* Customer Support Bots
* Internal Company Copilots
* Document Search Systems
* PDF Q&A Systems

These applications all rely heavily on embeddings, retrieval, and RAG architectures.
