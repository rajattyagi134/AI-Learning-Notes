# Project 09 - Hybrid Enterprise AI Agent

## Project Objective

The goal of this project is to build a Hybrid Enterprise AI Agent capable of answering user questions using multiple knowledge sources.

In previous projects, we built:

- Website RAG Assistant (Project 06)
- Production SQL Assistant (Project 07)
- Oracle SQL Assistant (Project 08)

Each application worked independently and could answer only one type of question.

The objective of this project is to combine these independent AI systems into a single intelligent agent capable of:

- Understanding the user's intent
- Selecting the appropriate knowledge source
- Calling the correct AI agent
- Combining responses when multiple knowledge sources are required

Unlike previous projects, this system introduces **AI-based routing** and **tool orchestration**, making it our first Agentic AI application.

---

# Project Architecture

```text
                          User
                            │
                            ▼
                 Natural Language Question
                            │
                            ▼
                      Hybrid Agent
                            │
                            ▼
                        AI Router
                            │
          ┌─────────────────┼─────────────────┐
          ▼                                   ▼
     Oracle Agent                      Website RAG Agent
          │                                   │
          ▼                                   ▼
     Oracle Database                     ChromaDB
          │                                   │
          └───────────────┬───────────────────┘
                          ▼
                  Context Merger (LLM)
                          │
                          ▼
                  Natural Language Answer
```

---

# Project Structure

```text
09-Hybrid-AI-Agent/

│── app.py
│── agent.py
│── router.py
│── llm.py
│── notes.md
│
├── oracle/
│      │── __init__.py
│      │── agent.py
│      │── database.py
│      │── oracle_config.py
│      │── schema.py
│      │── sql_executor.py
│      │── sql_generator.py
│      │── sql_validator.py
│
├── rag/
│      │── __init__.py
│      │── agent.py
│      │── embeddings.py
│      │── vector_store.py
│      │── website_util.py
│      │── chroma_db/
│
├── test_router.py
├── test_rag.py
└── test_oracle.py
```

---

# Hybrid AI Workflow

```text
User Question
        │
        ▼
Hybrid Agent
        │
        ▼
AI Router
        │
        ▼
Determine Appropriate Tool
        │
 ┌──────┼───────────────┐
 ▼      ▼               ▼
SQL    RAG             BOTH
 │      │               │
 ▼      ▼               ▼
Oracle ChromaDB   Oracle + ChromaDB
 │      │               │
 └──────┴───────────────┘
        │
        ▼
Merge Responses
        │
        ▼
Generate Final Answer
```

---

# Components

## 1. Hybrid Agent

The Hybrid Agent is the main orchestrator of the application.

Unlike previous projects where a single agent handled every request, the Hybrid Agent coordinates multiple specialized AI agents.

Responsibilities:

- Receive user question
- Ask Router which tool to use
- Invoke Oracle Agent or RAG Agent
- Invoke both agents if required
- Merge responses
- Return final answer

Implemented in:

```text
agent.py
```

---

## 2. AI Router

The Router is responsible for deciding which AI Agent should answer the user's question.

Instead of answering questions itself, it classifies each question into one of three categories.

Possible Outputs:

```text
SQL
```

```text
RAG
```

```text
BOTH
```

Implemented in:

```text
router.py
```

---

# Why Router?

Without Router:

```text
Question
     │
     ▼
Single AI Agent
```

Every question goes to the same system.

With Router:

```text
Question
     │
     ▼
Determine Best Tool
     │
 ┌───┴────┐
 ▼        ▼
SQL      RAG
```

Benefits:

- Better accuracy
- Better scalability
- Cleaner architecture
- Easier addition of future agents

---

# Router Examples

Question

```text
How many companies are there?
```

Decision

```text
SQL
```

---

Question

```text
Tell me about Rajat's AI projects.
```

Decision

```text
RAG
```

---

Question

```text
How many companies are there and tell me about Rajat's AI projects.
```

Decision

```text
BOTH
```

---

# Router Prompt Engineering

The Router uses Prompt Engineering to classify user questions.

The LLM is instructed to return **only JSON**.

Example Output:

```json
{
    "tool":"SQL"
}
```

```json
{
    "tool":"RAG"
}
```

```json
{
    "tool":"BOTH"
}
```

Returning structured JSON simplifies parsing and integration inside Python.

---

# Website RAG Agent

Instead of creating another RAG implementation, we reused the complete Website RAG Agent developed in Project 06.

Responsibilities:

- Generate embeddings
- Perform semantic search
- Retrieve relevant website sections
- Build contextual information
- Generate grounded answers

Implemented in:

```text
rag/agent.py
```

---

# Website RAG Workflow

```text
Question
      │
      ▼
Generate Embedding
      │
      ▼
Vector Search
      │
      ▼
Retrieve Relevant Sections
      │
      ▼
Build Context
      │
      ▼
LLM
      │
      ▼
Answer
```

---

# Reusing Existing Components

Instead of rebuilding the entire RAG system, we reused components developed in Project 06.

Reused Components:

```text
Embeddings
```

```text
Vector Store
```

```text
Website Agent
```

```text
ChromaDB
```

Only minor modifications were required to integrate them into the Hybrid Agent.

---

# Standard Response Format

Originally, the Website Agent returned:

```python
(
    answer,
    retrieved_docs
)
```

To support multiple agents, we standardized every agent to return the same response structure.

Example:

```python
{
    "answer": "...",
    "sources": [...]
}
```

Benefits:

- Common interface
- Easier orchestration
- Cleaner code
- Better scalability

---

# Benefits of Modular Design

Instead of building one large application, the project is divided into multiple independent components.

```text
Hybrid Agent
```

```text
Router
```

```text
Website Agent
```

```text
Oracle Agent
```

Each component has a single responsibility.

Benefits:

- Easier testing
- Easier debugging
- Better maintainability
- Code reuse
- Production-style architecture

---

# End of Part 1

# Oracle Agent

Instead of directly embedding SQL generation inside the Hybrid Agent, we reused the Oracle SQL Assistant developed in Project 08 and converted it into an independent AI Agent.

The Oracle Agent is responsible for answering questions that require structured data from the Oracle database.

Implemented in:

```text
oracle/agent.py
```

---

# Oracle Agent Workflow

```text
Question
      │
      ▼
Read Database Schema
      │
      ▼
Generate SQL using LLM
      │
      ▼
Validate SQL
      │
      ▼
Execute SQL
      │
      ▼
Fetch Database Rows
      │
      ▼
Generate Natural Language Answer
```

---

# Reusing Existing Components

Instead of writing a new SQL Assistant, we reused the complete Oracle SQL project.

Reused Components:

```text
Database Connection
```

```text
Schema Discovery
```

```text
SQL Generator
```

```text
SQL Validator
```

```text
SQL Executor
```

Only a new wrapper class (OracleAgent) was created.

---

# Oracle Agent Responsibilities

The Oracle Agent is responsible for:

- Reading database schema
- Generating SQL
- Validating generated SQL
- Executing SQL
- Returning structured results
- Generating human-friendly answers

Implemented in:

```text
oracle/agent.py
```

---

# Why Create OracleAgent?

Project 08 already worked.

However, the Hybrid Agent requires every tool to expose the same interface.

Instead of calling multiple classes separately:

```python
schema.get_schema()

generator.generate_sql()

executor.execute()

validator.validate()
```

the Hybrid Agent simply calls:

```python
response = oracle_agent.answer_question(question)
```

This hides all implementation details from the Hybrid Agent.

---

# Common Interface

One important design decision in this project was making every AI Agent expose the same public method.

Example:

```python
answer_question(question)
```

Regardless of the underlying implementation.

Website Agent

```python
answer_question(question)
```

Oracle Agent

```python
answer_question(question)
```

Future Agents

```python
WeatherAgent.answer_question()

EmailAgent.answer_question()

GitHubAgent.answer_question()
```

This is an example of abstraction.

The Hybrid Agent doesn't need to know how an individual agent works internally.

---

# Tool Calling

Instead of writing large if-else blocks inside the application, every specialized AI component becomes a Tool.

Examples:

```text
Oracle Agent
```

```text
Website RAG Agent
```

Future tools may include:

```text
Weather Agent
```

```text
Email Agent
```

```text
GitHub Agent
```

The Router decides which tool should be called.

---

# Hybrid Agent Execution

The Hybrid Agent coordinates the complete workflow.

Pipeline:

```text
User Question
        │
        ▼
AI Router
        │
Determine Tool
        │
 ┌──────┼──────────────┐
 ▼      ▼              ▼
SQL    RAG           BOTH
 │      │              │
 ▼      ▼              ▼
Oracle Website     Oracle + Website
Agent    Agent       Agents
        │
        ▼
Generate Final Answer
```

---

# SQL Requests

Example:

Question

```text
How many companies are there?
```

Execution:

```text
Router
      │
      ▼
SQL
      │
      ▼
Oracle Agent
      │
      ▼
Oracle Database
      │
      ▼
Answer
```

Only the Oracle Agent is executed.

---

# RAG Requests

Example:

Question

```text
Tell me about Rajat's AI projects.
```

Execution:

```text
Router
      │
      ▼
RAG
      │
      ▼
Website Agent
      │
      ▼
ChromaDB
      │
      ▼
Answer
```

Only the Website Agent is executed.

---

# Hybrid Requests

Example:

Question

```text
How many companies are there and tell me about Rajat's AI projects.
```

Execution:

```text
Router
      │
      ▼
BOTH
      │
 ┌────┴────┐
 ▼         ▼
Oracle   Website
Agent     Agent
 │         │
 ▼         ▼
Oracle   ChromaDB
 │         │
 └────┬────┘
      ▼
Merge Answers
      ▼
Final Answer
```

Both agents work independently before their responses are merged.

---

# Context Merging

When Router returns:

```text
BOTH
```

both agents execute independently.

Instead of merging responses using Python string concatenation, we ask the LLM to combine them.

Pipeline:

```text
Oracle Answer
       │
       ▼
Website Answer
       │
       ▼
LLM
       │
       ▼
Professional Final Answer
```

Benefits:

- Better readability
- Natural flow
- Removes duplicate information
- Produces coherent responses

---

# LLM Wrapper

As in previous projects, all interactions with Ollama are centralized.

Instead of calling:

```python
ollama.chat(...)
```

throughout the application,

we created:

```text
llm.py
```

Benefits:

- Single LLM interface
- Cleaner architecture
- Easy model replacement
- Reusable across all projects

Current Model:

```text
Llama3
```

Future Models:

```text
GPT-5
Claude
Gemini
DeepSeek
```

No application code changes are required.

---

# Separation of Responsibilities

Each component performs exactly one task.

Hybrid Agent

```text
Coordinates Agents
```

Router

```text
Selects Tool
```

Oracle Agent

```text
Answers Database Questions
```

Website Agent

```text
Answers Document Questions
```

LLM Wrapper

```text
Communicates with LLM
```

This follows the Single Responsibility Principle (SRP).

---

# Benefits of This Architecture

Compared to previous projects, this architecture introduces:

- AI-based decision making
- Tool orchestration
- Agent abstraction
- Common interfaces
- Component reuse
- Better scalability
- Cleaner architecture

The Hybrid Agent can now be extended by simply adding new agents without changing the existing implementation.

---

# End of Part 2

# Routing Logic

The Router is the first component executed by the Hybrid Agent.

Instead of answering questions, its responsibility is to classify the user's request and decide which AI Agent should handle it.

Routing Pipeline:

```text
User Question
      │
      ▼
AI Router
      │
      ▼
Determine Tool
      │
 ┌────┼─────────┐
 ▼    ▼         ▼
SQL  RAG      BOTH
```

The Router returns only one of the following values:

```text
SQL
```

```text
RAG
```

```text
BOTH
```

The Hybrid Agent then invokes the appropriate agent(s).

---

# Agent Orchestration

Unlike previous projects, the Hybrid Agent does not contain business logic for SQL generation or RAG retrieval.

Instead, it orchestrates specialized agents.

Workflow:

```text
Question
     │
     ▼
Router
     │
     ▼
Choose Agent
     │
 ┌───┴─────────────┐
 ▼                 ▼
Oracle Agent   Website Agent
     │                 │
     ▼                 ▼
Oracle DB        ChromaDB
     │                 │
     └───────┬─────────┘
             ▼
      Merge Responses
             ▼
        Final Answer
```

This makes the Hybrid Agent lightweight and easy to extend.

---

# Agent Communication

Each agent works independently.

Example:

```python
oracle_response = oracle_agent.answer_question(question)

rag_response = rag_agent.answer_question(question)
```

Neither agent knows about the other.

The Hybrid Agent is responsible for coordinating them.

---

# Common Response Object

Every agent returns the same response structure.

Example:

```python
{
    "answer": "...",
    "sources": [...]
}
```

Benefits:

* Consistent interface
* Easier orchestration
* Simpler debugging
* Future extensibility

---

# Problems Faced During Development

## Relative Import Issues

After moving the Oracle and RAG code into separate folders, Python could no longer locate modules.

Example:

```text
ModuleNotFoundError
```

Solution:

Instead of

```python
from schema import SchemaManager
```

we changed to

```python
from oracle.schema import SchemaManager
```

Similarly,

```python
from database import Database
```

became

```python
from oracle.database import Database
```

We also added

```text
__init__.py
```

inside both folders to convert them into Python packages.

---

# Empty LLM Wrapper

During development, the application failed with:

```text
ImportError:
cannot import name 'LLM'
```

Reason:

The file

```text
llm.py
```

was accidentally empty.

Solution:

Created a reusable LLM wrapper class.

Benefits:

* Single interface for Ollama
* Cleaner code
* Easier model replacement

---

# Empty ChromaDB

Initially, the Website Agent always returned:

```text
I don't have enough information to answer that.
```

Reason:

A new empty ChromaDB was created inside the project.

The vector database from Project 06 had not been copied.

Solution:

Copied the existing

```text
chroma_db
```

folder into the

```text
rag/
```

directory.

The Website Agent immediately started retrieving relevant website sections.

---

# Standardizing Agent Responses

The original Website Agent returned:

```python
(
    answer,
    retrieved_docs
)
```

The Oracle Agent returned:

```python
{
    ...
}
```

To simplify orchestration, both agents were updated to return the same response format.

Final format:

```python
{
    "answer": "...",
    "sources": [...]
}
```

This allowed the Hybrid Agent to work with any agent in the same way.

---

# Why Use a Common Interface?

Every agent exposes:

```python
answer_question(question)
```

This allows the Hybrid Agent to remain completely independent of the agent's internal implementation.

Example:

```python
response = agent.answer_question(question)
```

Whether the agent accesses:

* Oracle
* ChromaDB
* REST APIs
* Weather Service

the Hybrid Agent never changes.

---

# Architecture Evolution

## Project 06

Website RAG

```text
Question
    │
    ▼
Retriever
    │
    ▼
Context
    │
    ▼
LLM
```

---

## Project 08

Oracle SQL Assistant

```text
Question
    │
    ▼
Schema
    │
    ▼
LLM
    │
    ▼
SQL
    │
    ▼
Oracle
    │
    ▼
Rows
    │
    ▼
LLM
```

---

## Project 09

Hybrid Enterprise AI Agent

```text
Question
    │
    ▼
Router
    │
    ▼
Choose Agent
    │
 ┌──┴──────────┐
 ▼             ▼
SQL          RAG
 │             │
 ▼             ▼
Oracle     ChromaDB
 │             │
 └──────┬──────┘
        ▼
Merge Responses
        ▼
Final Answer
```

---

# Production Improvements

Implemented:

* AI Router
* Oracle Agent
* Website Agent
* Hybrid Agent
* Common Interface
* Tool Orchestration
* Response Standardization
* Code Reuse
* Package Organization
* Modular Architecture

---

# Benefits of Hybrid AI

Compared to standalone applications:

* Uses multiple knowledge sources
* Better answer quality
* Cleaner architecture
* Reusable agents
* Easy to extend
* Production-ready design

---

# Preparing for Future Projects

This project lays the foundation for:

* Multi-Agent Systems
* Agentic AI
* LangGraph
* MCP Servers
* AI Tool Calling

The Hybrid Agent will later evolve into a Manager Agent capable of coordinating many specialized AI agents.

---

# End of Part 3

# Design Principles

The Hybrid Enterprise AI Agent was designed using several software engineering principles.

## Single Responsibility Principle (SRP)

Each component has one responsibility.

```text
Hybrid Agent
```

Coordinates multiple agents.

```text
Router
```

Selects the appropriate AI Agent.

```text
Oracle Agent
```

Handles structured database questions.

```text
Website Agent
```

Handles unstructured website questions.

```text
LLM Wrapper
```

Communicates with the LLM.

---

## Separation of Concerns

Each layer is responsible for a single concern.

```text
Routing
```

↓

```text
Tool Selection
```

↓

```text
Data Retrieval
```

↓

```text
Answer Generation
```

This keeps the code modular and easier to maintain.

---

## Reusability

Instead of rewriting previous projects, we reused them.

Reused from Project 06:

- Embeddings
- ChromaDB
- Website RAG Agent

Reused from Project 08:

- Oracle Database
- Schema Discovery
- SQL Generator
- SQL Validator
- SQL Executor

Only new components were developed for this project.

---

## Modularity

The project is divided into independent modules.

```text
Router
```

```text
Oracle Agent
```

```text
Website Agent
```

```text
Hybrid Agent
```

Each module can be developed, tested and maintained independently.

---

## Extensibility

Adding a new AI Agent requires minimal changes.

Example:

```text
Weather Agent
```

```text
Email Agent
```

```text
GitHub Agent
```

```text
Slack Agent
```

The Router simply needs a new routing option.

---

# Interview Questions

## What is a Hybrid AI Agent?

A Hybrid AI Agent combines multiple AI systems and data sources to answer user questions. Instead of relying on a single retrieval mechanism, it intelligently selects the appropriate tool based on the user's request.

---

## Why Build a Hybrid AI Agent?

Different knowledge sources solve different problems.

- SQL databases store structured data.
- Vector databases store unstructured knowledge.

A Hybrid Agent combines both to produce more complete answers.

---

## What is the Role of the Router?

The Router analyzes the user's question and determines which AI Agent should answer it.

Possible routing decisions:

- SQL
- RAG
- BOTH

---

## Why Separate Oracle Agent and Website Agent?

Each agent specializes in a single task.

Oracle Agent:

- Structured data retrieval
- SQL generation
- Database interaction

Website Agent:

- Semantic search
- Context retrieval
- Document-based question answering

This separation improves modularity and maintainability.

---

## Why Standardize Agent Responses?

Every agent returns:

```python
{
    "answer": "...",
    "sources": [...]
}
```

Benefits:

- Consistent interface
- Easier orchestration
- Simpler integration
- Better scalability

---

## Why Use an LLM for Context Merging?

Instead of concatenating text manually, the LLM generates a coherent and natural response.

Benefits:

- Better readability
- Removes duplicate information
- Produces professional answers

---

## How Does the Hybrid Agent Decide Which Tool to Use?

The Router uses Prompt Engineering and an LLM to classify each question into one of three categories:

- SQL
- RAG
- BOTH

The Hybrid Agent then executes the selected tool(s).

---

## How Would You Extend This Architecture?

New agents can be added without changing existing components.

Examples:

- Weather Agent
- Email Agent
- GitHub Agent
- Filesystem Agent

Only the Router needs to be updated with new routing logic.

---

## Difference Between Project 08 and Project 09?

Project 08:

- One Oracle SQL Agent
- Single knowledge source

Project 09:

- Multiple AI Agents
- AI Router
- Tool orchestration
- Multiple knowledge sources
- Context merging

---

## Is This Agentic AI?

Yes.

This project introduces the fundamental concepts of Agentic AI:

- Decision making
- Tool selection
- Task orchestration
- Multiple specialized agents
- Context merging

It serves as the foundation for Multi-Agent AI Systems.

---

# Key Learnings

* Combined multiple AI systems into one application.
* Built the first AI Router for intelligent tool selection.
* Reused previous projects through modular design.
* Standardized agent interfaces for easier orchestration.
* Learned how specialized AI agents collaborate.
* Implemented context merging using an LLM.
* Understood the importance of abstraction and reusable components.
* Built the foundation for Agentic AI systems.

---

# Final Architecture

```text
                          User
                            │
                            ▼
                 Natural Language Question
                            │
                            ▼
                      Hybrid Agent
                            │
                            ▼
                        AI Router
                            │
          ┌─────────────────┼─────────────────┐
          ▼                                   ▼
     Oracle Agent                      Website RAG Agent
          │                                   │
          ▼                                   ▼
     Oracle Database                     ChromaDB
          │                                   │
          └───────────────┬───────────────────┘
                          ▼
                  Context Merger (LLM)
                          │
                          ▼
                  Natural Language Answer
                          │
                          ▼
                     Final Response
```

---

# Project Outcome

```text
AI Router                              ✅
Hybrid Agent                           ✅
Oracle Agent                           ✅
Website RAG Agent                      ✅
Tool Selection                         ✅
Tool Orchestration                     ✅
Context Merging                        ✅
Common Agent Interface                 ✅
Response Standardization               ✅
LLM Wrapper                            ✅
Code Reuse                             ✅
Modular Architecture                   ✅
Package Organization                   ✅
Production-Style Design                ✅
```

🏆 Successfully built a Hybrid Enterprise AI Agent capable of intelligently routing user questions, invoking multiple specialized AI agents, retrieving information from both structured and unstructured data sources, and generating a unified natural language response using a modular, enterprise-inspired architecture.

---

# Next Project

## Project 10 - Multi-Agent AI System

In this project, the Hybrid Agent will evolve into a **Manager Agent** capable of coordinating multiple specialized AI agents.

Instead of selecting a single tool, the Manager Agent will delegate tasks, collect responses from different agents, and combine them into a final answer.

Architecture Preview:

```text
                        User
                          │
                          ▼
                    Manager Agent
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
   Oracle Agent      RAG Agent        Web Agent
        │                 │                 │
        ▼                 ▼                 ▼
   Oracle DB         ChromaDB         Internet/API
        └─────────────────┼─────────────────┘
                          ▼
                   Response Aggregation
                          ▼
                     Final Answer
```

This project introduces the concepts of:

- Multi-Agent Collaboration
- Task Delegation
- Agent Communication
- Planning
- Agent Coordination

and prepares the foundation for learning **LangGraph**, **CrewAI**, and **MCP (Model Context Protocol)** in future projects.