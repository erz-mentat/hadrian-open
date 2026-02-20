# sg02 — README information architecture redesign (credibility + clarity)

## Goal

Redesign the README so a first-time reader can quickly answer:
- What problem HADRIAN targets
- What the system does (and does **not**) do
- What is actually in *this repository*
- How mature it is (with honest status/metrics)
- How to get value from the published artifacts (schema/docs)

## Inputs used (current repo docs only)

- `README.md` (positioning + links)
- `ARCHITECTURE.md` (pipeline description)
- `schema/event.schema.json` (public event contract)
- `METRICS.md` (current performance + “what works” list)
- `CHANGELOG.md` (doc/schema release history)
- `LICENSE` (MIT)

---

## Issues in current README (credibility/clarity)

1. **Repo vs. “not open-source” conflict**
   - Current README says “HADRIAN is not open-source.”
   - Repo ships an **MIT LICENSE**, which strongly implies the repo contents *are* open-source. This needs precise scoping language.

2. **Status is not front-and-center**
   - Concrete, honest status exists in `METRICS.md` but isn’t surfaced in the README.

3. **No “how to use what’s here”**
   - The repo is documentation-first (architecture + schema), but there’s no quickstart to validate/consume the schema.

4. **Safety expectation management**
   - “Warning infrastructure” can be read as an emergency system. The README should explicitly say this is **not an emergency service** and should be used alongside official sources.

5. **Broken doc reference**
   - `ARCHITECTURE.md` points to `DESIGN.md` (not present). Either publish it or remove the reference; until then, acknowledge the gap.

---

## Proposed README outline (recommended information architecture)

1. **HADRIAN**
   - One-sentence description
   - Three bullets: what it is / what it isn’t / who it’s for
   - Safety disclaimer (not an emergency service)

2. **Problem** *(replacement text below)*

3. **Solution / Approach** *(replacement text below)*
   - Pipeline diagram (short)
   - Design principles: auditability, uncertainty, severity≠confidence

4. **What’s in this repository**
   - Architecture doc, schema, metrics, changelog, license
   - Explicit license/scoping statement: repo artifacts are MIT; production implementation not included

5. **Status** *(replacement text below; cite METRICS truthfully)*

6. **Quickstart** *(replacement text below)*
   - Reading order
   - Validate JSON against schema

7. **Known limitations** *(replacement text below)*

8. **Roadmap** *(replacement text below)*

9. **Follow**
   - X + Substack links from current README

---

## Paste-ready replacement text blocks

These blocks are meant to replace/augment the README sections directly.

### A) Problem

Emergency information moves fast, but **trustworthy, actionable alerts** still tend to come from a few brittle channels.

Today you usually get one of three options:
- **Official alerting:** authoritative, but often slow, region-locked, and optimized for top‑down broadcast.
- **Proprietary platforms:** fast and polished, but closed systems with limited auditability.
- **Ad-hoc crowdsourcing:** group chats and social feeds that are fast—but noisy, hard to verify, and hard to operationalize.

What’s missing is **infrastructure for citizen reports that can be validated, structured, and audited**, so downstream systems (humans or software) can make informed decisions.


### B) Solution / Approach

**HADRIAN** is a pipeline that turns raw, unstructured citizen reports into **structured events** with explicit uncertainty and an audit trail.

Core idea: don’t pretend reports are perfectly accurate—**model uncertainty** and keep decisions explainable.

High-level pipeline:

```text
raw report → normalize → extract → geo-resolve → deduplicate/cluster → score → alert
```

Design principles (as reflected in the published schema):
- **Auditability by default:** outputs are traceable via an explanation trail.
- **Uncertainty is first-class:** ambiguous locations/times are represented explicitly (e.g., uncertainty radius).
- **Severity ≠ confidence:** how bad something is and how sure we are are separate fields.

The output contract is defined by the public JSON Schema: `schema/event.schema.json`.


### C) Status

**Status (Feb 2026): MVP in progress.** This repository publishes the public-facing documentation and schema.

What’s available here:
- Pipeline overview: `ARCHITECTURE.md`
- Event contract (JSON Schema): `schema/event.schema.json`
- Current benchmarks and gaps: `METRICS.md`
- Release notes for published artifacts: `CHANGELOG.md`

What works (per `METRICS.md`):
- Text classification (some categories stable)
- Geocoding + deduplication
- Ops console (map view, KPIs)
- Offline priors integration

Open gaps (per `METRICS.md`):
- Full end-to-end pipeline latency is **TBD**
- Medical category recall is currently **~50%** (target ≥70%)

**Licensing / scope note:** the contents of this repository are MIT-licensed (see `LICENSE`). The running HADRIAN service / full production implementation is not included in this repo.


### D) Quickstart

This repo is documentation-first.

Recommended reading order:
1. `ARCHITECTURE.md` (pipeline)
2. `schema/event.schema.json` (event contract)
3. `METRICS.md` (current performance + limitations)
4. `CHANGELOG.md` (published changes)

Validate an `event.json` file against the schema:

**Option 1 — Python**
```bash
pip install jsonschema
python -m jsonschema -i event.json schema/event.schema.json
```

**Option 2 — Node (AJV via npx)**
```bash
npx --yes ajv-cli validate -s schema/event.schema.json -d event.json
```

Minimal example `event.json`:
```json
{
  "id": "evt_2026-02-19_0001",
  "category": "A",
  "severity": 3,
  "confidence": 0.72,
  "when_iso": "2026-02-19T20:15:00Z",
  "where_text": "near the central station",
  "lat": 52.5251,
  "lon": 13.3694,
  "location_uncertainty_m": 800,
  "cluster_id": null,
  "tags": ["traffic", "accident"],
  "raw_text": "Crash near the central station, traffic stopped.",
  "normalized_text": "crash near central station traffic stopped",
  "explanation": [
    {"step": "classification", "reason": "Matched traffic/accident indicators"},
    {"step": "geo", "reason": "Resolved 'central station' with 800m uncertainty"}
  ]
}
```


### E) Known limitations

- **Not an emergency service:** do not rely on HADRIAN as your only source in emergencies; verify with official channels.
- **False reports are inevitable:** confidence scoring reduces risk; it does not eliminate misinformation.
- **Coverage/performance varies:** results depend on region, language, vocabulary, and reporting style.
- **Metrics are incomplete:** some benchmarks exist, but full end-to-end latency is still TBD (`METRICS.md`).
- **This repo is the public interface, not the full system:** it publishes the event contract + documentation; it does not ship the production implementation.
- **Documentation gap:** `ARCHITECTURE.md` references `DESIGN.md` (not present in this repo).


### F) Roadmap

- **Publish missing design rationale:** add `DESIGN.md` to cover threat model, abuse cases, safety constraints, and governance assumptions.
- **End-to-end metrics:** publish full pipeline latency (p95) and stage-by-stage breakdown with methodology.
- **Schema evolution:** versioned schema releases (v1.1+) with backward-compat notes.
- **Examples:** add a small set of synthetic/redacted example events + validation scripts.
- **Category improvements:** raise weak-category recall (notably Medical) and publish evaluation notes.

---

## Follow

- Follow the build: https://x.com/erz_qwzhdrch
- Read the thinking: https://substack.com/@erzhadrian
