# Methods

How HADRIAN metrics are measured and reported.

## Reporting principles
- Publish measured values, not aspiration phrasing.
- Separate stage-level metrics from end-to-end metrics.
- Report limitations next to every metric block.

## Dataset policy
Current public reporting is based on synthetic or non-sensitive evaluation sets.
No private reporter data is required to reproduce the public examples.

## Core metric definitions

### Latency
- **Stage latency (p50/p95/p99):** runtime per pipeline stage.
- **End-to-end latency (p50/p95/p99):** from raw report accepted to event emitted.
- Report sample size `N`, environment, and warm/cold context.

### Classification quality
Per category (A-E):
- precision
- recall
- F1
- confusion notes for common failure modes

### Georesolution quality
- coverage (% reports that resolve to coordinates)
- median/percentile location error where ground truth exists
- uncertainty calibration notes

### Deduplication quality
- false-merge rate
- false-split rate
- sampling protocol for manual review

### Confidence calibration
- expected calibration error (ECE) where available
- observed correctness by confidence bucket

## Benchmark context template (required for metric updates)
- commit hash / release tag
- runtime environment (CPU/GPU, RAM, OS)
- dependency versions where relevant
- sample size `N`
- data slice description (category mix, language/region assumptions)

## Known methodological limits (current)
- End-to-end production-equivalent latency is still incomplete.
- Category B (Medical) has lower recall than target.
- Public metrics may not represent all deployment contexts.

## Update cadence
- Metrics should be refreshed when schema changes affect interpretation.
- Significant model/rule changes require a method note in `CHANGELOG.md`.
