# Governance

This document defines how public artifacts in this repository evolve.

## Decision model
- Maintainer-led, evidence-first.
- Changes affecting trust/safety require documented rationale.
- Backward compatibility is preferred for schema updates.

## Artifact ownership
- `README.md`: public positioning and constraints
- `ARCHITECTURE.md` / `DESIGN.md`: system reasoning and boundaries
- `schema/event.schema.json`: machine contract
- `METRICS.md` / `METHODS.md`: measured performance and methodology

## Change classes
1. **Editorial** (wording/docs only)
2. **Schema-compatible** (additive/non-breaking)
3. **Schema-breaking** (requires migration path)
4. **Safety-critical** (changes affecting misuse/false-alert risk)

## Required process by class
- Editorial: normal review.
- Schema-compatible: update changelog + examples as needed.
- Schema-breaking: add migration guidance before release.
- Safety-critical: include threat/risk note and operator impact summary.

## Release discipline
- Semantic-ish progression for docs/schema releases (`0.x` while maturing).
- Every release update includes:
  - what changed,
  - why it changed,
  - known limitations.

## Transparency rules
- Do not overstate reliability.
- Unknowns must remain explicit (`TBD` is acceptable when bounded).
- Claims in `README.md` must match `METRICS.md` and `METHODS.md`.

## Security and abuse posture
- Operational anti-abuse internals are intentionally not published here.
- Public docs should still describe abuse categories and guardrail intent.

## External feedback
- Issues/PRs that improve clarity, safety, and interoperability are welcome.
- Contributions that increase abuse surface without safeguards should be rejected.
