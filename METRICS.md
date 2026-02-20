# Metrics (v1.x)

Public performance snapshot for HADRIAN artifacts in this repository.
Methodology and metric definitions: see `METHODS.md`.

---

## Latency

| Stage               | p95 Latency |
|---------------------|-------------|
| Text Classification | ~17ms       |
| Full Pipeline       | TBD         |

Goal: full pipeline under 100ms p95 in controlled conditions.

---

## Classification

| Category      | Status          | Notes                       |
|---------------|-----------------|-----------------------------|
| A (Traffic)   | ✅ Stable       | Rule-based, high recall     |
| B (Medical)   | ⚠️ ~50% recall  | Target: >=70%. In progress. |
| C (Weather)   | ✅ Stable       |                             |
| D (Assembly)  | ✅ Stable       |                             |
| E (Violence)  | ✅ Stable       |                             |

---

## Deduplication

Current approach:
- time window and geo radius per category
- semantic similarity threshold tuned per category
- cross-category deduplication with aspect-compatibility rules

Pending public metrics:
- false-merge rate
- false-split rate

---

## Production status

MVP in progress (as of Feb 2026).

What currently works:
- text classification
- geocoding + deduplication
- ops console (map view, KPIs)
- offline priors integration

Known gaps:
- full end-to-end latency not yet published
- medical category recall below target

---

*Updated: 2026-02-20*
