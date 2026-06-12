# AI Fundamentals & Generative AI Notes

# 1. Artificial Intelligence (AI)

## Definition

Artificial Intelligence (AI) is the field of computer science focused on creating systems that can perform tasks that normally require human intelligence.

Examples:

* Understanding language
* Recognizing images
* Making decisions
* Solving problems
* Learning from data

---

## Types of AI

### Narrow AI

Designed for specific tasks.

Examples:

* ChatGPT
* Siri
* Google Translate
* Fraud Detection Systems

---

### General AI (AGI)

A hypothetical AI capable of performing any intellectual task a human can do.

Not yet achieved.

---

# Interview Questions

Q: What is AI?

Answer:
AI is the field of building systems capable of performing tasks that normally require human intelligence such as reasoning, learning, perception, and language understanding.

---

# 2. Machine Learning (ML)

## Definition

Machine Learning is a subset of AI that enables systems to learn patterns from data instead of relying on explicitly programmed rules.

Traditional Programming:

Input + Rules = Output

Machine Learning:

Input + Output = Rules (Model)

---

## Example

Fraud Detection

Traditional Programming:

if amount > 10000:
suspicious

Machine Learning:

Train on historical fraud data and let the model learn fraud patterns automatically.

---

# Interview Questions

Q: Difference between AI and ML?

Answer:
AI is the broader field of creating intelligent systems. ML is a subset of AI that learns patterns from data.

---

# 3. Features

## Definition

Features are input variables used by a machine learning model.

Examples:

Fraud Detection:

* Transaction Amount
* Currency
* Country
* Merchant Category
* Device Type

House Price Prediction:

* Area
* Number of Rooms
* Location
* Age of Property

---

## Important Point

Better features usually produce better models.

Garbage Features → Poor Model

Good Features → Better Predictions

---

# Interview Questions

Q: What are features?

Answer:
Features are measurable input variables used by a model to make predictions.

---

# 4. Labels

## Definition

Labels are the expected outputs used during training.

Examples:

Fraud Detection:

Features:
Amount = 10000
Country = US

Label:
Fraud

Email Classification:

Email Content

Label:
Spam

---

## Importance

Labels teach the model what correct output looks like.

---

# Interview Questions

Q: What is a label?

Answer:
A label is the correct target output associated with training data.

---

# 5. Training

## Definition

Training is the process of teaching a model using historical data.

During training:

Features + Labels → Model Learning

The model adjusts internal parameters to reduce prediction errors.

---

## Example

Train using:

Amount | Country | Fraud
1000 | NO | No
20000 | NG | Yes

The model learns fraud patterns.

---

# Interview Questions

Q: What happens during training?

Answer:
The model learns relationships between features and labels by minimizing prediction errors.

---

# 6. Inference

## Definition

Inference is the process of using a trained model to make predictions on new unseen data.

---

## Example

Input:

Amount = 15000
Country = NG

Prediction:

Fraud Probability = 92%

---

# Interview Questions

Q: Difference between Training and Inference?

Answer:
Training teaches the model using historical data. Inference uses the trained model to make predictions on new data.

---

# 7. Types of Machine Learning

## Supervised Learning

Training data contains labels.

Examples:

* Fraud Detection
* Spam Detection
* House Price Prediction

Goal:

Learn relationship between Features and Labels.

---

## Unsupervised Learning

Training data contains no labels.

Goal:

Discover hidden patterns.

Examples:

* Customer Segmentation
* Clustering
* Anomaly Detection

---

## Reinforcement Learning

An agent learns through rewards and penalties.

Examples:

* Self-driving Cars
* Robotics
* AlphaGo
* Game Playing

---

# Interview Questions

Q: Difference between Supervised and Unsupervised Learning?

Answer:
Supervised learning uses labeled data, while unsupervised learning discovers patterns from unlabeled data.

---

# 8. Regression

## Definition

Regression predicts continuous numerical values.

Examples:

* House Price
* Temperature
* Sales Forecasting

---

## Example

Input:

Area = 1500 sq ft

Output:

Price = 500000

---

# Interview Questions

Q: What is Regression?

Answer:
Regression is a supervised learning technique used to predict continuous values.

---

# 9. Classification

## Definition

Classification predicts categories or classes.

Examples:

* Fraud / Legit
* Spam / Not Spam
* Approved / Rejected

---

## Example

Input:

Transaction Data

Output:

Fraud

---

# Interview Questions

Q: What is Classification?

Answer:
Classification is a supervised learning technique used to predict discrete categories.

---

# 10. Neural Networks

## Definition

A neural network is a machine learning model inspired by the human brain.

It consists of:

* Input Layer
* Hidden Layers
* Output Layer

---

## Purpose

Learn complex patterns from data.

Used in:

* Image Recognition
* NLP
* Speech Recognition
* Generative AI

---

# Interview Questions

Q: What is a Neural Network?

Answer:
A neural network is a layered machine learning model capable of learning complex patterns and representations from data.

---

# 11. Weights

## Definition

Weights determine the importance of each feature.

Example:

House Price

Price = Weight × Area

Higher Weight → Higher Influence

---

# Interview Questions

Q: What is a Weight?

Answer:
A weight represents the importance of a feature in determining model predictions.

---

# 12. Bias

## Definition

Bias is an additional parameter that shifts model predictions.

Formula:

Prediction = Weight × Feature + Bias

---

## Purpose

Allows flexibility in fitting data.

---

# Interview Questions

Q: Why is Bias required?

Answer:
Bias allows the model to shift predictions and fit data more accurately.

---

# 13. Generative AI

## Definition

Generative AI creates new content instead of only predicting labels.

Examples:

* Text Generation
* Image Generation
* Code Generation
* Summarization

---

## Traditional ML vs Generative AI

Traditional ML:

Input → Prediction

Generative AI:

Input → New Content

---

# Interview Questions

Q: What is Generative AI?

Answer:
Generative AI creates new content such as text, images, code, or audio based on learned patterns from training data.

---

# 14. Large Language Models (LLMs)

## Definition

LLMs are deep learning models trained on massive text datasets.

Examples:

* GPT
* Llama
* Claude
* Gemini

---

## Capabilities

* Question Answering
* Summarization
* Coding
* Translation
* Reasoning

---

# Interview Questions

Q: What is an LLM?

Answer:
A Large Language Model is a deep learning model trained on large amounts of text data to understand and generate human language.

---

# 15. Tokens

## Definition

LLMs process tokens instead of words.

Examples:

"Artificial Intelligence"

may become multiple tokens.

---

## Why Important?

Tokens determine:

* Cost
* Speed
* Context Length

---

# Interview Questions

Q: What is a Token?

Answer:
A token is the smallest unit of text processed by an LLM.

---

# 16. Context Window

## Definition

The maximum amount of information an LLM can consider at one time.

Examples:

* Previous messages
* Documents
* Instructions

---

## Importance

Larger context windows allow processing larger documents and conversations.

---

# Interview Questions

Q: What is a Context Window?

Answer:
The context window is the maximum number of tokens an LLM can process in a single interaction.

---

# Key Learning Summary

1. AI is the broader field.
2. ML learns patterns from data.
3. Features are inputs.
4. Labels are expected outputs.
5. Training teaches models.
6. Inference makes predictions.
7. Regression predicts numbers.
8. Classification predicts categories.
9. Neural Networks learn complex patterns.
10. Generative AI creates content.
11. LLMs generate language.
12. Tokens are the units processed by LLMs.
13. Context Window determines how much information an LLM can remember.
