# SG07 — OSS Growth Surface Memo (final, based on current repo docs)

**Repo scope today (as documented):**
- `README.md` states: *HADRIAN (the production system) is not open-source*, but the architecture, design decisions, and event schema are intended to be public.
- `ARCHITECTURE.md` publishes the stage-by-stage pipeline and category taxonomy (A–E).
- `METRICS.md` publishes early performance/quality targets and current status.
- `schema/event.schema.json` defines a v1 HADRIAN Event JSON Schema.
- `CHANGELOG.md` indicates this is an “open review” publication of architecture/metrics/schema.
- `LICENSE` is MIT for the contents of this repository (docs/schema), even if the operational system remains closed.

This memo describes an **open-source growth surface** that is consistent with that posture: open the *standard + tooling* around the pipeline, while keeping operational anti-abuse/production internals closed.

---

## 1) What should be open (safe surface) vs. kept closed (abuse/ops risk)

### Open (high leverage; aligned with current docs)
1. **The Event standard (spec-first)**
   - Treat `schema/event.schema.json` as a normative, versioned public contract.
   - Publish a human-readable “Event Spec” that explains semantics of:
     - `severity` (content-based) vs `confidence` (epistemic)
     - nullability rules (`when_iso`, `lat/lon`, uncertainty)
     - meaning of `cluster_id` and required audit trace in `explanation`

2. **The pipeline stage boundaries as public interfaces** (from `ARCHITECTURE.md`)
   - Publish *contracts* for each stage:
     - Ingest/Normalize → Extract → Geo-resolve → Deduplicate/Cluster → Score → Alert Generation → Logging/Explainability
   - The goal is interoperability: people can build compatible implementations without learning your internal production details.

3. **Conformance tests for the standard**
   - A test suite that asserts:
     - schema compliance
     - invariants implied by docs (e.g., separation of severity/confidence, uncertainty handling)
     - regression/golden vectors for tricky edge cases (ambiguous location strings, time ambiguity)

4. **Developer tooling around the standard**
   - CLI + SDKs for validating and producing Events:
     - `hadrian validate event.json` (schema + invariants)
     - `hadrian explain event.json` (pretty-print `explanation[]`)
     - TypeScript/Python packages that ship the schema + validator

5. **Synthetic fixtures and benchmark harness** (consistent with `METRICS.md` being “honest numbers”)
   - Publish synthetic report fixtures aligned to categories A–E.
   - Provide a reproducible harness for latency and quality measurements that can update `METRICS.md` without exposing real incident data.

### Keep closed (explicitly not part of the OSS surface)
- **Anti-abuse and gaming-resistant scoring logic** (anything that, if copied, makes adversarial reporting easier).
- **Reporter reputation / trust weighting**, fraud detection, brigading defenses.
- Any production keys, runbooks, data partnerships, or telemetry.
- Any identity linkage, deanonymization, or sensitive data handling beyond what is already public.

---

## 2) Contributor hooks (how serious builders can contribute *now*)

### Hook A — Spec hardening (best early funnel)
The repo already has a schema; what’s missing is “no ambiguity.” Contributions here are high-signal and safe.
- Clarify field semantics and edge cases (especially `when_iso`, `where_text`, uncertainty radius, `cluster_id`).
- Standardize `explanation[].step` values to mirror the pipeline in `ARCHITECTURE.md`.
- Tighten schema constraints where safe (formats, bounds, required fields) without making real-world ingestion impossible.

### Hook B — Conformance suite + examples
Give contributors a concrete target:
- Add a `/examples` directory with valid/invalid Event JSON.
- Add CI tests: “examples must validate against the schema.”
- Add golden tests that reflect your explicit design principles (traceability, uncertainty modeling).

### Hook C — Interop tooling (SDKs/CLI)
Serious builders will show up if they can integrate quickly.
- Ship a minimal TS and Python validator library that embeds the schema.
- Provide a CLI that is deterministic and offline.

### Hook D — Metrics and benchmark contributions
`METRICS.md` already sets expectations; make it reproducible.
- Contributors add synthetic fixtures, benchmark scripts, and reporting tables.
- This channels optimization work into a safe harness (no production secrets).

---

## 3) First issues (bounded, high-value, aligned with current repo)

### Spec / schema
1. **Add a human-readable Event spec doc**
   - Expand on `schema/event.schema.json` semantics with examples.
2. **Add Event examples (valid + invalid)**
   - Cover categories A–E, missing times, ambiguous locations, uncertainty modeling.
3. **Add schema validation tests**
   - Minimal Node/Python test runner validating all examples.
4. **Define schema versioning rules**
   - Add a short policy: what increments `version` and how compatibility is handled.

### Docs hygiene
5. **Resolve the `DESIGN.md` reference**
   - `ARCHITECTURE.md` points to `DESIGN.md`, but it’s not present in the repo.
   - Either add `DESIGN.md` (even a brief “design choices” summary) or remove/replace the link.

### Metrics
6. **Make `METRICS.md` reproducible**
   - Add a “How to measure” section (even if the implementation comes later): dataset (synthetic), harness, and reporting format.

### Community ops (minimal but builder-friendly)
7. **Add CONTRIBUTING + SECURITY**
   - Keep it short; focus on: what you accept, safety constraints, how to run validation/tests.

---

## 4) Docs to attract serious builders (without expanding beyond the current posture)

With the current repo, the “credible builder set” is:
- **README**: add a clear “What’s open here” section:
  - Architecture + schema are open; operational system is not.
  - Contributions are focused on the **standard, tooling, and conformance**.
- **ARCHITECTURE**: keep as the pipeline truth; ensure all references exist.
- **METRICS**: keep honest; add reproducibility path.
- **CHANGELOG**: track spec/schema releases.
- **LICENSE (MIT)**: clarify it applies to this repository’s published assets.

---

## 5) Practical north-star: “HADRIAN-compatible” ecosystem

Define success as:
- multiple independent implementations can emit a compliant HADRIAN Event
- Events carry a clear audit trail (`explanation[]`) matching the pipeline
- contributors can improve clarity, tests, and integration tooling without access to production internals

If you make the **spec + conformance + tooling** excellent, you’ll attract serious builders while keeping anti-abuse and ops details appropriately closed.
