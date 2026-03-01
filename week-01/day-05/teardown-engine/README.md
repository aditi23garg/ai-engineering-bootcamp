# AI Product Teardown Engine

## Overview
A reusable prompt-based tool that takes any AI-powered product 
as input and produces a structured 6-layer architectural teardown 
of the AI engineering behind it.

## Product Analyzed
YouTube Recommendation Feed

## LLM Selected for Final Tool
Claude

## The 6 Layers Framework
| Layer | What It Covers |
|-------|---------------|
| Layer 1 | Data Foundation — collection, storage, pipelines |
| Layer 2 | Statistics & Analysis — A/B testing, metrics, causal inference |
| Layer 3 | Machine Learning Models — architectures, training, ranking |
| Layer 4 | LLM / Generative AI — language models, RAG, agents |
| Layer 5 | Deployment & Infrastructure — serving, latency, MLOps |
| Layer 6 | System Design & Scale — caching, load balancing, distributed systems |

## Files in This Repository
| File | Description |
|------|-------------|
| `system-prompt-v1.md` | Initial system prompt before multi-LLM comparison |
| `llm-comparison.md` | Layer-by-layer comparison of Claude, ChatGPT and Gemini |
| `best-parts-map.md` | Best elements extracted from each LLM per layer |
| `system-prompt-v2.md` | Rebuilt prompt after applying learnings from comparison |
| `llm-selection.md` | Decision framework for choosing Claude as final LLM |
| `final-teardown.md` | Best output produced by V2 prompt on Claude |
| `stress-test.md` | Generalization test on Basic Calculator app |
| `reflection.md` | Personal reflections on complexity and LLM differences |

## Key Findings
- YouTube's recommendation system is Bleeding Edge complexity
- Layer 3 (ML Models) is the most critical layer
- Layer 2 (Statistics) was the most surprising in terms of complexity
- Claude produced the most interview-worthy non-obvious insights
- Basic Calculator correctly rated as Simple — tool generalizes properly

## Process followed
1. Built V1 system prompt
2. Ran same prompt across Claude, ChatGPT and Gemini
3. Compared outputs layer by layer
4. Extracted best parts from each LLM
5. Rebuilt V2 prompt based on evidence
6. Selected Claude as final LLM with reasoning
7. Generated final teardown using V2 prompt
8. Stress tested on Basic Calculator to verify generalization

## How to Use This Tool
1. Copy the system prompt from `system-prompt-v2.md`
2. Paste it into Claude (or your preferred LLM)
3. Type any AI product name as the user message
4. Get a structured 6-layer teardown

## Repository

https://github.com/aditi23garg/ai-engineering-bootcamp/tree/main/week-01/day-05/teardown-engine
