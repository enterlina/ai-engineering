# AI Engineering Portfolio

A collection of practical AI Engineering projects covering retrieval-augmented
generation, LLM evaluation, information retrieval, agents, model integration,
and production-oriented experimentation.

## Projects

| Project | Description | Key results |
|---|---|---|
| [FilmScope RAG Assistant](projects/filmoskop-rag/README.md) | Russian-language support RAG system with corpus cleaning, routed BM25+dense retrieval, cross-encoder reranking, grounded generation, and evaluation | Recall@5: **0.9464**, MRR: **0.8296** |
| [LLM Product Card Generation](projects/llm-product-card-generation) | Structured JSON generation from product specifications and reviews with Qwen2.5-1.5B, validation, parameter tuning, concurrency experiments, and cost analysis | **88.33% valid** (265/300); **5.51 min** batch; **19.89 RUB/1k** estimated compute |\n| [AI Payment Assistant](projects/ai-payment-assistant/README.md) | Safety-first action-taking LLM agent with guardrails, idempotent refunds, resilient API calls, structured audit traces, and side-effect-aware evaluation | **70.8% overall**; **4/4 attacks blocked**; **0 extra refunds** |

## Repository structure

```text
ai-engineering/
├── projects/
│   ├── filmoskop-rag/
│   └── llm-product-card-generation/
└── README.md
```

Each project has its own README documenting the problem, approach, findings, and limitations. Available code and reproducibility materials vary by project. Credentials, restricted datasets, model weights, and large runtime checkpoints are excluded from version control.

## Author

[Elena Dalboni da Rocha (enterlina)](https://github.com/enterlina)

