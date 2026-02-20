# Memo: Public-facing review of `ARCHITECTURE.md`

**Repo:** `tmp/hadrian-open-review`  
**Docs consulted:** `ARCHITECTURE.md`, `README.md`, `METRICS.md`, `schema/event.schema.json`  
**Goal:** improve `ARCHITECTURE.md` for **public readers** (civic partners, journalists, potential users, and technically curious non-engineers) without turning it into an internal implementation spec.

---

## Executive take
The current `ARCHITECTURE.md` is a strong **pipeline sketch** with good values (uncertainty modeled, severity vs confidence separated, no panic language, audit trail). But as a public architecture overview it is too *inside-out*: it lists steps without explaining the **system context**, **actors**, **inputs/outputs**, **validation meaning**, and **safety/privacy boundaries** that determine whether readers trust it.

---

## 1) What is already good (keep)

- **Pipeline mental model is clear and scannable.** The 7 stages match the repo’s thesis (raw reports → structured alerts).
- **Principles are embedded where they matter:** uncertainty, severity≠confidence, no speculation, auditability.
- **The doc stays non-operational.** It avoids leaking implementation details while still describing intent.

---

## 2) What’s missing / confusing for public readers (add)

### A) System context: “what talks to what?”
Public readers need a top-level picture:
- Where reports come from (channels/formats)
- Where alerts go (destinations, update behavior)
- Where the **ops console** and any **human review** fits (mentioned indirectly via `METRICS.md`)

### B) Meaning of “validated”
`README.md` promises “validated, structured alerts.” To be credible, the architecture doc should define:
- What validation is **today** (automated plausibility checks, dedupe, geo plausibility, etc.)
- When humans intervene (if/when)
- What validation **does not** guarantee (it reduces uncertainty; it doesn’t eliminate it)

### C) Definitions of terms
Words like *normalize, extract, deduplicate/clustering, uncertainty radius, audit trail* should be defined in plain language.

### D) Safety/privacy/abuse framing
For citizen reporting, trust requires explicit guardrails:
- PII handling and redaction (reports may contain names, phone numbers, addresses)
- Abuse resistance (spam/false reports/brigading)
- Harm reduction (thresholding, rate limits, careful language, “unknown” handling)

### E) Broken reference
`ARCHITECTURE.md` says “See `DESIGN.md`” but there is **no `DESIGN.md`** in this repo. Broken links reduce trust.

---

## 3) What to remove or rewrite (reduce jargon)

- Replace **“semantic similarity per category”** → “text similarity signals tuned by category.”
- Avoid internal-ish phrasing like **“aspect-compatibility”** (appears in `METRICS.md`) → explain via an example (“storm reports may relate to outage events”).
- Don’t lead with the pipeline. Start with **what HADRIAN is/isn’t** and the end-to-end system behavior; then show the pipeline.

---

## 4) What to add (highest ROI changes)

1. A one-screen **system context diagram** and one-line explanation per component.
2. A short section defining **Severity** and **Confidence** (and why they are separate).
3. A “**What ‘validated’ means**” section that matches current maturity (`METRICS.md`).
4. A “**Safety, privacy, abuse resistance**” section (even if early / partial).
5. A **worked example**: raw report → normalized text → resulting Event JSON (redacted), referencing `schema/event.schema.json`.
6. Fix the `DESIGN.md` reference by either:
   - creating `DESIGN.md`, or
   - inlining the design principles into `ARCHITECTURE.md`.

---

## 5) Proposed rewrite skeleton for a public `ARCHITECTURE.md`

> This is a **draft structure** (headings + intent). It’s designed to be understandable without internal code knowledge.

### Title
**HADRIAN Architecture (Public Overview)**

### 0) Audience & transparency promise
- Who this doc is for
- What is disclosed vs intentionally not disclosed (security/abuse considerations)

### 1) What HADRIAN is (and isn’t)
- One-paragraph description: citizen-to-citizen warning infrastructure
- Non-goals / disclaimers:
  - not emergency dispatch
  - not a replacement for official alerts
  - outputs are probabilistic; the system reduces uncertainty

### 2) Actors
- Reporters (submit observations)
- Subscribers (receive alerts)
- Operators/moderators (review/merge/suppress/correct)
- External priors (optional; align with “offline priors integration” in `METRICS.md`)

### 3) System at a glance (context diagram)
```
[Reporters] -> [Ingest] -> [Pipeline] -> [Event + Audit Log] -> [Alert Delivery]
                                     \-> [Ops Console]
```
- 1–2 lines per block describing purpose and outputs.

### 4) Data model: report → event
- What is a “report” (unstructured text; future media)
- What is an “event” (structured record)
- Link: `schema/event.schema.json`
- Explain the two key scores:
  - **Severity (1–5):** impact if true
  - **Confidence (0–1):** how sure we are

### 5) Pipeline overview (plain-language)
Keep the existing 7 stages, but for each stage add:
- what it does
- what it outputs
- how uncertainty is represented

1) Ingest & Normalize  
2) Extraction  
3) Geo Resolution (coordinates + uncertainty radius)  
4) Deduplication / Clustering  
5) Scoring (severity separate from confidence)  
6) Alert Generation (brief, non-alarmist; no speculation)  
7) Logging / Explainability (audit trail)

### 6) Validation: what it means here
- Automated checks (dedupe, plausibility, consistency)
- Human review triggers (if any; reflect ops console reality)
- Corrections/updates (how an event evolves)

### 7) Safety, privacy, and abuse resistance
- PII redaction / minimization
- Retention guidance (raw vs normalized vs derived)
- Abuse handling (spam, false reports)
- Harm reduction and publishing thresholds

### 8) Performance & maturity
- Link to `METRICS.md`
- What’s stable vs in progress (keep the “honest numbers” tone)

### 9) Limitations & failure modes
- Unknown time/location
- Ambiguity & multilingual edge cases
- Coordinated misinformation
- Coverage bias

### Appendix A) Worked example
- Raw report
- Normalized text
- Resulting Event JSON (redacted)
- Short explanation snippets

### Appendix B) Category taxonomy
- A–E table (current)
- Versioning policy for future categories

---

## 6) Minimal-change option (if you don’t want a full rewrite)
If you want to preserve the current short format, make these four edits:
1) Add a top section: **What HADRIAN is/isn’t** + define Severity vs Confidence.
2) Add a one-screen **system diagram** (include ops console).
3) Fix the **missing `DESIGN.md` link** (remove or add the file).
4) Add a **worked example** referencing `schema/event.schema.json`.
