# AI Payment Assistant

[View the complete project repository →](https://github.com/enterlina/ai-payment-assistant)

A safety-first, action-taking LLM agent for installment-payment operations. The assistant answers policy questions, retrieves order and payment status, calculates business-day deadlines, and creates eligible refunds through an unreliable payment gateway.

## Engineering challenge

An AI assistant that can move money must remain safe when:

- customer text contains direct or indirect prompt injection;
- an external API returns HTTP 429 or HTTP 5xx;
- a refund succeeds but the response times out;
- the model selects an invalid or unauthorized action;
- trusted operator confirmation is missing.

## What I built

- A bounded manual ReAct loop with OpenAI-compatible function calling and Ollama.
- Three guardrail layers for untrusted input, action-policy validation, and sensitive-output redaction.
- Reliable API wrappers with five-second timeouts, bounded retries, exponential backoff, and calendar fallback.
- Stable idempotency keys that prevent duplicate refunds after ambiguous write failures.
- Structured JSONL traces for every model, tool, guardrail, and external-API event.
- A side-effect-aware evaluation suite covering 24 operational and adversarial scenarios.

## Results

| Metric | Result |
|---|---:|
| Correct scenarios | **17 / 24 (70.8%)** |
| Payment and order-status scenarios | **4 / 4 (100%)** |
| Prompt-injection attacks blocked | **4 / 4 (100%)** |
| Unconfirmed refunds stopped | **2 / 2 (100%)** |
| False-block rate | **0%** |
| Extra or duplicate refunds | **0** |
| Average agent steps | **2.0** |

The remaining failures were primarily incomplete tool chains from the local 7B model. They remained safe failures: no unauthorized or duplicate financial operation was created.

## Stack

Python, Ollama, Qwen2.5-7B, OpenAI-compatible APIs, function calling, ReAct, HTTP APIs, JSONL observability, prompt-injection defense, idempotency, and scenario-based agent evaluation.

## Evidence

- [Full source repository](https://github.com/enterlina/ai-payment-assistant)
- [English case-study notebook](https://github.com/enterlina/ai-payment-assistant/blob/main/portfolio_case_study.ipynb)
- [Evaluation report](https://github.com/enterlina/ai-payment-assistant/blob/main/logs/eval_main.txt)
- [Structured evaluation results](https://github.com/enterlina/ai-payment-assistant/blob/main/logs/eval_main.json)
- [Audit traces](https://github.com/enterlina/ai-payment-assistant/tree/main/logs)
