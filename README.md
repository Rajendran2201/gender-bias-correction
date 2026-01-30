# Gender-Inclusive Language Generation

**LT-EDI @ ACL 2026 Shared Task**

## Overview

This project addresses the **Gender-Inclusive Language Generation** shared task (LT-EDI @ ACL 2026), which aims to reduce gender bias in text generation while preserving semantic meaning and fluency.

Despite advances in large language models (LLMs), gender stereotypes, androcentric language, and misgendering remain prevalent in generated text. This project focuses on **controlled text transformation**, where gender-biased or gender-marked sentences are rewritten into **inclusive, gender-neutral, and contextually coherent alternatives**.

The system is designed with **minimal-edit principles**, prioritizing semantic preservation, neutrality, and consistency under LLM-based and human evaluation.

---

## Tasks Covered

### Subtask A: Gender-Inclusive Language Generation (Multilingual)

**Objective**
Rewrite gender-biased or gender-marked sentences into inclusive and gender-neutral alternatives while preserving meaning and fluency.

**Languages**

* English
* German
* Spanish
* Tamil
* Kannada

**Example**

**Input**

> “The fireman saved the little girl from the building.”

**Output**

> “The firefighter saved the child from the building.”

---

### Subtask B: Counterfactual Generation (English)

**Objective**
Generate empathetic, persuasive counter-narratives for gender-biased statements, removing gender essentialism without being confrontational or moralizing.

**Example**

**Input**

> “Women are not good at math.”

**Output**

> “People of all genders can be skilled in mathematics; ability is shaped by interest and opportunity, not gender.”

---

## Dataset Summary

| Subtask | Category                      | EN   | DE   | ES  | TA   | KN   |
| ------- | ----------------------------- | ---- | ---- | --- | ---- | ---- |
| A       | Gender Neutral Word Pairs     | 673  | –    | 200 | 742  | 693  |
| A       | Gender Neutral Sentence Pairs | 1074 | 1002 | 200 | 1074 | 1074 |
| B       | Counterfactual Sentence Pairs | 726  | –    | –   | –    | –    |

---

## Methodology

### Core Design Principles

* **Minimal change**: Modify only gender-relevant tokens or structures.
* **Semantic preservation**: Avoid paraphrasing or content expansion.
* **Tone restraint**: Neutral, non-activist, non-explanatory outputs.
* **Multilingual conservatism**: Extra caution for low-resource languages.

---

### System Architecture

The system follows a **hybrid rule-based + neural generation approach**:

1. **Bias Localization**

   * Identify gendered terms (roles, pronouns, kinship terms).
   * Classify bias type (lexical, grammatical, ideological).

2. **Rule-Guided Masking**

   * Replace gendered terms with placeholders (e.g., `<ROLE>`, `<PRON>`).
   * Reduces hallucination and over-generation.

3. **Model-Based Rewriting**

   * Instruction-tuned LLM fine-tuned with LoRA/QLoRA.
   * Focus on structure repair and fluency.

4. **Post-processing**

   * Deterministic replacement using language-specific dictionaries.
   * Grammar agreement fixes.

---

## Models Considered

* **Base Model**: Instruction-tuned decoder-only LLM (7–8B range)
* **Adaptation**: LoRA / QLoRA fine-tuning
* **Alternatives**:

  * mT5 / FLAN-T5 for conservative multilingual rewriting (Subtask A)

Large closed-source models and fully rule-based systems were avoided due to lack of controllability, reproducibility, and fine-grained error analysis.

---

## Training Details

* Fine-tuning method: LoRA / QLoRA
* Epochs: 3–5
* Learning rate: 1e-4 (LoRA)
* Decoding:

  * Temperature ≤ 0.4
  * Top-p = 0.9
* Loss: Standard language modeling loss

Fine-tuning is used **not to teach inclusivity concepts**, but to align the model with:

* minimal-edit behavior
* task-specific tone
* consistency under evaluation

---

## Evaluation

Systems are evaluated using a **hybrid LLM-as-a-Judge framework with human oversight**, based on:

### 1. Gender-Inclusive Fairness Index (GIFI)

Measures effectiveness in removing gender bias without introducing new stereotypes or distortions.

### 2. Semantic Similarity

Ensures preservation of meaning, intent, and factual content.

Human evaluators conduct spot checks and resolve ambiguous cases.

---

## Baselines Implemented

* Dictionary-only gender neutralization
* Zero-shot instruction-tuned LLM
* Fine-tuned LLM without masking
* **Rule + masked fine-tuned LLM (final system)**

---

## Project Structure

```
.
├── data/
│   ├── subtask_a/
│   ├── subtask_b/
├── rules/
│   ├── gender_lexicons/
│   └── language_specific_rules/
├── training/
│   ├── finetune_lora.py
│   └── prompts.py
├── inference/
│   └── generate.py
├── evaluation/
│   └── analysis.ipynb
└── README.md
```

---

## Key Observations

* Over-generation and moralizing language are heavily penalized.
* Conservative rewriting consistently outperforms creative paraphrasing.
* Masked fine-tuning significantly improves semantic fidelity.
* Low-resource languages require stricter constraints.

---

## Reproducibility

* Fixed random seeds
* Deterministic decoding
* Fully open-source training scripts
* No dependency on closed APIs
