# Design Principles

This document defines the design choices behind the HADRIAN pipeline.

## Core principles

1. **Auditability over opacity**
   - Every stage should produce outputs that can be inspected later.
   - Alerts must include enough context to explain why they were generated.

2. **Uncertainty is explicit**
   - Unknown or ambiguous fields are represented as unknown, not hidden.
   - Location and time uncertainty should be carried through the pipeline.

3. **Severity and confidence stay separate**
   - Severity answers: how serious is the event if true?
   - Confidence answers: how likely is the event to be accurate?
   - These two values must never be collapsed into one score.

4. **Safety-first alert language**
   - Avoid panic language and speculation.
   - Prefer concise, factual summaries with confidence context.

5. **Public contract, private operations**
   - This repository publishes the event contract and architecture.
   - Operational internals (anti-abuse controls, infrastructure, private data handling details) are not published here.

## What “validated” means in HADRIAN

In this repository, “validated” means a report passed pipeline checks such as:
- normalization
- extraction consistency
- georesolution and clustering checks
- scoring and rule checks

It does **not** mean confirmed ground truth.

## Current boundaries

- HADRIAN is not an emergency service.
- Outputs can be wrong, delayed, or incomplete.
- Official channels remain the primary source for urgent incidents.
