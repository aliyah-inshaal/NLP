# KCA Reproduction and Extension for Hallucination Mitigation

This repository contains our NLP project for reproducing and extending the EMNLP 2024 paper:

**Knowledge Verification to Nip Hallucination in the Bud**  
Wan et al., 2024

## Project Overview

The original paper proposes **Knowledge Consistent Alignment (KCA)**, a method for reducing hallucinations in large language models by detecting and handling knowledge-inconsistent training examples before fine-tuning.

In this project, we reproduced the main idea of KCA under limited compute resources and extended it with additional experiments.

## What We Implemented

We trained and evaluated four tuning strategies:

| Strategy | Description |
|---|---|
| Standard Tuning | Normal instruction tuning baseline |
| Open-Book Tuning | Adds reference knowledge to the instruction |
| Discard Tuning | Removes knowledge-inconsistent examples |
| Refusal Tuning | Trains the model to refuse when knowledge is missing |

## Main Changes from the Original Paper

| Original Paper | Our Implementation |
|---|---|
| Full fine-tuning | QLoRA / LoRA fine-tuning |
| Multiple models | LLaMA-2-7B only |
| GPT-4 evaluation | Prometheus-2 judge |
| Full WizardLM-70K | Stratified subset |
| GPT-3.5-generated labels | Official precomputed KCA data |
| Original benchmarks only | Added HaluEval |

## Assignment 3 Extensions

We added:

1. **HaluEval** as an additional hallucination-focused dataset.
2. **Expanded LoRA** with attention and MLP projection layers.
3. **Uncertainty-aware prompting** during evaluation.
4. **Refusal rate** as a new metric for measuring model caution.

## Datasets Used

- WizardLM-Evol-Instruct
- KCA preprocessed data from `Wanfq/KCA_data`
- LIMAEval
- VicunaEval
- WizardLMEval
- TruthfulQA
- MS MARCO
- HaluEval

## Model and Training Setup

| Component | Configuration |
|---|---|
| Base model | `NousResearch/Llama-2-7b-hf` |
| Fine-tuning | QLoRA |
| Quantization | 4-bit NF4 |
| LoRA rank | 16 for extended experiments |
| Hardware | Google Colab Pro A100 |
| Frameworks | Hugging Face Transformers, TRL, PEFT, bitsandbytes |
| Judge model | Prometheus-2 |

## Key Results

### MS MARCO ROUGE-L

| Strategy | ROUGE-L |
|---|---:|
| Standard | 0.1097 |
| Open-Book | 0.1358 |
| Discard | 0.1229 |
| Refusal | 0.0836 |

Open-Book Tuning achieved the best ROUGE-L score, improving over Standard Tuning by **23.8%**.

### HaluEval Refusal Rate

| Strategy | Refusal Rate |
|---|---:|
| Standard | 16.0% |
| Open-Book | 5.0% |
| Discard | 25.0% |

Discard Tuning produced the highest refusal rate on HaluEval, suggesting that removing knowledge-inconsistent examples can make the model more cautious on factual questions.

## Important Notes

The hallucination-rate results from Prometheus-2 were near zero for most models. This should not be interpreted as perfect model performance. It mainly reflects the limitation of using Prometheus-2 as a judge with a lenient hallucination threshold.

The most reliable results in this project are the **ROUGE scores** and the **refusal-rate analysis**.
