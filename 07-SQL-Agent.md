# Project 07 - Production SQL Assistant

## Project Objective

The goal of this project is to build a production-style SQL Assistant capable of answering natural language questions by converting them into SQL, executing them on a relational database, and generating a human-friendly response.

Unlike RAG, which retrieves text from documents, this project retrieves structured data from a SQL database.

---

# Project Architecture

```text
                User
                  │
                  ▼
         Natural Language Question
                  │
                  ▼
          Schema Discovery
                  │
                  ▼
             SQL Generator
                  │
                  ▼
            SQL Validator
                  │
                  ▼
            SQL Executor
                  │
                  ▼
             SQLite Database
                  │
                  ▼
              Query Result
                  │
                  ▼
              LLM Wrapper
                  │
                  ▼
            Natural Language
                  │
                  ▼
              Final Answer
```

---

# Project Structure

```text
07-SQL-Agent/

│── app.py
│── agent.py
│── database.py
│── schema.py
│── sql_generator.py
│── sql_validator.py
│── sql_executor.py
│── llm.py
│── create_database.py
│── populate_database.py
│── employees.db
│── notes.md
```

---

# SQL Assistant Workflow

```text
User Question
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

# Components

## 1. Database Layer

Responsible for:

* SQLite connection
* Executing SQL
* Returning structured results
* Transaction management

Implemented in:

```text
database.py
```

---

## 2. Schema Discovery

Instead of hardcoding tables and columns, the assistant reads them automatically from SQLite.

Example:

```text
Table: employees

- id
- name
- salary
- department_id
```

Benefits:

* No prompt updates
* Automatically adapts to schema changes
* Production-friendly

Implemented in:

```text
schema.py
```

---

## 3. SQL Generator

Input:

```text
Question
+
Schema
```

Output:

```sql
SELECT ...
```

Uses:

* Llama3
* Prompt Engineering
* Dynamic Schema Injection

Implemented in:

```text
sql_generator.py
```

---

# Prompt Engineering

The SQL Generator prompt enforces:

* Return SQL only
* SQLite syntax
* SELECT statements only
* No explanations
* No markdown
* Case-insensitive comparisons
* Prefer LOWER() for text matching

Example:

```sql
WHERE LOWER(department_name)=LOWER('finance')
```

instead of

```sql
WHERE department_name='Finance'
```

This makes the assistant robust to user input such as:

```text
Finance
finance
FINANCE
FiNaNcE
```

---

# SQL Validator

Never trust LLM output directly.

Validator blocks:

* INSERT
* UPDATE
* DELETE
* DROP
* ALTER
* CREATE
* TRUNCATE
* REPLACE
* ATTACH
* DETACH
* PRAGMA

Only SELECT statements are allowed.

Purpose:

* Security
* Read-only access
* Prevent destructive queries

Implemented in:

```text
sql_validator.py
```

---

# SQL Executor

Responsible for:

* Executing SQL
* Exception handling
* Returning structured results

Output:

```python
[
    {
        "name":"John",
        "salary":175000
    }
]
```

instead of tuples.

Implemented in:

```text
sql_executor.py
```

---

# LLM Wrapper

Instead of calling:

```python
ollama.chat(...)
```

everywhere,

we created

```text
llm.py
```

Benefits:

* Single LLM interface
* Easy model replacement
* Cleaner architecture
* Reusable across projects

Future:

```text
Llama3
↓

GPT-5

Claude

Gemini

DeepSeek
```

No other code changes required.

---

# Agent

The orchestrator.

Responsibilities:

* Read schema
* Generate SQL
* Validate SQL
* Execute SQL
* Generate final answer

Implemented in:

```text
agent.py
```

---

# Data Model

Database:

```text
employees.db
```

Tables:

```text
departments
```

```text
employees
```

```text
projects
```

Relationships:

```text
Departments
      ▲
      │ department_id
      │
Employees
      ▲
      │ employee_id
      │
Projects
```

Supports:

* JOIN
* Aggregation
* Filtering
* Sorting
* Counting

---

# Structured vs Unstructured Retrieval

## RAG

```text
Question
↓

Embedding

↓

Vector Search

↓

Relevant Text

↓

LLM
```

---

## SQL Agent

```text
Question
↓

LLM

↓

SQL

↓

Database

↓

Rows

↓

LLM
```

Difference:

RAG retrieves text.

SQL Agent retrieves structured rows.

---

# Schema Discovery

Instead of

```text
Hardcoded Prompt
```

we built

```text
Dynamic Schema Discovery
```

Advantages:

* Schema changes automatically detected
* Easier maintenance
* Production-ready

---

# SQL Validation

Pipeline:

```text
LLM

↓

Generated SQL

↓

Validator

↓

Safe?

↓

Execute
```

This prevents dangerous SQL from reaching the database.

---

# Case-Insensitive SQL

Problem:

```sql
WHERE department='finance'
```

didn't match

```text
Finance
```

Solution:

```sql
WHERE LOWER(department_name)=LOWER('finance')
```

Production systems should never rely on exact capitalization from users.

---

# Logging

Added:

* Generated SQL
* Rows Returned
* Execution Time

Example:

```text
Question:
Who earns the highest salary?

Generated SQL:

SELECT name,salary
FROM employees
ORDER BY salary DESC
LIMIT 1;

Rows Returned:
1

Execution Time:
8 ms
```

Benefits:

* Debugging
* Monitoring
* Performance analysis

---

# Pretty Output

Instead of raw Python objects:

```python
[
 {"name":"John"}
]
```

Assistant displays:

```text
Generated SQL
---------------------

SELECT ...

---------------------

Answer

John earns the highest salary.

Rows Returned : 1

Execution Time : 8 ms
```

Much more professional.

---

# Architecture Evolution

### Website RAG

```text
Question

↓

Retriever

↓

Context

↓

LLM
```

---

### SQL Assistant

```text
Question

↓

Schema

↓

LLM

↓

SQL

↓

Database

↓

Rows

↓

LLM
```

---

# Production Improvements

Implemented:

* Dynamic Schema Discovery
* SQL Validation
* LLM Wrapper
* Structured Results
* Case-insensitive Queries
* SQL Logging
* Execution Timing
* Pretty Output
* Modular Architecture

---

# Design Principles

Applied:

* Single Responsibility Principle (SRP)
* Separation of Concerns
* DRY (Don't Repeat Yourself)
* Modular Design
* Reusable Components

---

# Interview Questions

## What is a SQL Agent?

A SQL Agent converts natural language into SQL, executes the query on a relational database, and converts the result back into natural language.

---

## Difference Between SQL Agent and RAG?

RAG retrieves unstructured text using embeddings.

SQL Agents retrieve structured data using SQL queries.

---

## Why Dynamic Schema Discovery?

It automatically adapts to database changes without modifying prompts or application code.

---

## Why SQL Validation?

LLMs can generate unsafe SQL.

Validation ensures only safe read-only queries are executed.

---

## Why Create an LLM Wrapper?

To centralize all LLM interactions, reduce duplicate code, and make it easy to switch models without changing the rest of the application.

---

## Why Return Dictionaries Instead of Tuples?

Dictionaries preserve column names, making the output easier to understand, debug, and consume by LLMs and APIs.

---

## Why Use LOWER()?

To support case-insensitive user input and improve the reliability of text-based SQL queries.

---

## Why Log SQL?

Logging helps with:

* Debugging
* Performance monitoring
* Query analysis
* Production support

---

# Key Learnings

* SQL Agents work on structured data.
* Schema discovery removes hardcoded prompts.
* LLMs generate SQL instead of retrieving text.
* SQL validation protects the database.
* Execution and answer generation are separate steps.
* Wrapping LLM calls improves maintainability.
* Production systems prioritize safety, observability, and modularity.

---

# Final Architecture

```text
User
 │
 ▼
SQL Agent
 │
 ├── Schema Discovery
 ├── SQL Generator
 ├── SQL Validator
 ├── SQL Executor
 ├── SQLite Database
 └── LLM Wrapper
        │
        ▼
     Final Answer
```

---

# Project Outcome

```text
SQLite Database                 ✅
Enterprise Schema               ✅
Automatic Schema Discovery      ✅
LLM SQL Generation              ✅
SQL Validation                  ✅
SQL Execution                   ✅
Natural Language Response       ✅
LLM Wrapper                     ✅
Case-Insensitive Queries        ✅
SQL Logging                     ✅
Execution Timing                ✅
Pretty Output                   ✅
Production-Style Architecture   ✅
```

🏆 Successfully built a production-style SQL Assistant capable of translating natural language into SQL, safely executing queries, and generating human-readable responses using a modular, enterprise-inspired architecture.
