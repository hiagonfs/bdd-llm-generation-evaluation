# BDD LLM Generation Evaluation

> Replication package for the experimental evaluation of Large Language Models (LLMs) and prompting strategies for automated Behavior-Driven Development (BDD) scenario generation.

This repository contains the datasets, prompts, generated BDD scenarios, evaluation metrics, human assessments, statistical analyses, and supporting scripts used in the study:

**“Evaluating LLMs and Prompting Strategies for BDD Scenario Generation: Reference-Based Performance, Stability, and Practitioner Perception.”**

---

## 📌 Overview

This study investigates the use of Large Language Models for automatically generating BDD scenarios in **Gherkin syntax** from natural-language software test cases.

The experimental evaluation compares:

- **5 open-weight LLMs**
- **3 prompting strategies**
  - Zero-shot
  - One-shot
  - Few-shot
- **259 software test cases**
- **10 independent runs per configuration**
- **38,850 generated BDD scenarios**

The study evaluates both the **reference-based quality** of the generated scenarios and their **stability across repeated executions**.

---

## 🤖 Evaluated Models

| Model | Repository |
|---|---|
| Qwen3-8B | `Qwen/Qwen3-8B` |
| Gemma 4 E4B IT | `google/gemma-4-E4B-it` |
| Granite 4.1 8B | `ibm-granite/granite-4.1-8b` |
| Llama 3 8B Instruct | `meta-llama/Meta-Llama-3-8B-Instruct` |
| Mistral 7B Instruct v0.3 | `mistralai/Mistral-7B-Instruct-v0.3` |

All models were executed in a self-hosted environment using the same experimental configuration.

---

## 💬 Prompting Strategies

Three prompting strategies were evaluated under a controlled prompt design.

| Strategy | Demonstrations |
|---|---:|
| Zero-shot | 0 |
| One-shot | 1 |
| Few-shot | 3 |

The same finalized prompt templates were consistently applied across all evaluated models.

---

## 📊 Evaluation

Generated scenarios were evaluated using automatic metrics and human assessment.

### Automatic Metrics

- **BERTScore F1**
- **METEOR**
- **Manhattan Distance**
- **Natural Language Inference (NLI)**

### Stability Analysis

Repeated executions were also analyzed to investigate generation variability and convergence.

The stability analysis includes:

- Run-level variability
- Coefficient of Variation (CV)
- Normalized Token-Level Levenshtein Distance
- Convergence across repeated executions

### Human Evaluation

A human evaluation was conducted to investigate how well automatic metrics reflect the quality of generated BDD scenarios.

The assessment considered:

- **Structure**
- **Semantic correspondence**
- **Level of detail**

---

## 🧪 Experimental Design

For each combination of:

```text
Test Case × Model × Prompting Strategy
