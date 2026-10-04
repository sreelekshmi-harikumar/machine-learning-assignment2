# PCCST503 — Assignment 2: Vector Embedding for Capability Composition

**Representation:** *TAPCE — Typed-Atom Partitioned Capability Embedding*  
Typed state variables are grounded into atoms; states, goals, preconditions, constraints and effects are ternary vectors;
data dependencies are port vectors; operational properties live in a monoid block; composition is block-wise, associative and
sound with respect to execution. No planner, no pre-trained model.

| deliverable | file |
|---|---|
| 1. Formal embedding design | `DELIVERABLE_1_FORMAL_EMBEDDING_DESIGN.md` (+ `.pdf`) |
| 2. Implementation | `capemb/` + `DELIVERABLE_2_IMPLEMENTATION.md` |
| 3. Experimental dataset | `datasets/*.json` + `DELIVERABLE_3_EXPERIMENTAL_DATASET.md` |
| 4. Technical report | `DELIVERABLE_4_TECHNICAL_REPORT.md` (+ `.pdf`) |
| Explanation / viva Q&A | `EXPLANATION.md` |
| Results (auto-generated) | `results/RESULTS.md`, `results/results.json`, `results/figures/` |

## Run

```bash
pip install numpy scipy matplotlib pytest
python datasets/build_datasets.py && python datasets/make_dataset_doc.py
python -m pytest tests -q            # 21 passed
python experiments/run_all.py        # reproduces every table and figure
```

## 10-second demo

```python
from capemb import load_problem, CapabilityEmbedder
E = CapabilityEmbedder(load_problem("datasets/P1_ecommerce_checkout.json"))
E.compat("CreateOrder", "MakePayment_API")["label_name"]        # PARTIAL (needs more than CreateOrder gives)
E.compat("MakePayment_API", "SendNotification")["label_name"]   # STRICT
E.compat("CreateOrder", "CancelCart")["label_name"]             # CONFLICT
c = E.compose(["CreateOrder", "ValidatePaymentLimit", "MakePayment_API"]); c.valid
E.similarity("MakePayment_API", "MakePayment_DB", "func")       # 1.0 (same function) ...
E.compat("MakePayment_API", "MakePayment_DB")["label_name"]     # ... but CONFLICT (similar != composable)
```
