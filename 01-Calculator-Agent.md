# Project 1 - Calculator Agent

# Project Goal

Build an AI Agent capable of:

* Understanding user requests
* Deciding whether a calculator is needed
* Extracting mathematical expressions
* Executing calculations
* Returning results

This project introduces the core concepts of Agentic AI.

---

# Problem Statement

A normal chatbot can answer questions.

However:

User:

What is 500 divided by 10?

A chatbot may answer:

50

But it is performing reasoning only.

Instead, we want the model to:

1. Decide a calculator is needed
2. Generate a valid mathematical expression
3. Call a calculator tool
4. Return the result

This introduces Tool Calling.

---

# Initial Architecture

User
↓
LLM
↓
Answer

Example:

User:
What is AI?

LLM:
Artificial Intelligence is...

This works for general conversations.

---

# Agent Architecture

User
↓
Router Agent
↓
Tool Selection
↓
Tool Execution
↓
Result

Example:

User:
What is 500 divided by 10?

Router:
Use Calculator

Calculator:
500 / 10

Result:
50

---

# Messages

LLMs communicate using messages.

Example:

```python
messages = [
    {
        "role": "user",
        "content": "Hello"
    }
]
```

Messages form the conversation history.

---

# Roles

Three important roles:

## System

Defines behavior.

Example:

You are a routing agent.

---

## User

Represents user requests.

Example:

What is 500 divided by 10?

---

## Assistant

Represents model responses.

Example:

50

---

# System Prompt

## Definition

The System Prompt defines the model's job.

Think of it as a job description.

---

## Example

You are a routing agent.

Determine whether the user needs:

* CALCULATOR
* CHAT

Return only JSON.

---

## Importance

System prompts strongly influence model behavior.

Without a system prompt:

Model acts as a general assistant.

With a system prompt:

Model performs a specific role.

---

# Prompt Engineering

## Definition

Prompt Engineering is the process of designing prompts that guide model behavior.

---

## Goal

Improve:

* Reliability
* Accuracy
* Consistency

---

## Example

Bad Prompt:

Answer this question.

Good Prompt:

You are a routing agent.
Return only JSON.

---

# Few-Shot Prompting

## Definition

Providing examples to teach the model patterns.

---

## Example

User:
What is 500 divided by 10?

Output:

{
"tool":"CALCULATOR",
"expression":"500/10"
}

The model learns the pattern.

---

## Why It Works

The model generalizes from examples.

Example:

User:
What is 100 divided by 20?

Model infers:

{
"tool":"CALCULATOR",
"expression":"100/20"
}

---

# Agent

## Definition

An AI Agent is an AI system capable of:

* Reasoning
* Making decisions
* Using tools
* Executing actions

---

## Difference Between Chatbot and Agent

Chatbot:

User
↓
Answer

Agent:

User
↓
Reason
↓
Choose Tool
↓
Execute Tool
↓
Answer

---

# Tools

## Definition

Tools are external capabilities available to the model.

Examples:

* Calculator
* Search Engine
* Database
* Email Sender
* Web Browser

---

## Calculator Tool

Example:

```python
def calculate(expression):
    return eval(expression)
```

The calculator performs actual computation.

---

# Tool Calling

## Definition

The process of selecting and executing a tool.

---

## Example

User:

What is 500 divided by 10?

Agent:

Use Calculator

Tool Input:

500/10

Tool Output:

50

---

# Tool Routing

## Definition

Determining which tool should be used.

---

## Example

Question:

25 * 17

Route:

CALCULATOR

---

Question:

What is AI?

Route:

CHAT

---

# Structured Output

## Definition

Output formatted in a predictable machine-readable structure.

Example:

```json
{
  "tool":"CALCULATOR",
  "expression":"500/10"
}
```

---

## Why Structured Output?

Software systems require predictable formats.

Bad:

Use calculator for this.

Good:

{
"tool":"CALCULATOR"
}

---

# Benefits

* Easy parsing
* Reliable execution
* Easier integration

---

# Parameter Extraction

## Definition

Extracting information required by tools.

---

## Example

User:

What is 500 divided by 10?

Extracted:

500/10

---

## Why Important?

Tools require structured inputs.

Calculator:

500/10

Database:

SELECT * FROM users

Search:

Latest AI news

---

# Semantic Translation

## Definition

Converting human language into executable instructions.

---

## Example

Human Language:

What is 500 divided by 10?

Translated:

500/10

---

Human Language:

What is absolute value of -100?

Translated:

abs(-100)

---

# Validation

## Definition

Checking model output before execution.

---

## Why Needed?

LLMs can produce invalid outputs.

Example:

|-100|

instead of

abs(-100)

---

## Rule

Never trust model output blindly.

Always validate.

---

# Error Handling

## Example

Model Output:

{
"tool":"CALCULATOR"
}

Missing expression.

Without validation:

Application crashes.

With validation:

Return friendly error.

---

# Logging

## Definition

Recording system activity.

Example:

2026-06-11
Tool Selected:
CALCULATOR

Expression:
500/10

Result:
50

---

## Why Important?

Helps debugging agent behavior.

---

# Project Evolution

## Version 1

User
↓
Rules
↓
Calculator

---

## Version 2

User
↓
Router Agent
↓
Calculator

---

## Version 3

User
↓
Agent
↓
Structured Output
↓
Tool Execution
↓
Result

---

# Key Lessons Learned

1. Agents are more than chatbots.
2. System prompts define behavior.
3. Tools extend model capabilities.
4. Structured output is essential.
5. Parameter extraction is required.
6. Validation is critical.
7. LLM output should never be blindly trusted.
8. Tool routing enables intelligent decision making.
9. Semantic translation converts human language into machine-executable instructions.
10. Logging improves debugging and observability.

---

# Interview Questions

## Q1: What is an AI Agent?

Answer:

An AI Agent is a system capable of reasoning, selecting tools, executing actions, and returning results to achieve a goal.

---

## Q2: What is Tool Calling?

Answer:

Tool Calling is the process where an AI agent selects and invokes external tools to perform tasks beyond text generation.

---

## Q3: Why use Structured Output?

Answer:

Structured output provides predictable machine-readable responses that software systems can reliably parse and execute.

---

## Q4: What is Tool Routing?

Answer:

Tool Routing is the process of deciding which tool should be used for a specific user request.

---

## Q5: Why is Validation Important?

Answer:

LLMs can generate incorrect or malformed outputs. Validation ensures safe and reliable execution.

---

## Q6: What is Parameter Extraction?

Answer:

Parameter Extraction converts user requests into the structured inputs required by tools.

Example:

"What is 500 divided by 10?"

becomes

"500/10"

for the calculator tool.

---

# Real World Applications

* AI Assistants
* Research Agents
* SQL Agents
* Coding Agents
* Customer Support Agents
* Autonomous Workflow Systems
* Multi-Agent Architectures

All of these systems are built on the same concepts learned in this Calculator Agent project.
