# SG08 — Operator / Emergency-Ops View (Trust + Adoption)

**Basis (current repo docs only):** `README.md`, `ARCHITECTURE.md`, `METRICS.md`, `schema/event.schema.json`, `CHANGELOG.md`, `LICENSE`.

## Executive frame
From an emergency-ops/operator perspective, HADRIAN must be adoptable as **decision support under time pressure**: predictable, auditable, safe under failure, and governable. The current repo provides a promising **conceptual pipeline + schema** and an “honest metrics” tone, but it does not yet provide the operational trust package an operator organization requires.

---

## 1) What an operator needs to trust (before adopting)

### 1.1 Mission boundaries (what it is / isn’t)
Operators need explicit statements of:
- **Intended use** (situational awareness, triage support, early warning intake).
- **Not intended for** (auto-dispatch, coercive actions, naming suspects, panic messaging).
- **Audience** (operators-only vs public alerts) and any “human gate” expectations.
- A concrete definition of **“validated”** (algorithmic scoring vs corroboration vs human verification).

### 1.2 Actionable semantics
Terms must be operationally crisp:
- **Severity (1–5)** mapped to consequences / SOP thresholds.
- **Confidence (0–1)** must be **calibrated** and interpretable.
- Category definitions (A–E) and edge cases.
- How to combine severity+confidence for triage without “confidence inflation”.

### 1.3 Evidence that holds up in AARs
Operators need decision-grade measurement:
- False positive / false negative tradeoffs **by category**.
- Precision/recall, confusion matrices, **calibration** for confidence.
- **End-to-end** latency and throughput (not only one stage).
- Known failure modes + mitigations.

### 1.4 Operator controls & safety rails
Adoption usually starts with operator-gated workflows:
- Hold/release queue, suppression, rate limits.
- Override/edit with full audit logging.
- Kill switch / safe mode.
- Escalation policy (paging/notification conditions).

### 1.5 Observability + auditability
Needed for post-incident review:
- Complete audit trail from raw report to alert.
- Clear, consistent explanation outputs.
- KPIs that match operator questions (volume, geo hotspots, dedupe rate, drift signals).

### 1.6 Security, privacy, and abuse resistance
Citizen-to-citizen warning systems are abuse magnets:
- Threat model and abuse handling (spam, brigading, hoaxes, targeted harassment).
- Privacy posture (PII minimization/redaction, retention, access controls).
- Integrity controls to prevent silent manipulation.

### 1.7 Reliability & degraded-mode safety
Operators need predictable failure behavior:
- Dependency inventory and what happens when each fails.
- Surge behavior (backpressure, prioritization).
- Disaster recovery expectations.

### 1.8 Interoperability
Operators already live in existing tools:
- Stable feeds/exports and a schema versioning promise.
- Clear mapping to common alert formats (e.g., CAP) if public alerts are in scope.

---

## 2) What the current repo already gives an operator (positives)

From the provided docs:
- **Pipeline decomposition** (ingest → extract → geo → dedupe → score → alert → logging/explainability). This supports reasoning about where errors come from.
- Explicit principle: **severity and confidence are separate** (good operator-aligned design).
- `event.schema.json` includes:
  - `category`, `severity` (1–5), `confidence` (0–1)
  - optional `when_iso`, `where_text`, `lat/lon`, `location_uncertainty_m`
  - `cluster_id` and an `explanation[]` concept for audit trail
- `METRICS.md` adopts an “honest numbers” stance and admits gaps (e.g., full pipeline latency TBD; medical recall low).

These are strong *trust signals*, but they are not yet sufficient for operational adoption.

---

## 3) Gap analysis (operator adoption blockers visible from current docs)

### 3.1 “Validated” is claimed but not defined
**Docs say:** “validated, structured alerts” (README).

**Gap:** no operational definition or states.

**Operator impact:** unclear whether this is rumor intake vs corroborated truth; organizations will assume liability risk.

### 3.2 Metrics are not yet decision-grade
**Docs show:** p95 for text classification (~17ms) and “full pipeline TBD”; category status with recall note for Medical (~50% recall).

**Gap:** no precision, no false-positive rate, no calibration, no end-to-end benchmarks.

**Operator impact:** cannot set SOP thresholds (e.g., “auto-page at confidence ≥ X”).

### 3.3 Failure modes and degraded-mode behavior are undocumented
**Docs show:** stage list, but not “what happens if geo fails / dedupe fails / upstream dependencies fail”.

**Operator impact:** unpredictable failure is worse than known limitations.

### 3.4 Operator workflow and controls are only mentioned, not specified
**Docs say:** “Ops console (map view, KPIs)” exists (METRICS).

**Gap:** no workflow description, review gates, suppression, escalation policy, or runbooks.

**Operator impact:** no training or accountability pathway; pilots stall.

### 3.5 Schema is thin on provenance and operational fields
**Docs provide:** event schema with text, category, scores, optional geo/time.

**Gap:** missing fields commonly needed for ops trust:
- source provenance (channel, reporter type/tier)
- ingested timestamp vs observed timestamp
- corroboration counts, contributing report IDs
- event status lifecycle (triage/confirmed/suppressed/resolved)

**Operator impact:** harder to judge credibility quickly; harder to justify actions.

### 3.6 Safety / abuse / harm controls are absent from public docs
**Gap:** no published threat/abuse model, anti-hoax procedures, anti-doxxing stance.

**Operator impact:** high-risk adoption category; disinformation operations are expected.

### 3.7 Governance and change control not described
**Gap:** no public model for who can change thresholds, how changes are audited, how disputes are handled.

**Operator impact:** emergency orgs require clear accountability and change management.

### 3.8 Documentation coherence issues
- `ARCHITECTURE.md` references `DESIGN.md`, but `DESIGN.md` is not present.
- `README.md` says “HADRIAN is not open-source”, while the repo includes an **MIT LICENSE** (this may reflect “docs are open but core system isn’t”, but it should be made explicit).

**Operator impact:** signals immaturity/ambiguity; reduces trust.

---

## 4) Proposed public artifacts (to enable operator trust) — still compatible with a non-open core

These can be published without releasing production code.

### 4.1 Operator trust pack
- **Operator Handbook**: definitions (validated/severity/confidence), triage patterns, examples.
- **Runbooks**: surge, hoax/misinfo response, geocoding outage, kill switch.
- **Console guide**: KPIs definitions and workflows (screenshots acceptable).

### 4.2 Measurement & transparency
- **Evaluation report**: datasets, labeling protocol, precision/recall by category, calibration plots, end-to-end latency under load.
- **SLOs**: latency, availability, and error budget for alerting.

### 4.3 Safety/security/privacy
- **Threat model** + abuse mitigations.
- **Privacy/data handling policy** (PII minimization, retention, access).
- **Security posture** summary (authz, audit logging, disclosure policy).

### 4.4 Interoperability & schema governance
- **Schema versioning policy** and changelog discipline.
- Optional: **CAP mapping** if public alerting is intended.

---

## 5) Bottom line (operator view)

The repo is a solid public **design and schema** starting point with operator-aligned principles (explicit stages; uncertainty represented; severity ≠ confidence). But to be adopted in emergency operations, it needs (public) **definitions, evidence, operator controls/runbooks, and safety/governance artifacts**—not just architecture.
