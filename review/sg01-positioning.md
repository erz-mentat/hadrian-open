# SG01 — Positioning & Narrative (HADRIAN)

## 1) Current strengths
- **Clear moral stance + differentiation:** “transparent, traceable, no black box” is memorable and aligns with the system design (audit trail, explicit steps).
- **Good problem framing:** contrasts government alerts, proprietary systems, and chaotic crowdsourcing; lands the gap as missing *infrastructure*.
- **Concrete mental model:** the pipeline (`normalize → extract → geo-resolve → deduplicate → score → alert`) makes HADRIAN feel tangible fast.
- **Trust language baked into the architecture:** explicit uncertainty handling, and **severity** kept separate from **confidence**; “no panic language” fits safety-critical comms.
- **Credibility through restraint:** “honest numbers, no marketing” + published schema/metrics signals seriousness.

## 2) Critical weaknesses
- **The promise is bigger than the proof (yet):** “validated” is a high bar, but the README doesn’t quickly explain *how* validation is achieved (cross-checking, human-in-the-loop, abuse resistance). Readers may doubt the claim.
- **Adversarial framing may narrow the audience:** “No government filter / No corporate…” can trigger misinformation/vigilantism concerns even if the design is careful.
- **“Citizen-to-citizen” is ambiguous/exclusionary:** can read political/legal (“citizen” vs resident). “Community-to-community” / “people-to-people” is clearer and safer.
- **Repo expectation mismatch:** “not open-source” appears early; without a positive replacement (“this repo is the open spec + benchmarks”), you risk immediate bounce.
- **Weak call-to-action:** no crisp “start here” path (Architecture → Schema → Metrics) or explicit ask for what feedback/contributions are wanted.

## 3) Rewritten one-liner
> **HADRIAN is an auditable warning pipeline that turns community incident reports into structured, geo-resolved, deduplicated alerts — with a traceable decision trail.**

## 4) Rewritten 6-line hero section for README
```md
# HADRIAN
An auditable, community-to-community warning pipeline.
Turn messy witness reports into structured events and calm, actionable alerts.
Geo-resolve, deduplicate, and score **severity** and **confidence** separately.
This repo publishes the architecture, event schema, and benchmarks (the service isn’t open-source).
Follow updates: @erz_qwzhdrch • substack.com/@erzhadrian
```
