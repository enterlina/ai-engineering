# FilmScope RAG Assistant — Hybrid Retrieval, Reranking & Grounded Generation

**AI Engineering case study · Elena Dalboni da Rocha**

[← AI Engineering portfolio](../../README.md) · [GitHub profile](https://github.com/enterlina) · [Product & Design portfolio](https://buildnxt.app)

## The challenge

Build a Russian-language support assistant that answers questions from a fictional streaming-service knowledge base. The system needed to find the right evidence in noisy documents, handle exact service identifiers, generate answers grounded in retrieved passages, and abstain when reliable support was unavailable.

The central engineering challenge was **retrieval quality**: getting the right source into a small context window without exposing the generation model to irrelevant or duplicate material.

## Solution and engineering work

I implemented and evaluated a retrieval-augmented generation (RAG) pipeline:

1. **Corpus preparation:** repaired text encoding issues, removed archive/service content and near-duplicates, and applied paragraph-aware chunking.
2. **Dense semantic search:** used `intfloat/multilingual-e5-base` embeddings to retrieve related Russian-language content.
3. **Hybrid routing:** added BM25 and Reciprocal Rank Fusion (RRF) to strengthen retrieval for exact identifiers and terms.
4. **Two-stage ranking:** retrieved the top 20 candidates, then applied `BAAI/bge-reranker-v2-m3` cross-encoder reranking to select the top 5 passages.
5. **Grounded generation:** evaluated answer generation with `openai/gpt-oss-20b`, including abstention when the retrieved context was insufficient.
6. **Ablation experiments:** tested query expansion, HyDE, and their combination; retained the simpler reranked pipeline because these variants did not outperform it.
7. **Reproducible evaluation:** saved versioned checkpoints, retrieval metrics, and experiment settings to avoid comparing incompatible runs.

## Retrieval evaluation

| Configuration | Recall@5 | MRR |
| --- | ---: | ---: |
| Clean dense baseline | 0.7589 | 0.6553 |
| Routed BM25 + dense | 0.7679 | 0.6529 |
| **Hybrid + cross-encoder reranking** | **0.9464** | **0.8296** |

**Measured improvement from the clean dense baseline:**

- **Recall@5:** +0.1875 absolute (18.75 percentage points; approximately 24.7% relative improvement).
- **MRR:** +0.1743 absolute (approximately 26.6% relative improvement).
- **Retrieval target:** Recall@5 ≥0.85 — exceeded in the selected evaluation.

These are *retrieval* metrics, not end-to-end answer accuracy. The final cleaned-corpus generation evaluation is a separate experiment and must not be conflated with earlier generation scores.

## Error analysis and design decisions

- **Noise reduction:** removed 21 archive pages, 13 service documents, and seven content-level near-duplicates without removing gold-relevant documents in the evaluated set.
- **Exact matches matter:** semantic similarity alone can miss product names or identifiers, motivating routed sparse + dense retrieval.
- **Reranking delivered the largest retrieval gain:** the hybrid candidate set became substantially stronger after cross-encoder ranking.
- **Simpler was better:** query expansion and HyDE added complexity without improving the selected evaluation metric.
- **Abstention is essential:** the assistant should not invent an answer when official context is insufficient.

## Architecture

```text
Russian-language query
       ↓
Corpus filtering and paragraph-aware chunks
       ↓
Multilingual E5 dense retrieval
       + routed BM25 / RRF for exact terms
       ↓
Top-20 candidates
       ↓
BGE cross-encoder reranker
       ↓
Top-5 grounded contexts
       ↓
GPT-OSS 20B response or abstention
```

## Explore the implementation

| Resource | What it contains |
| --- | --- |
| [English notebook — full project](filmoskop_rag_project_en.ipynb) | Implementation, experiment outputs, evaluation and discussion in English |
| [Russian notebook — full project](filmoskop_rag_project.ipynb) | Original implementation and documentation |
| [Engineering notes](README-project.md) | Technical setup, model selection and reproducibility notes |
| [Retrieval checkpoint](filmoskop_checkpoints/retrieval_v3_results.json) | Versioned machine-readable final retrieval results |
| [Dependencies](requirements-project.txt) | Python package requirements |
| [All project files](.) | Notebooks, scripts and checkpoints |

## Reproducibility

```bash
python -m pip install -r requirements-project.txt
jupyter notebook filmoskop_rag_project_en.ipynb
```

The notebooks request an API key via a hidden prompt; credentials should never be committed. Expensive steps use versioned JSON/NPZ checkpoints and a fixed random seed (42). Cross-encoder reranking was configured for CPU execution on macOS to manage memory pressure.

## Limitations and next steps

- This is an **educational/research prototype**, not a production support service.
- End-to-end answer-quality metrics for the final v3 corpus must be recomputed before claiming final generation accuracy.
- Abstention thresholds calibrated on available answerable examples should be validated on a separate held-out split.
- Future evaluation should include answer groundedness, unsupported-claim rate, abstention precision/recall, latency, and cost per correctly answered question.

---

**Author:** [Elena Dalboni da Rocha (enterlina)](https://github.com/enterlina)

[More AI Engineering projects](../../README.md) · [Portfolio](https://buildnxt.app)
