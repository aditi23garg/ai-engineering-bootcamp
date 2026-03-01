# Final Teardown — YouTube Recommendation Feed
# Generated using V2 System Prompt on Claude

## Layer 1: Data Foundation

**What's happening:** YouTube ingests ~500 hours of video per minute 
alongside continuous behavioral event streams — clicks, watch-time 
(to the millisecond), seeks, skips, shares, likes — from 2B+ monthly 
active users. Raw event logs flow through Pub/Sub and Kafka into 
Dataflow pipelines for real-time feature computation, while batch 
pipelines on Bigtable/Spanner maintain user history profiles going 
back months. Video metadata and transcripts are processed offline 
via Dataflow jobs writing to Colossus/GCS.

**Key technologies:** Apache Kafka, Google Cloud Pub/Sub, Dataflow 
(Apache Beam), Bigtable, Spanner, Colossus, Flume, Dremel/BigQuery 
for analytics

**Engineering challenge:** Training-serving skew is the core threat. 
Features computed offline during training diverge from what is 
available at inference time due to pipeline latency. The tradeoff: 
accepting ~100-200ms stale features from a feature store rather than 
paying the cost of fully synchronous real-time computation.

**Skill required:** Data Engineer with experience in streaming 
pipelines (Beam/Dataflow), feature store design, large-scale log 
ingestion, and training-serving consistency.

**Honesty check:** This layer is massive and deeply critical — 
YouTube's data infra is arguably more complex than its models.

---

## Layer 2: Statistics & Analysis

**What's happening:** YouTube runs thousands of A/B experiments 
simultaneously using Overlapping Experiment Framework to avoid 
experiment interference. Metric design is brutally hard: raw CTR 
is heavily Goodhart-corrupted (clickbait wins), so the team uses 
a composite satisfaction signal blending watch-time, post-watch 
survey scores, and explicit feedback. Survival analysis is used 
to model long-term retention curves.

**Key technologies:** Google's internal Overlapping Experiment 
Framework, CUPED (Controlled-experiment Using Pre-Experiment Data) 
for variance reduction, Doubly Robust / IPS estimators for offline 
policy evaluation.

**Engineering challenge:** Surrogate metric corruption. Optimizing 
watch-time at scale produced the "rabbit hole" effect — users watched 
more but reported lower satisfaction. YouTube now explicitly 
incorporates survey-based satisfaction signals, accepting that this 
metric is noisier because it is harder to Goodhart.

**Skill required:** Experimentation statistician with expertise in 
causal inference, metric design, variance reduction (CUPED), and 
multi-metric tradeoff frameworks.

**Honesty check:** Heavily used. Metric design is arguably the most 
consequential intellectual work at YouTube.

---

## Layer 3: Machine Learning Models

**What's happening:** YouTube's system is a two-stage cascade at 
massive scale. Stage 1: a two-tower candidate generation model learns 
separate user and video embedding towers (~256-dim), trained on 
implicit feedback, retrieving hundreds of candidates from 800M+ 
videos via approximate nearest neighbor search. Stage 2: a deep 
ranking model using Multi-gate Mixture-of-Experts (MMoE) scores 
candidates across competing objectives with task-specific output 
heads sharing a gated expert layer.

**Key technologies:** TensorFlow, ScaNN (open-sourced by Google), 
TFX for pipeline orchestration, TFRanking, MMoE architecture, 
TPU v4 pods for training

**Engineering challenge:** Competing objective tension between 
engagement and satisfaction. MMoE handles this by learning separate 
expert networks with learned gating per task, but calibrating the 
final ranking score still requires explicit objective weighting that 
is a product and policy decision as much as an ML one.

**Skill required:** ML Engineer with expertise in two-tower 
retrieval, multi-task ranking, MMoE, embedding spaces, and 
large-scale TFX production pipelines.

**Honesty check:** This is the beating heart of the product — 
extremely heavily used and actively published about by Google Research.

---

## Layer 4: LLM / Generative AI

**What's happening:** LLM usage in the core recommendation feed is 
currently minimal. Traditional behavioral signals are far more 
predictive than semantic understanding. Where generative AI appears: 
(1) video transcript and chapter generation via ASR feeding into 
content understanding, (2) LLM-assisted enrichment of video metadata 
and topic taxonomy, (3) YouTube conversational search features 
sitting adjacent to the feed.

**Key technologies:** Gemini API for metadata enrichment and 
conversational features, ASR pipelines for transcript extraction, 
embedding alignment between text and behavioral embeddings for 
cold-start.

**Engineering challenge:** Embedding space mismatch. LLM-derived 
semantic embeddings live in a very different space from behavioral 
co-watch embeddings. The tradeoff: use LLM embeddings primarily as 
a cold-start signal for new videos, falling back to behavioral 
signals once sufficient watch data accumulates (typically within 
~48 hours of upload).

**Skill required:** ML Engineer with experience in multimodal 
embedding alignment, RAG, and integrating foundation model outputs 
into production ranking pipelines.

**Honesty check:** Thin layer for the core feed. Behavioral ML 
utterly dominates. LLMs are supplementary and not the main 
ranking signal.

---

## Layer 5: Deployment & Infrastructure

**What's happening:** The two-stage system has very different serving 
profiles: candidate generation ANN lookup must return results in 
<10ms, while the ranking model must fit within a ~100ms total budget. 
Models are continuously trained on 24-48 hour data windows with new 
versions validated offline before incremental rollout. Canary 
deployments to 1% of YouTube's ~30M daily US users still expose 
a new model to ~300K users.

**Key technologies:** Google Borg, TensorFlow Serving, ScaNN for 
ANN serving, dedicated TPU inference pods, internal feature serving 
infrastructure, Monarch for real-time monitoring.

**Engineering challenge:** Latency budget enforcement under tail 
conditions. P99 latency spikes during viral events when sudden 
surges cause cache misses and cold feature lookups simultaneously. 
The tradeoff: hedged requests (parallel requests to multiple serving 
replicas) reduce P99 at the cost of ~2x compute overhead.

**Skill required:** Production ML Engineer / MLOps with experience 
in low-latency model serving, canary deployment frameworks, 
distributed systems, and ML monitoring.

**Honesty check:** Heavily used and deeply engineered — YouTube's 
serving infra is a significant competitive moat.

---

## Layer 6: System Design & Scale

**What's happening:** At 2B+ MAU with ~1B hours of video watched 
daily, the fan-out problem is severe: a single homepage load triggers 
parallel calls to candidate generators, feature stores, ranking 
servers, and diversity filters before assembly. The system must 
handle highly skewed request distributions — viral events can spike 
a single video's request rate by 1000x in minutes.

**Key technologies:** Google's global load balancing (Maglev/
Andromeda), Colossus distributed file system, Spanner for globally 
consistent user state, CDN infrastructure, gRPC/Stubby, consistent 
hashing for cache partitioning.

**Engineering challenge:** Cold-start at viral scale. When a video 
goes from 0 to 10M views in 2 hours, behavioral signal pipelines 
have not propagated embeddings and feature store entries are stale. 
The tradeoff: YouTube likely maintains a "trending injection" layer 
that bypasses standard ranking and force-inserts viral content using 
rule-based signals, accepting reduced personalization for correctness.

**Skill required:** Distributed Systems Engineer with experience in 
global-scale serving architecture, tail latency mitigation, cache 
invalidation at scale, and fan-out pattern design.

**Honesty check:** This layer is extremely heavily used — YouTube's 
scale is genuinely at the frontier of what has been engineered.

---

## Overall Analysis

**Most critical layer:** Layer 3 (Machine Learning Models), 
specifically the MMoE ranking model. The multi-objective tension — 
engagement vs. satisfaction vs. advertiser goals vs. creator fairness 
— is the defining product and engineering challenge. Getting this 
layer wrong by even a few percent in objective weighting has 
measurable effects on billions of watch-hours and public trust.

**Complexity rating: Bleeding Edge**
YouTube combines frontier-scale data infrastructure (500 hours/min 
ingestion), a two-stage ML system requiring sub-100ms end-to-end 
latency across 800M+ video corpus, live multi-objective optimization 
with real societal consequences, and global serving at 2B+ MAU. Each 
layer individually would be Advanced; running all six simultaneously 
at this scale is genuinely at the frontier of what exists.

**If rebuilding from scratch, first priority:**
Define your satisfaction metric before writing a single line of ML 
code. Every architectural decision downstream is determined by what 
you are actually trying to optimize. YouTube's most painful technical 
debt (the watch-time rabbit hole problem) stemmed from optimizing a 
metric that was easy to measure but wrong to maximize. The two-tower 
models and MMoE architectures are recoverable engineering mistakes; 
optimizing the wrong objective for years at scale is a product and 
trust problem that takes a decade to unwind.
