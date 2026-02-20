# sg05 — Credibility-first metrics framework

Scope: finalize a metrics approach using **only what this repo currently documents** (pipeline stages, categories A–E, event schema fields including `confidence` and `location_uncertainty_m`, and the current `METRICS.md` numbers/targets).

This memo defines:
- **What to report now** (credible today)
- **What to avoid** (until defensible)
- **Milestone thresholds for v0.2 / v0.3** (explicit, testable)

Repo anchors:
- Pipeline stages and audit trail intent: `ARCHITECTURE.md`
- Categories A–E: `ARCHITECTURE.md` / `METRICS.md`
- Event schema includes `severity` (1–5), `confidence` (0–1), geo fields, and `explanation[]`: `schema/event.schema.json`
- Current metrics state: `METRICS.md` (Text classification ~17ms p95; full pipeline TBD; target: full pipeline <100ms p95; Medical/B recall ~50% with target ≥70%; others marked “stable”; dedup described but not measured)


## 1) Credibility-first rules (non-negotiables)

1. **Every number ships with its measurement context**
   - commit/version, environment (hardware + runtime), N (sample size), and “warm vs cold” where relevant.
2. **Report error tradeoffs, not vibes**
   - Avoid “✅ stable”. Use precision/recall (or explicit error rates) per category.
3. **Keep semantics aligned to the architecture**
   - Pipeline claims should map to pipeline stages and schema fields (e.g., if `confidence` exists, calibration must be measured).
4. **Prefer externally-auditable artifacts**
   - A fixed evaluation set definition + harness command beats aspirational prose.


## 2) What to report now (credible today)

### A) Latency (publish both micro and end-to-end)
Current doc has:
- Text classification p95 ≈ **17ms**
- Full pipeline: **TBD**
- Target: full pipeline p95 **< 100ms**

Report now (even if numbers are not yet “good”):
- **Per-stage latency**: p50/p95/p99 for at least
  - classification
  - geo resolution
  - dedup/clustering
  - scoring + alert generation
- **End-to-end latency**: p50/p95/p99 from “raw report received” → “event emitted”

Required footnote fields:
- machine + runtime, concurrency, input length distribution, warm/cold, N.

### B) Classification quality (A–E)
Current doc includes category status + one recall estimate:
- B/Medical recall ≈ **50%** (target ≥ **70%**)
- A/C/D/E marked “stable” but without measured error

Report now:
- Per-category **precision, recall, F1**, plus support (N per class)
- Confusion highlights (top confusions) so “stable” becomes falsifiable

Why this is credibility-critical: recall-only can be gamed by over-triggering; warning systems need the **precision/recall tradeoff** visible.

### C) Geo resolution + uncertainty (ties to schema)
Because the schema includes `lat/lon` and `location_uncertainty_m`, publish:
- **Geo coverage**: % reports producing coordinates vs null
- **Uncertainty discipline**: % outputs with non-null `location_uncertainty_m`
- If you have any truth set: distance error distribution (median/p95)

If you don’t have geo truth yet, publish **manual audit sampling** + error taxonomy (e.g., “ambiguous place name”, “missing where_text”, “too broad”).

### D) Dedup/clustering outcomes (not just the design)
Current doc describes dedup logic (time window, geo radius, semantic threshold, cross-category compatibility) but gives no outcomes.

Report now:
- Cluster size distribution
- A small annotated sample measuring:
  - **false merges** (distinct events merged)
  - **false splits** (same event separated)

### E) Audit trail completeness (ties to architecture + schema)
The architecture claims “full audit trail” and the schema has `explanation[]`.

Report now:
- % events with non-empty `explanation`
- Median number of explanation steps
- Top failure reasons (missing step, empty reason, etc.)

### F) “Production status” stays narrative
Keep the “What works” list (classification, geocoding+dedup, ops console, offline priors) as narrative—but don’t mix it into numeric KPIs.


## 3) What to avoid reporting (until defensible)

Avoid these, because they read like marketing or are easy to misinterpret:
- **“✅ Stable”** without a fixed test set + measured error rates
- Any latency number without environment + warm/cold + N
- **Recall-only** (especially for Medical and Violence categories)
- Benchmarks that exclude expensive steps (e.g., geo) unless clearly labeled “microbench”
- “Tuned per category” claims without showing the resulting precision/recall tradeoff


## 4) Milestone thresholds

These thresholds are framed so release notes remain defensible.
They intentionally emphasize: **(a) measurement maturity first**, then **(b) performance targets**.

### v0.2 — “Measurement-complete baseline”
Goal: turn today’s partial metrics into a reproducible report card.

Gates:
1) **Methodology present**
- Publish the benchmark protocol: environment, N, warm/cold, and a reproducible harness command.

2) **Latency truth (no more TBD)**
- End-to-end full pipeline p95 is **measured and published** (pass/fail is publication itself).
- Text classification p95 remains in the current ballpark (≈17ms today); explicitly track regressions.

3) **Classification reported for all categories**
- Per-category precision/recall/F1 for A–E published.
- Category B (Medical) recall reaches the existing stated target: **≥ 70%** (and publish precision alongside).

4) **Dedup has an outcome metric**
- At least one annotated evaluation (pairwise or cluster-level) with false-merge/false-split rates published.

5) **Audit trail completeness tracked**
- % with `explanation[]` present and non-empty published.

### v0.3 — “MVP-ready target attainment + guardrails”
Goal: meet the repo’s stated speed target and raise medical performance while preventing silent failure modes.

Gates:
1) **Latency target hit (repo-stated)**
- Full pipeline end-to-end latency: p95 **< 100ms** (explicit repo target).

2) **Classification strengthened (especially Medical)**
- Category B (Medical) recall **≥ 80%**, with precision reported (and tracked for regressions).
- Worst-category F1 (across A–E) should be tracked and improved release-over-release (publish it even if imperfect).

3) **Dedup safety guardrail**
- False-merge rate has a defined target and is improving; the key is that it is treated as a release gate (because merges can suppress alerts).
  - If you want a numeric gate: set it only once you have a stable annotation protocol.

4) **Confidence becomes meaningful**
- Define what `confidence` *means* operationally (e.g., “category correct AND geo resolved within stated uncertainty”) and publish calibration-by-bucket.


## 5) Suggested `METRICS.md` rewrite template (drop-in)

1. Methodology (commit, dataset, environment, N, warm/cold)
2. Latency (per stage + end-to-end)
3. Quality
   - Classification A–E (precision/recall/F1)
   - Geo coverage + uncertainty
   - Dedup false merges/splits
   - Confidence calibration (if defined)
4. Audit trail completeness (`explanation[]`)
5. Known limitations + failure modes


## 6) Minimal edits to the current `METRICS.md` tone

Keep the honest tone. The biggest credibility upgrade is to replace:
- “✅ Stable” → measured precision/recall/F1
- “Full pipeline TBD” → measured end-to-end (even if it misses target)
- “~17ms p95” → same number *plus* environment + N + warm/cold
