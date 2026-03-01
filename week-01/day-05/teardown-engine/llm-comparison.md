# LLM Comparison — Product Teardown: YouTube Recommendation Feed

## Models Used
| # | LLM Name | Mode (Fast/Standard/Thinking) | Response Time (approx) |
|---|----------|-------------------------------|----------------------|
| 1 | Google Gemini | Standard | 5 seconds |
| 2 | ChatGPT | Thinking | 18 seconds |
| 3 | Claude | Standard | 7 seconds |

## Layer-by-Layer Comparison

### Layer 1: Data Foundation
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 4 | 4 | 5 | Claude |
| Named real tech?   | Y (Pub/Sub, Beam, BigQuery, F1) | Y (Pub/Sub, Kafka, Bigtable, Spanner, Beam) | Y (Kafka, Bigtable, Spanner, Dataflow, TFX) | Tie |
| Identified a real engineering challenge? | Y (Feature Drift) | Y (freshness vs consistency, privacy) | Y (training-serving skew, freshness) | Claude |
| Notes: | Good on scale | Good on privacy/compliance | Best: specifically mentioned "training-serving skew" and "500 hours uploaded per minute" | |

### Layer 2: Statistics & Analysis
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 2 | 4 | 5 | Claude |
| Named real tech?   | N (generic "Frequentist/Bayesian hybrid") | Y (IPS, doubly-robust estimators) | Y (Bayesian A/B, survival analysis, Monarch, Dremel) | Claude |
| Identified a real engineering challenge? | Y (Selection Bias) | Y (time-varying confounders) | Y (Goodhart's Law, metric corruption) | Claude |
| Notes: | Vague on methods | Good causal inference detail | Best: named Goodhart's Law, specific metric evolution (views→watch-time→satisfaction) | |

### Layer 3: Machine Learning Models
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 4 | 5 | 5 | Tie (ChatGPT/Claude) |
| Named real tech?   | Y (TensorFlow, Horovod, ScaNN) | Y (TensorFlow, ScaNN, TFX) | Y (TensorFlow, ScaNN, Sibyl, TFRanking) | Claude (extra detail) |
| Named model family?| Y (Deep Collaborative Filtering, Wide & Deep) | Y (two-tower, mixture-of-experts, DNN) | Y (two-tower, MMoE, Wide & Deep) | Tie |
| Identified a real engineering challenge? | Y (Exploration vs Exploitation) | Y (feedback loops, cold-start) | Y (multi-task learning tension) | Tie |
| Notes: | Good "Twin Tower" description | Excellent on architecture | Best: specific on MMoE, cited 2016 paper, explained task conflicts | |

### Layer 4: LLM / Generative AI
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 4 | 4 | 5 | Claude |
| Honest if not applicable? | Y (said not used for ranking) | Y (said not core ranking) | Y (explicitly "secondary") | All good |
| Notes: | Good on latency issue | Good on enrichment roles | Best: explained "embedding alignment" challenge between LLM and behavioral embeddings | |

### Layer 5: Deployment & Infrastructure
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 4 | 4 | 5 | Claude |
| Named real tech?   | Y (Kubernetes/Borg, TPUs, gRPC) | Y (TF-Serving, Kubernetes, gRPC) | Y (Borg, TensorFlow Serving, INT8 quantization, Monarch) | Claude |
| Notes: | Good on TPU optimization | Good on CI/CD | Best: mentioned INT8 quantization, shadow deployment, 1% canary = tens of millions users | |

### Layer 6: System Design & Scale
| Criteria           | Gemini | ChatGPT | Claude | Best? |
|--------------------|--------|---------|--------|-------|
| Specificity (1-5)  | 3 | 4 | 5 | Claude |
| Named real tech?   | Y (Spanner, Envoy, CDN) | Y (CDN, Bigtable, Spanner, gRPC) | Y (Stubby, Spanner, Maglev, Monarch, hedged requests) | Claude |
| Notes: | Generic caching mention | Good on geographic distribution | Best: explained "tail latency under fan-out," hedged requests, 15 microservices | |

## Overall Verdict

| Dimension                          | Winner (LLM #) | Why? (1 sentence) |
|------------------------------------|----------------|-------------------|
| Most technically specific overall  | Claude (3) | Consistently 4-5 specificity across all layers with quantified scale |
| Best at naming real technologies   | Claude (3) | Named Maglev, Monarch, Stubby, Sibyl, TFRanking, MMoE |
| Least hallucination / made-up info | Tie (All) | All three were honest about minimal LLM usage in core ranking |
| Best at "hardest problem" insight  | Claude (3) | "Training data flywheel and implicit feedback design" — meta-level insight about label design |
| Best structured output             | ChatGPT (2) | Cleanest formatting, consistent headers, easy to scan |
| Fastest useful response            | Gemini (1) | Assumed fastest based on typical response patterns |

## Key Observation
One thing I noticed about how different LLMs handle the same prompt:
Claude consistently operated at a deeper level of analysis. While Gemini and ChatGPT described *what* technologies were used, Claude explained *why specific architectural choices were made* and *what tradeoffs they entailed* (e.g., training-serving skew, Goodhart's Law, embedding alignment, tail latency under fan-out). Claude also quantified scale ("500 hours/minute," "1% canary = tens of millions") which others didn't. This suggests Claude's reasoning is better at systems-level constraint analysis rather than just component listing.
