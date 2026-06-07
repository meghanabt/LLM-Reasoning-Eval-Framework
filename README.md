# LLM Reasoning Eval Framework

A structured evaluation framework for benchmarking reasoning stability and retrieval 
faithfulness across open-source LLMs — designed to surface model-specific failure modes 
under adversarial conditions before production deployment.

---

## Overview

Most LLM evaluations stop at accuracy on clean inputs. This framework stress-tests models 
under **prompt perturbations** and **retrieval noise** — the conditions that actually cause 
failures in production RAG systems.

Evaluated models:
- `mistralai/Mistral-7B-Instruct-v0.2`
- `meta-llama/Meta-Llama-3-8B-Instruct`
- `microsoft/Phi-3-mini-4k-instruct`
- `tiiuae/falcon-7b-instruct`

---

## Eval Dimensions

| Dimension | Description |
|---|---|
| Instruction following | Does the model follow explicit constraints in the prompt? |
| Context retention | Does the model correctly use provided context vs. hallucinate? |
| Multi-step reasoning | Can the model chain reasoning steps without drift? |
| Retrieval faithfulness | Are answers grounded in retrieved documents? (RAGAS) |
| Hallucination rate | How often does the model generate unsupported claims? |
| Prompt perturbation stability | Does output quality degrade under rephrased inputs? |
| Retrieval noise tolerance | Does the model handle irrelevant retrieved chunks gracefully? |
| Answer relevance | Is the final answer relevant to the original question? |

---

## Failure Taxonomy

After running evals across all 8 dimensions, failures were categorized into 4 types:

**Type 1 — Instruction Drift**
Model ignores explicit constraints mid-generation. Most common in Falcon-7B on 
multi-turn prompts.

**Type 2 — Retrieval Hallucination**
Model generates plausible-sounding facts not present in retrieved chunks. Highest 
rate observed in Mistral-7B on noisy retrieval conditions.

**Type 3 — Reasoning Collapse**
Model loses track of intermediate steps in multi-hop reasoning chains. Observed 
across all models on chains longer than 3 hops.

**Type 4 — Perturbation Sensitivity**
Output quality degrades significantly under semantically equivalent but rephrased 
prompts. Phi-3-Mini showed strongest robustness here.

---

## Stack

| Layer | Tool |
|---|---|
| Model loading | HuggingFace Transformers |
| Retrieval eval | RAGAS |
| Observability | LangSmith |
| Vector store | Pinecone |
| Orchestration | LangChain |
| Language | Python 3.11 |

---

## Results Summary

| Model | Instruction Following | Retrieval Faithfulness | Hallucination Rate | Perturbation Stability |
|---|---|---|---|---|
| Mistral-7B | ✅ Strong | ⚠️ Moderate | ❌ High on noise | ✅ Stable |
| Llama-3-8B | ✅ Strong | ✅ Strong | ⚠️ Moderate | ✅ Stable |
| Phi-3-Mini | ⚠️ Moderate | ✅ Strong | ✅ Low | ✅ Most stable |
| Falcon-7B | ❌ Weakest | ⚠️ Moderate | ⚠️ Moderate | ❌ Most sensitive |

---

## Key Findings

- **Llama-3-8B** offered the best overall balance of retrieval faithfulness and 
  reasoning stability — recommended for production RAG pipelines.
- **Phi-3-Mini** punches above its weight on perturbation robustness — strong 
  candidate for latency-sensitive deployments despite smaller size.
- **Falcon-7B** showed consistent instruction drift on multi-turn prompts — not 
  recommended for conversational workflows without fine-tuning.
- Retrieval noise (injecting irrelevant chunks) caused hallucination rates to spike 
  2-3x across all models — highlighting the importance of retrieval quality over 
  model size.

---

## Structure
