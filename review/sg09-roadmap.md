# SG09 — 90‑day roadmap (3 phases, hard deliverables, solo pace)

Repo: `hadrian-open-review` (public docs + schema)

**What exists today (anchor points):**
- `README.md` (positioning + scope statement)
- `ARCHITECTURE.md` (pipeline overview; currently references `DESIGN.md` which is missing)
- `schema/event.schema.json` (Event Schema v1; description uses “validated”)
- `METRICS.md` (partial benchmarks; full pipeline latency = TBD)
- `CHANGELOG.md` (0.1.0 initial publication)
- Review notes: `review/sg01-positioning.md`, `review/sg02-readme.md`, `review/sg06-trust-legal.md`

**Operating assumption (realistic solo build pace):** 8–12 focused hours/week on public-repo work.

**Roadmap goal:** In 90 days, a skeptical reader can (a) understand HADRIAN’s claims and limits, (b) validate example events against the schema, and (c) see a small, reproducible demo surface that proves the *contract* without implying a production emergency system.

---

## Phase 1 (Days 1–30): Credibility + repo consistency
**Theme:** make the repo internally consistent and safe to interpret.

### Hard deliverables (must ship)
1) **README trust/claims pass (align with MIT license + safety posture)**
- Update `README.md` to:
  - clarify “production service not open-source” vs “this repo content is MIT” (per SG06)
  - replace/soften absolutist phrasing (“validated”, “anywhere”, “no government filter”) with bounded language
  - add a clear **Not an emergency service / do not rely** disclaimer
  - add a “Start here” path: `ARCHITECTURE.md` → `schema/event.schema.json` → `METRICS.md` → `CHANGELOG.md`
- **DoD:** README reads as self-consistent with `LICENSE` and does not over-promise life-safety reliability.

2) **Architecture doc hygiene**
- Resolve the broken reference in `ARCHITECTURE.md` to missing `DESIGN.md` by choosing one:
  - **Option A:** add `DESIGN.md` (recommended)
  - **Option B:** remove the reference and fold the key design choices into `ARCHITECTURE.md`
- **DoD:** no dead links; “Key Design Choices” are present in-repo.

3) **Safety + abuse baseline docs (thin but explicit)**
- Add `SAFETY.md` (or `TRUST.md`) covering:
  - what “validation” means in this repo context (e.g., dedup + confidence, not ground-truth confirmation)
  - non-goals: not dispatch, not triage, not official alerts
  - reporting guidelines (no names/PII, no accusations)
- **DoD:** one place that a journalist / regulator / skeptical engineer can cite for limitations.

4) **Changelog discipline**
- Update `CHANGELOG.md` with a new entry (e.g., `0.1.1`) capturing these doc/claim changes.
- **DoD:** dated entry with explicit bullet list of changes.

---

## Phase 2 (Days 31–60): Contract usability + measurable methodology
**Theme:** make the schema *usable* and metrics *interpretable*.

### Hard deliverables (must ship)
1) **Schema documentation + examples (no breaking changes required)**
- Keep `schema/event.schema.json` as the canonical v1 file.
- Add `schema/README.md` explaining fields, especially:
  - `severity` vs `confidence`
  - optional geo/time fields and uncertainty
  - privacy implications of `raw_text`, `where_text`, `explanation`
- Add `examples/` with **10–20 synthetic event JSONs** spanning categories A–E.
- **DoD:** a reader can open an example and understand every field via `schema/README.md`.

2) **Validation scripts (one-command check)**
- Add a minimal validator:
  - Node (AJV) or Python (`jsonschema`)—pick one
  - validates all files in `examples/`
- **DoD:** a single command validates examples against the schema (document the command in README).

3) **Metrics methodology stub + one honest end-to-end statement**
- Add `METHODS.md` describing:
  - what metrics currently mean in `METRICS.md`
  - what data they were measured on (even if “internal dev set; details withheld”)
  - what is still unknown and why (e.g., full pipeline p95 TBD due to external deps)
- Update `METRICS.md` to include either:
  - a first end-to-end measurement (even if “prototype harness”), **or**
  - a clearly-scoped plan + date for when it will be measured
- **DoD:** `METRICS.md` no longer reads like marketing; it has definitions + caveats.

4) **Release a documentation milestone**
- Tag a doc/spec release (e.g., `0.2.0`) capturing Phase 2 artifacts.
- **DoD:** `CHANGELOG.md` has the release notes; schema/examples/scripts/methods are included.

---

## Phase 3 (Days 61–90): Demo surface + feedback loop (without production claims)
**Theme:** prove the public contract “in motion” via synthetic data and a tiny viewer.

### Hard deliverables (must ship)
1) **Synthetic demo feed**
- Add `demo/feed.ndjson` (or `demo/feed.json`) with **100–500 synthetic events** matching the schema.
- Add `demo/README.md` describing:
  - how it was generated
  - what it represents (demonstration only)
  - what it does *not* represent (real-world coverage/accuracy)
- **DoD:** demo feed validates against the schema using the Phase 2 validator.

2) **Read-only viewer or walkthrough**
- Provide one:
  - `demo/viewer/index.html` (static list + optional map), **or**
  - `demo/WALKTHROUGH.md` with screenshots and a narrative showing how to interpret severity/confidence/uncertainty.
- **DoD:** someone can understand the event contract without running private code.

3) **Governance / handling note (lightweight, non-adversarial)**
- Add `GOVERNANCE.md` stating:
  - moderation stance for doxxing/incitement/defamation
  - how lawful requests are handled (avoid “no government filter” posture)
  - transparency commitments (what is logged/published; what is withheld)
- **DoD:** a clear policy surface exists that matches the repo’s trust narrative.

4) **Close the loop: roadmap-to-reality update**
- Update `README.md` with a short “Status” section that links to:
  - latest schema
  - methods + metrics
  - demo feed + viewer/walkthrough
- **DoD:** README becomes the hub; no scavenger hunt.

---

## Suggested weekly cadence (solo-friendly)
- **Week 1–2:** README + architecture hygiene + SAFETY.md
- **Week 3–4:** changelog + polish
- **Week 5–6:** schema docs + examples
- **Week 7–8:** validator + methods + metrics update + tag `0.2.0`
- **Week 9–10:** demo feed + validation
- **Week 11–12:** viewer/walkthrough + governance + README hub update

---

## Non-goals (explicitly out of scope for these 90 days)
- Running a public ingestion endpoint (high abuse risk).
- Publishing operational playbooks or private implementation details.
- Making global coverage or emergency-reliability claims.
