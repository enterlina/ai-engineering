# LLM Product Card Generation — Structured Output, Benchmarking & Cost Optimization

**AI Engineering case study · Yandex Practicum, Sprint 1 · Elena Dalboni da Rocha**

[← AI Engineering portfolio](../../README.md) · [GitHub profile](https://github.com/enterlina) · [Product & Design portfolio](https://buildnxt.app)

## The challenge

Build a batch inference pipeline that turns product specifications and customer reviews into structured, machine-readable e-commerce product cards. Each result must include a description, pros, cons, tags, and the correct product ID.

The engineering targets were:

- **Output quality:** at least 80% of cards pass schema/content validation.
- **Performance:** process 300 products within 8 minutes.
- **Efficiency:** estimated compute cost at most 25 RUB per 1,000 cards.

The challenge was not simply to prompt an LLM, but to make its outputs **consistent, measurable, and economical at batch scale**.

## Solution and engineering work

I developed an inference and evaluation workflow around **Qwen2.5-1.5B-Instruct**:

1. **Structured generation:** prompted the model to produce JSON containing `product_id`, `description`, `pros`, `cons`, and `tags`.
2. **Automated validation:** checked required fields, matching IDs, description length and sentence count, and allowed item counts for pros, cons, and tags.
3. **Parameter experiments:** evaluated temperature, top-p, top-k, repetition penalty, and maximum output tokens.
4. **Long-context handling:** selected reviews across the available list to stay within an approximately 1,500-token input budget.
5. **Batch and concurrency testing:** measured throughput, wall time, request latency, and validity under different concurrency settings.
6. **API comparison and resiliency:** compared against a sampled OpenRouter model and implemented retries with exponential backoff for rate limits and transient server failures.
7. **Cost modeling:** calculated VM-equivalent processing cost from elapsed time and an assumed hourly rate.

## Reported benchmark results

The source project's final `outputs/report.json` contains the following results for **300 products**:

| Metric | Reported result | Acceptance criterion |
| --- | ---: | ---: |
| Structurally valid cards | **265 / 300** | ≥240 |
| Validity rate | **88.33%** | ≥80% |
| Invalid rate | 11.67% | — |
| Batch execution time | **330.53 s (5.51 min)** | ≤480 s |
| Throughput | **0.908 cards/s** | — |
| Valid cards per minute | **48.1** | — |
| Average model latency | 8.50 s | — |
| Estimated compute cost / 1,000 cards | **19.89 RUB** | ≤25 RUB |
| Runtime margin | **149.47 s (31.1%)** | — |

**Reported configuration:** Qwen/Qwen2.5-1.5B-Instruct; temperature 0.3; top_p 0.9; top_k 50; repetition_penalty 1.2; max_tokens 350; concurrency 8. The final JSON report identifies NVIDIA Tesla T4 with vLLM 0.6.6.

**Measurement note:** These figures are taken from the archived report, not reproduced in this public repository. Historical README sections conflict on whether the final run was actually executed on NVIDIA T4 or locally with Ollama; hardware provenance therefore remains **unverified** pending the original run logs. The 19.89 RUB figure is a **VM-equivalent estimate** at 65 RUB/hour, not an invoice or full infrastructure cost.

## What I learned

- **Validation must be part of the pipeline.** Generating plausible prose is not enough when downstream software depends on strict JSON and field-level constraints.
- **Quality and speed are separate trade-offs.** Concurrency can increase per-request latency and does not automatically improve throughput, especially on local hardware.
- **Compare only like-for-like evaluations.** Small API samples, different token limits, and mixed hardware runs cannot support broad claims about which model is best.
- **Failure analysis guides the next optimization.** The final report records 35 invalid results, with errors including missing or undersized lists, generation failures, and description constraints.
- **Compute cost only matters alongside usable output.** One diagnostic OpenRouter experiment produced 0 valid JSON outputs out of 20 at a 350-token cap; this is a limited test outcome, not a general verdict on hosted LLMs.

## Limitations and next steps

- **Output validity ≠ factual correctness.** A separate human/factual evaluation is needed to assess whether pros, cons, and descriptions are faithful to reviews.
- The archived source README reports 33 invalid cards in one section versus **35** in the final JSON summary. The reported benchmark table uses the final JSON value.
- Older concurrency results describe separate runs and should not be mixed into the final benchmark.
- To make results fully reproducible, preserve environment/version information, execution logs, clean sample inputs, and the validation protocol; rerun on confirmed hardware.
- Future work: factual-grounding checks, model comparisons under matched budgets, confidence intervals across multiple runs, and cost-quality Pareto analysis.

## Project context

This work was completed as an AI Engineering project in the Yandex Practicum track. The original archive includes Python scripts for generation, validation, hyperparameter experiments, concurrency tests, and model/API comparison, along with outputs and summary reports.

The public case study intentionally documents the engineering approach and results **without redistributing the course dataset, assignment materials, or unreviewed source code**. Code and reproducibility artifacts can be added after permissions and secrets have been checked.

---

**Author:** [Elena Dalboni da Rocha (enterlina)](https://github.com/enterlina)

[More AI Engineering projects](../../README.md) · [Portfolio](https://buildnxt.app)
