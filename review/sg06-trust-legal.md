# SG06 — Trust / legal-risk review (README, LICENSE, public claims)

Scope reviewed (current repo docs):
- `README.md`
- `LICENSE`
- `ARCHITECTURE.md`
- `METRICS.md`
- `CHANGELOG.md`
- `schema/event.schema.json`

Focus: wording/positioning and trust/legal-risk signals created by public claims.

---

## Executive takeaways (highest impact first)

1. **License vs positioning conflict (trust risk):** `README.md` says “**HADRIAN is not open-source**” while the repo contains an **MIT License** granting broad reuse rights for “software and associated documentation files”. Most readers will treat MIT as open-source and reuse accordingly; the README line reads like an attempted claw-back and can reduce credibility.
2. **Absolutist/defiant phrasing increases scrutiny:** “**No government filter**” and “**No corporate black box**” are rhetorically strong but can be interpreted as a refusal to comply with lawful requests or basic harm controls.
3. **Reliability implications in a safety domain:** “**validated … alerts**” + “warning infrastructure” can be interpreted as reliance-inducing claims. If users treat outputs as authoritative, misfires (false positives/negatives, delays, wrong location) can become liability/reputation events.
4. **Comparative/market claims can be contestable:** “There is **no infrastructure** for validated citizen-to-citizen warnings” is a broad market statement that competitors or press may challenge.
5. **Schema implies storage of sensitive data:** requiring `raw_text`, `normalized_text`, and `explanation` audit trail implies retention of potentially personal/sensitive content and location/time signals; without explicit minimization/retention rules, trust and compliance burden increases.

---

## 1) License + “not open-source” alignment

### What’s currently stated
- `LICENSE`: MIT
- `README.md`: “HADRIAN is not open-source. But most of: the architecture, the design decisions, and event schema go public.”

### Risk created
- **User confusion:** people will reasonably assume they can reuse/redistribute anything in the repo under MIT.
- **Downstream disputes:** future takedown attempts (“this isn’t open-source”) will conflict with the already-granted MIT permissions.

### Practical remediation options (pick one)

**Option A — keep MIT, clarify what’s not open-source (recommended for trust)**
- Update README to: “**The production HADRIAN service/operational code is not open-source. This repository publishes documentation and schemas under MIT.**”
- Add a “What’s in this repo / what’s not” section.

**Option B — change license to match intent**
- If you truly don’t want reuse: replace MIT with a license aligned to docs-only publication intent (or a more restrictive license). Expect lower adoption.

**Option C — split licensing**
- Example: schema under permissive license, prose under CC BY 4.0, with explicit headers.

---

## 2) Safe claims vs risky claims (practical table)

“Safe” = less likely to be interpreted as a promise/guarantee, anti-compliance posture, or contestable market statement.

| Source | Claim / phrasing (verbatim or near) | Why risky (trust/legal) | Safer alternative (keeps intent) |
|---|---|---|---|
| README | “**No government filter. No corporate black box.**” | Absolutes can read as refusal to comply with lawful orders; invites adversarial scrutiny; “no black box” can be false if any third-party models/APIs are used. | “**Independent and transparent by design.** We publish schemas/decision logic where possible and aim to avoid opaque gatekeeping, while complying with applicable law and preventing abuse.” |
| README | “Raw reports … into **validated**, structured alerts.” | “Validated” in a warning/alert context implies correctness. Users can reasonably rely on it; failures become harder to defend. | “Raw reports … into **structured, auditable** alerts with explicit confidence/severity and documented checks. ‘Validated’ refers to internal consistency checks, not ground-truth confirmation.” |
| README | “from anyone, **anywhere**” | Implies global availability/coverage/language support; increases moderation, sanctions/export, and regulatory expectations. | “from anyone **where supported**, expanding over time.” |
| README | “Citizen-to-citizen **warning infrastructure**.” | “Warning” suggests life-safety. Increases expectation of uptime/accuracy and duty-of-care framing. | “Citizen-to-citizen **situational awareness** / **incident reporting** infrastructure.” (Keep “warning” only with strong reliance disclaimers.) |
| README | “There is **no infrastructure** for validated citizen-to-citizen warnings.” | Broad market claim; easy to dispute; can be seen as disparaging or misleading advertising if used commercially. | “We haven’t found a widely adopted open, auditable standard infrastructure for validated citizen-to-citizen warnings.” |
| README | “Fast. Transparent.” | “Fast” becomes a measurable promise unless scoped; “transparent” can be challenged if any step is opaque. | “Designed for low latency and auditable decisions; benchmarks are versioned and published as measured.” |
| README/ARCH | “Every step is explicit. Every decision is traceable.” / “full audit trail” | Overclaim if any component is opaque (LLM, vendor geocoder, human moderation). Also implies detailed logs (privacy/regulatory). | “We aim for end-to-end traceability. External dependencies are labeled; we log inputs/outputs at appropriate granularity.” |
| ARCH | “uncertainty modeled explicitly **at every step**” | Technical absolute; any exception undermines credibility. | “Uncertainty is modeled and propagated **where available**, and we avoid presenting overconfident outputs.” |
| ARCH | “no panic language, no speculation” | Generally good. Risk is operational: requires moderation/templating so raw reports don’t leak panic language. | Keep; optionally add “alerts are templated to avoid speculation; raw reports are not broadcast verbatim.” |
| METRICS | “honest numbers, no marketing” | Raises expectation of methodological rigor; current doc has “Full pipeline: TBD”. | “Early benchmarks; some measurements still TBD. Methodology and environment will be documented and versioned.” |
| METRICS | “Target: full pipeline under **100ms p95**.” | Could be treated as a commitment; performance depends on deployment and external services. | “Goal: <100ms p95 in controlled deployments; not guaranteed; depends on configuration and external services.” |
| METRICS | “Traffic ✅ Stable … high recall” / “Medical ⚠️ ~50% recall” | Quantified claims without dataset/method definitions are challengeable. “Medical” category heightens perceived safety/health responsibility. | Add: “Measured on internal dev set; definitions + dataset summary forthcoming.” Also add: “Not for diagnosis/triage; informational only.” |
| Schema | `raw_text` + `normalized_text` + `explanation` required | Strongly implies retention of raw user content + reasoning. Potential PII, defamation, sensitive location/time; increases privacy/security obligations. | Add repo-level guidance: redact PII; retention limits; access controls; consider hashing/pseudonymization; default to rounding/uncertainty for location. |

---

## 3) Disclaimer recommendations (drop-in, practical)

These are not “legalese”; they prevent common misreadings and reduce reliance risk.

### A) Reliance / emergency-service disclaimer (high priority)
Add prominently to README and any landing page:
- HADRIAN is **not an emergency service** and is **not** an official public warning system.
- Alerts may be **incorrect, incomplete, delayed, or missing**.
- For emergencies, use **local emergency services** and official channels.

### B) Define “validation” precisely
If you keep the word “validated”, define it once:
- Validation = deduplication/consistency checks/confidence scoring/(optional) human review.
- Validation ≠ confirmed ground truth.

### C) Content/reporting guidelines (defamation/doxxing mitigation)
Add a short “Reporting guidelines” section:
- Do not include names, phone numbers, license plates, or identifying details.
- Do not accuse individuals; describe observable facts.
- No incitement/hate/harassment.
- Abusive content may be removed; accounts may be restricted.

### D) Data-handling signals (because schema implies retention)
Add a minimal “Data handling” note:
- Whether you store raw reports; **retention period**; who can access.
- Location handling (precise vs rounded; uncertainty radius).
- Whether you publish only aggregated statistics.

### E) Compliance posture (replace “no government filter” vibe)
Add a one-liner:
- “We operate independently and may restrict content to comply with applicable law and to prevent harm (e.g., doxxing, incitement).”

---

## 4) Concrete text snippets (ready to paste)

### 4.1 README: clarify open-source vs not
> **What’s public vs what’s not**
>
> The production HADRIAN service and its operational code are **not open-source**. This repository publishes **architecture notes and an event schema** so the system’s design can be inspected and debated.
>
> The contents of this repository are licensed under `LICENSE`.

### 4.2 README: safety & reliance
> **Important**: HADRIAN is an informational system in development. It is **not an emergency service** and may produce false or missing alerts. Always use official channels and local emergency services for urgent situations.

### 4.3 README: reporting guidelines
> **Reporting guidelines**: Avoid personal data (names, phone numbers, plates). Describe observable facts and approximate location. Do not post accusations about individuals.

---

## 5) Maintainer notes (small trust fixes)

- `ARCHITECTURE.md` references `DESIGN.md` which is not present → either add it or remove the reference.
- Consider adding `METHODS.md` for metrics: dataset description, metric definitions (recall, p95), measurement environment, and update cadence.

---

## Owner checklist

- [ ] Resolve license/positioning mismatch (Option A/B/C)
- [ ] Replace absolutist phrases (“no government filter”, “validated”) with defined, bounded language
- [ ] Add emergency/reliance disclaimer
- [ ] Add reporting + moderation guidelines
- [ ] Add minimal privacy/retention/data-handling notes consistent with schema
- [ ] Add metrics methodology note or downgrade/qualify numerical claims
