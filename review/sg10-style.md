# SG10 — HADRIAN-open public writing style

Raw. Clear. Direct.

This repo already says it best in `METRICS.md`: **“honest numbers, no marketing.”**

Use this voice everywhere public: README, architecture notes, schema docs, changelog.

## Scope

Applies to HADRIAN-open public artifacts in this repo:
- `README.md`
- `ARCHITECTURE.md`
- `METRICS.md`
- `CHANGELOG.md`
- `schema/*`

---

## 1) Non‑negotiables

1. **Anti-hype.** If it reads like positioning, rewrite it as a mechanism or a measurement.
2. **No panic language. No speculation.** (Matches `ARCHITECTURE.md` step 6.)
3. **Credibility beats cleverness.** Remove snarky bracket-asides.
4. **Separate facts from plans.** Label unknowns as unknowns.
5. **Be consistent about “open”.** This repo is MIT-licensed (`LICENSE`). Don’t imply “closed” when the artifacts here are explicitly open.

---

## 2) Tone rules

Write like you’re answering a skeptical question.

- Short sentences.
- One claim per line.
- Concrete nouns/verbs.
- Calm and serious.

Avoid:
- “revolutionary / world-class / game-changing / seamless / robust”
- “always / never” (unless you can prove it)
- “trust me” phrasing (replace with: what we log, what we measure, what we publish)

---

## 3) How to make strong claims without hype

### 3.1 Prefer **mechanisms** over adjectives

Bad:
- “Transparent.”
- “Auditable.”
- “Fast.”

Good:
- “We log each pipeline step from raw report to alert, including intermediate outputs and scores.”
- “Severity and confidence are separate fields.” (This is a design invariant in `ARCHITECTURE.md`.)
- “Text classification p95 is ~17ms; full pipeline p95 is TBD.” (From `METRICS.md`.)

### 3.2 When you use numbers, label their status

Use one of:
- **Measured:** value + where it comes from
- **Target:** goal + why it’s plausible
- **TBD:** explicitly unknown (with a plan to measure)

Example pattern:
- “Full pipeline p95 latency: **TBD** (measurement not published yet). Target: **<100ms p95**.”

### 3.3 Name the failure modes (safety)

At minimum, mention:
- false positives (panic)
- false negatives
- spoofing / coordinated manipulation
- geo ambiguity and uncertainty radius
- duplicate cascades

Don’t promise elimination. Promise handling:
- “We model uncertainty explicitly at each step.” (From `ARCHITECTURE.md`.)

---

## 4) Repo / product scoping language

This repo has an MIT `LICENSE`. So be precise:

Use language like:
- “This repository publishes the **public interface**: architecture notes, metrics, and the event schema.”
- “The running HADRIAN service / production implementation is **not** included here.”

Avoid:
- “HADRIAN is not open-source.” (too broad; reads like a contradiction inside an MIT repo)

Preferred rewrite:
- “The public artifacts in this repo are MIT-licensed. The running service code is not published here.”

---

## 5) Formatting conventions

- Lists > paragraphs.
- Keep code blocks for pipelines/schemas.
- Don’t hide meaning in brackets. Put it in the sentence.

If you need emphasis, use structure (headings, bullets), not adjectives.

---

## 6) Before/after rewrites (README lines)

These rewrites keep the intent but make it checkable and aligned with repo docs.

### 6.1

**Before**
> Citizen-to-citizen warning infrastructure.

**After**
> Citizen-to-citizen warnings: people send reports; we turn them into structured events and alerts, with uncertainty and a logged trace.

### 6.2

**Before**
> No government filter. No corporate black box.

**After**
> Not a government channel and not a vendor black box: the pipeline steps and event contract are published in this repo.

### 6.3

**Before**
> Raw reports — from anyone, anywhere — into validated, structured alerts.

**After**
> We ingest raw reports and run them through a published pipeline (normalize → extract → geo-resolve → deduplicate/cluster → score → alert).

### 6.4

**Before**
> Fast. Transparent.

**After**
> Text classification p95 is ~17ms (`METRICS.md`). Full pipeline latency is TBD. We publish the pipeline and log decision traces.

### 6.5

**Before**
> Every step is explicit. Every decision is traceable.

**After**
> Each stage emits outputs we can audit later (including uncertainty radius, cluster decision, and scoring inputs).

### 6.6

**Before**
> HADRIAN is not open-source.

**After**
> This repo (docs + schema) is MIT-licensed. The running HADRIAN service / production implementation is not included here.

---

## 7) Pre-publish checklist (90 seconds)

- Did we replace adjectives with mechanisms or measurements?
- Did we mark unknowns as **TBD** (and avoid pretending)?
- Did we avoid panic language and speculation?
- Did we name at least one major failure mode relevant to the claim?
- Are “open / public / not included” statements consistent with `LICENSE`?
