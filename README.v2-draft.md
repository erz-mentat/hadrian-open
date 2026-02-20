# HADRIAN

An auditable community-to-community warning pipeline.
Turns raw incident reports into structured events and calm, actionable alerts.
Geo-resolves location, deduplicates reports, and scores severity and confidence separately.
Each alert includes a traceable decision path.
This repository publishes architecture, schema, and metrics.
The production service implementation is not included here.

## What this is
- A public interface for understanding how HADRIAN reasons.
- A documentation + schema repo for builders, operators, and reviewers.

## What this is not
- Not an emergency service.
- Not a replacement for official warning channels.
- Not the full production codebase.

## What is public vs private

### Public in this repository
- `ARCHITECTURE.md` - pipeline overview
- `schema/event.schema.json` - event contract
- `METRICS.md` - current benchmark status and known gaps
- `CHANGELOG.md` - published changes

### Private / not included here
- Production infrastructure and deployment setup
- Anti-abuse internals and operational controls
- Full running service implementation

License note: repository contents are licensed under `LICENSE` (MIT).

## Problem
Emergency information is fast, but reliable warning signals are fragmented.

Today, most people rely on one of three channels:
- official systems that are authoritative but often top-down and slower to update
- proprietary platforms that are polished but closed and hard to audit
- group chats and social feeds that are fast but noisy and hard to verify

What is missing is a shared, auditable pipeline that can turn raw public reports into structured events with explicit uncertainty.

## Approach
HADRIAN treats reports as noisy inputs and converts them into structured events through a fixed pipeline.
Instead of hiding uncertainty, it represents it in the output. It also keeps severity and confidence separate so operators can reason clearly.

```text
raw report -> normalize -> extract -> geo-resolve -> deduplicate/cluster -> score -> alert
```

Each event carries a traceable decision path through the pipeline.

## Current status (Feb 2026)
MVP in progress.

Evidence from `METRICS.md`:
- text classification p95 latency: ~17ms
- full end-to-end pipeline latency: TBD (target: <100ms p95)
- category status:
  - A (Traffic): stable
  - B (Medical): ~50% recall, target >=70%
  - C (Weather): stable
  - D (Assembly): stable
  - E (Violence): stable

What currently works:
- text classification
- geocoding + deduplication
- ops console (map view, KPIs)
- offline priors integration

Known gap: full end-to-end latency and weak-category recall still need measurable improvement before stronger reliability claims.

## Quickstart
This repo is documentation-first. Start here:
1. `ARCHITECTURE.md` - pipeline overview
2. `schema/event.schema.json` - event contract
3. `METRICS.md` - current benchmark status and gaps
4. `CHANGELOG.md` - what changed and when

Validate a local `event.json` against the public schema:

```bash
pip install jsonschema
python -m jsonschema -i event.json schema/event.schema.json
```

## Known limitations
- HADRIAN is not an emergency service. Use official channels for urgent incidents.
- False positives and false negatives are expected in early-stage systems.
- Coverage and quality vary by category, region, language, and report quality.
- Full end-to-end latency is not yet published in the current metrics file.
- This repository publishes the public contract and docs, not production anti-abuse internals.
- `ARCHITECTURE.md` currently references `DESIGN.md`, which is not yet published in this repo.

## Roadmap (90 days)
### Days 1-30: credibility baseline
- finalize README v2 and scope language
- publish `DESIGN.md` and `THREAT_MODEL.md`
- add synthetic examples and schema validation scripts

### Days 31-60: measurement discipline
- publish `METHODS.md` with reproducible evaluation protocol
- add first end-to-end latency measurement
- publish schema v1.1 with migration notes

### Days 61-90: public adoption surface
- publish a synthetic demo event feed (+ minimal viewer or walkthrough)
- add `GOVERNANCE.md` and operator-facing safety notes
- cut a tagged release with docs, schema, metrics, and examples

## Follow
- X: https://x.com/erz_qwzhdrch
- Substack: https://substack.com/@erzhadrian
