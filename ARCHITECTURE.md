# Architecture

## Pipeline Overview

```
[Raw Report]
     │
     ▼
1. Ingest & Normalize
     │  text cleaning, language normalization, metadata extraction
     ▼
2. Extraction
     │  category, location signals, time signals, indicators
     │  — uncertainty modeled explicitly at every step
     ▼
3. Geo Resolution
     │  ambiguous location strings → coordinates + uncertainty radius
     │  ("near the bridge" is valid input — we model the uncertainty, not ignore it)
     ▼
4. Deduplication / Clustering
     │  location + time + semantic similarity per category
     │  — new event or part of existing cluster?
     ▼
5. Scoring
     │  Severity (what happened) and Confidence (how sure) — always separate
     ▼
6. Alert Generation
     │  structured event + brief situation description
     │  — no panic language, no speculation
     ▼
7. Logging / Explainability
     └  full audit trail — every decision traceable from raw report to alert
```

## Categories

| ID | Type                     |
|----|--------------------------|
| A  | Traffic / Accident       |
| B  | Medical                  |
| C  | Weather / Infrastructure |
| D  | Assembly / Demo          |
| E  | Violence / Vandalism     |

## Key Design Choices

See [DESIGN.md](./DESIGN.md) for the reasoning behind each decision.

## Performance (v1.x)

See [METRICS.md](./METRICS.md) for current benchmarks.
