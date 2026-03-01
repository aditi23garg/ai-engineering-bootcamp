# LLM Selection Decision

| Decision Factor | My Choice | Reason |
|-----------------|-----------|--------|
| Which LLM followed prompt structure most faithfully? | ChatGPT | Cleanest formatting, consistent headers, easy to scan |
| Which LLM was most technically accurate? | Claude | Least hallucination, most specific tradeoffs, named real Google infrastructure (Maglev, Monarch, Stubby) |
| Which LLM's output was most readable? | ChatGPT | Best organized, professional tone, clear sections |
| Which LLM handled "honesty check" best? | Tie (All) | All three correctly identified minimal LLM usage in core ranking |

**Selected LLM for final tool:** Claude

**Why:** While ChatGPT had better formatting, Claude's technical accuracy and depth of systems thinking are more critical for a teardown tool. Claude consistently identified non-obvious tradeoffs (training-serving skew, Goodhart's Law, embedding alignment, tail latency under fan-out) that demonstrate real engineering insight. I can reformat output, but I cannot fix shallow technical analysis. Claude's ability to quantify scale ("1% canary = tens of millions of users") and cite specific infrastructure (MMoE, Maglev, Monarch) makes it the best engine for this tool.
