# GPE

GPE is a research benchmark and evaluation framework for studying how
GEO-style evidence poisoning affects systems that retrieve and judge Web
evidence. This experimental release may contain bugs.

## Setup

```bash
conda env create -f environment.yml
conda activate gpe
python -m pip install -e .
cp .env.example .env
```

Set `LLM_API_KEY`, `LLM_MODEL`, and `LLM_BASE_URL` in `.env` for experiments
that call an LLM. The bundled benchmark and graph data are loaded automatically.

## Data

- `gpe/data/gpe.jsonl`: claims, benign evidence, related distractors, and fixed
  poisoned evidence.
- `gpe/data/graph/`: auxiliary claim, entity, event, and source records.
- `doc/benchmark-examples.md`: representative benchmark records used by the
  paper.

## Basic use

```python
from gpe import ClaimLoader, DatasetEvidenceLoader, EvidenceRetriever

claims = ClaimLoader()
claim = claims.get_claim("benchmark-000002")

evidence = DatasetEvidenceLoader()
clean = evidence.get_evidence_list(
    claim["claim_id"],
    top_k=3,
    poison_ratio=0,
)

retriever = EvidenceRetriever(
    evidence_types={"benign", "related_distractor"},
)
hits = retriever.search(
    claim["original_claim"],
    top_k=3,
    claim_id=claim["claim_id"],
    mode="bm25",
)
```

Run the bundled graph and retrieval example:

```bash
python exp/run_graph_and_retrieval.py \
  --entity BBC \
  --claim-id benchmark-000001 \
  --query "steel production emissions" \
  --top-k 3
```

Run one-example evaluation checks:

```bash
python exp/run_claim_retrieval_evaluation.py \
  --method direct_evidence \
  --smoke-test \
  --threads 1

python exp/run_global_retrieval_evaluation.py \
  --method direct_evidence \
  --smoke-test \
  --threads 1
```

## License

This project is licensed under the [MIT License](LICENSE).
