# Threat Model

Scope: public-facing HADRIAN pipeline and event outputs described in this repository.

## Security goals
- Preserve integrity of emitted events.
- Reduce false alerts from malicious or low-quality reports.
- Protect sensitive report content from unnecessary exposure.
- Keep operator decisions auditable.

## What is in scope
- Report ingestion and normalization.
- Extraction, georesolution, deduplication, scoring, and alert generation.
- Public event schema and audit/explanation fields.

## Primary adversaries
1. **Spam/brigade attackers**
   - Goal: flood the system with low-quality reports.
2. **Misinformation actors**
   - Goal: trigger false alerts or suppress real incidents.
3. **Targeted harassment actors**
   - Goal: doxxing/defamation through report content.
4. **Data scrapers**
   - Goal: extract sensitive signals from raw event text.

## High-risk abuse scenarios
- Coordinated false reports in a narrow time/geography window.
- Location spoofing and ambiguous place-name abuse.
- Duplicate amplification (same incident re-posted to inflate confidence).
- PII leakage through `raw_text` and `explanation` fields.

## Mitigation principles
- Treat all input as untrusted.
- Keep severity and confidence separate.
- Preserve uncertainty rather than forcing false precision.
- Use conservative alert language (no panic/speculation).
- Maintain traceability for post-incident review.

## Operational controls (expected)
- Rate limits and abuse throttling per source and region.
- Deduplication and corroboration checks before high-impact alerts.
- PII minimization/redaction before publishing public-facing artifacts.
- Manual override/suppression path for suspicious clusters.
- Retention limits and access controls for raw report payloads.

## Residual risk
Residual risk remains for:
- high-quality coordinated misinformation,
- long-tail geolocation ambiguity,
- low-signal categories with weak recall,
- delayed detection under burst load.

These risks must be communicated clearly to users and operators.

## Disclosure and updates
- Threat model should be reviewed on each schema minor version change.
- Major incident learnings should produce explicit mitigation updates.
