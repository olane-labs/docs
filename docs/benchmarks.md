# LongMemEval Benchmark

Olane achieves the highest GPT-4o score on LongMemEval with **92.2% overall accuracy** — 461 of 500 questions answered correctly.

## Overall Comparison (GPT-4o)

All systems evaluated using GPT-4o as both the generation and judge model, per the [LongMemEval](https://github.com/xiaowu0162/LongMemEval) benchmark protocol.

| System | Score |
|---|---|
| **Olane** | **92.2%** |
| Mastra OM | 84.23% |
| Oracle (answer sessions only) | 82.4% |
| Supermemory | 81.6% |
| Zep | 71.2% |
| Full context (no memory) | 60.2% |

## Per-Category Breakdown (GPT-4o)

| Category | Olane | Mastra OM | Supermemory | Zep | Best |
|---|---|---|---|---|---|
| Knowledge Update | 87.2% (68/78) | 85.9% | **88.46%** | 83.3% | Supermemory |
| Multi-Session | **88.0%** (117/133) | 79.7% | 71.43% | 57.9% | Olane |
| Single-Session Assistant | **100.0%** (56/56) | 82.1% | 96.43% | 80.4% | Olane |
| Single-Session Preference | **96.7%** (29/30) | 73.3% | 70.00% | 56.7% | Olane |
| Single-Session User | **100.0%** (70/70) | 98.6% | 97.14% | 92.9% | Olane |
| Temporal Reasoning | **91.0%** (121/133) | 85.7% | 76.69% | 62.4% | Olane |
| **Overall** | **92.2%** (461/500) | 84.23% | 81.6% | 71.2% | **Olane** |

> **Note:** Olane leads in 5 of 6 categories. The only category where another system scores higher is Knowledge Update, where Supermemory edges ahead by 1.3 points (88.46% vs 87.2%).

## Key Results

- **100%** — Perfect scores on both Single-Session Assistant and Single-Session User. No other system achieves this.
- **+8 pts** — Olane outperforms the previous GPT-4o SOTA (Mastra OM at 84.23%) by nearly 8 percentage points.
- **+10 pts** — Beats the Oracle baseline (82.4%) by 10 points, despite the Oracle only processing the 1–3 sessions containing answers.

## Full Leaderboard (GPT-4o)

All results using GPT-4o for an apples-to-apples comparison.

| Rank | System | Overall |
|---|---|---|
| 1 | **Olane** | **92.2%** |
| 2 | EmergenceMem Internal | 86.0% |
| 3 | Mastra OM | 84.23% |
| 4 | EmergenceMem Simple | 82.4% |
| 5 | Oracle (answer sessions only) | 82.4% |
| 6 | Supermemory | 81.6% |
| 7 | Mastra RAG | 80.05% |
| 8 | Zep | 71.2% |
| 9 | Full context (no memory) | 60.2% |

> **Note:** Olane leads the GPT-4o leaderboard by over 6 points. Other systems require newer models to approach this score — Mastra OM needs GPT-5-mini (94.87%) and Hindsight needs Gemini 3 Pro (91.40%) to reach comparable results.

## Methodology

Results are from the [LongMemEval](https://github.com/xiaowu0162/LongMemEval) benchmark — 500 questions across 6 categories testing long-term memory capabilities including preference tracking, knowledge updates, temporal reasoning, and multi-session synthesis.

- **Model**: GPT-4o for both generation and evaluation
- **Evaluation**: LLM-as-judge with question-specific prompts, binary correctness scoring
- **Scope**: All 500 benchmark instances processed, 492 answered (8 abstentions)
- **Categories**: Unweighted average across 6 categories

Competitor scores sourced from published results by [Mastra](https://mastra.ai/research/observational-memory), [Supermemory](https://supermemory.com), and [Zep](https://zep.ai).
