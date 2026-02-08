# Metrics (v1.x)

Current performance — honest numbers, no marketing.

---

## Latency

| Stage              | p95 Latency |
|--------------------|-------------|
| Text Classification | ~17ms       |
| Full Pipeline       | TBD         |

Target: full pipeline under 100ms p95.

---

## Classification

| Category | Status         | Notes                      |
|----------|----------------|----------------------------|
| A (Traffic)   | ✅ Stable    | Rule-based, high recall    |
| B (Medical)   | ⚠️ ~50% recall | Target: ≥70%. In progress. |
| C (Weather)   | ✅ Stable    |                            |
| D (Assembly)  | ✅ Stable    |                            |
| E (Violence)  | ✅ Stable    |                            |

---

## Deduplication

Time window and geo radius per category.
Semantic similarity threshold: tuned per category.
Cross-category deduplication: aspect-compatibility based.

---

## Production Status

in progress for MVP [as of 02/2026].

What works:
- Text classification
- Geocoding + deduplication
- Ops console (map view, KPIs)
- Offline priors integration


---

*Updated: Feb 2026*
