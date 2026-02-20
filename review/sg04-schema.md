# SG04 — Schema Review Memo: `schema/event.schema.json`

**Repo context used:** `README.md`, `ARCHITECTURE.md`, `METRICS.md`, `CHANGELOG.md` (v0.1.0, Feb 2026).

**File reviewed:** `schema/event.schema.json` (JSON Schema Draft 2020-12)

HADRIAN’s docs describe a pipeline that converts raw citizen reports into **validated, structured, auditable events**, with explicit uncertainty at each step and strict separation between **Severity** (what happened) and **Confidence** (how sure).

This memo focuses on: (1) high-level schema risks, (2) missing/under-specified fields, (3) naming/semantic improvements, and (4) a revised **public contract** proposal.

---

## 1) Current schema (v1 as published): quick inventory

### Required
- `id` (string)
- `category` (enum `A|B|C|D|E`)
- `severity` (int 1–5)
- `confidence` (number 0–1)
- `raw_text` (string)
- `normalized_text` (string)
- `tags` (string[])

### Optional / nullable
- `when_iso` (string|null)
- `where_text` (string|null)
- `lat` / `lon` (number|null)
- `location_uncertainty_m` (number|null)
- `cluster_id` (string|null)
- `explanation[]` of `{ step, reason }` (both strings)

Strengths: compact, readable, captures HADRIAN’s key conceptual split (severity vs confidence) and an audit trail primitive.

---

## 2) High-level schema risks

### Risk A — Ambiguous entity semantics (event vs report vs cluster)
The schema calls the object an “event”, but includes `cluster_id` (“merged into an existing cluster”). Meanwhile the repo docs describe the pipeline output as deduplicated/clusted/scored *events* that become alerts.

**Why this matters:** consumers can’t tell whether:
- `id` is a canonical, stable “alert identity”, or
- `id` is a per-report candidate later merged into a different canonical identity.

This ambiguity breaks downstream deduplication, update handling, and alert lifecycle management.

**Recommendation:** explicitly define (and encode) identity semantics. Either:
- make `id` the canonical event ID and remove/rename `cluster_id`, **or**
- keep per-report IDs but add a required canonical pointer (`canonical_event_id`) and provenance.

### Risk B — Public safety & privacy: required `raw_text` and detailed audit trail
Making `raw_text` **required** makes the schema unsuitable as a **public feed contract** because raw citizen reports routinely contain:
- PII (names, phone numbers),
- location specifics about vulnerable people/targets,
- defamation / doxxing,
- instructions or details that can be abused.

Similarly `explanation` can leak:
- verbatim PII (if reasons quote the report),
- internal decision rules/prompting that enable adversarial gaming.

**Recommendation:** split schemas (public vs internal) or at least make raw/audit fields optional and explicitly redacted/sanitized for public distribution.

### Risk C — Validation is too permissive for interoperability
Key constraints are missing:
- `when_iso` has no `format: "date-time"`.
- `lat`/`lon` have no bounds (`[-90,90]`, `[-180,180]`).
- `lat` can exist without `lon` and vice versa.
- `tags` allow duplicates, empty strings, and unbounded growth.
- `explanation.items.properties` has no `additionalProperties: false` and allows unexpected fields.

**Impact:** clients will implement ad-hoc parsing rules, leading to inconsistent behavior.

### Risk D — Versioning is not part of the instance payload
The schema file has a top-level `"version": "1"`, but events validated against it don’t necessarily carry a `schema_version`.

**Impact:** once events are shared across systems, version routing becomes out-of-band and fragile.

**Recommendation:** include a required `schema_version` in the event object.

### Risk E — Null vs missing is undefined
Many properties are `type: [<t>, "null"]` but the contract doesn’t define whether:
- omitted means “unknown”,
- null means “unknown”, or
- null means “known to be absent / not applicable”.

**Recommendation:** pick a convention (commonly: omit unknowns in JSON) and reserve explicit null for “known absent/not applicable”, or introduce explicit `*_status` fields.

### Risk F — Category encoding is compact but brittle for a public API
`category` is `A..E`. It is stable within this repo, but not self-describing and hard to extend without breaking strict enum consumers.

**Recommendation:** keep the code, but add a descriptive, stable identifier (e.g., `traffic_accident`) in a structured `category` object.

### Risk G — Forward compatibility and strictness are unspecified
The schema does not set `additionalProperties`. By JSON Schema defaults, **additional properties are allowed**, which can be good for evolution—but only if you explicitly document extension rules.

**Recommendation (public contract):** either:
- set `additionalProperties: false` and provide an `extensions` object for controlled evolution, **or**
- explicitly document that unknown fields may appear and must be ignored.

---

## 3) Missing or under-specified fields (relative to the repo’s stated pipeline)

The pipeline described in `ARCHITECTURE.md` includes ingest/normalize, extraction, geo resolution, clustering, scoring, alert generation, and logging/explainability.

To support that end-to-end *as a contract*, consider the following gaps:

### Identity & lifecycle (needed for alerting)
- `event_id` (canonical identity; clarify format: UUID/ULID)
- `status` (e.g., `open|updated|resolved|false_positive`) to avoid “silent disappearance”
- `revision` (monotonic integer per `event_id`)
- timestamps: `created_at`, `updated_at`, `published_at` (all `date-time`)

### Time semantics with uncertainty
Current `when_iso` conflates “occurred at” and “observed/received at”. A robust contract typically distinguishes:
- `occurred_at_*` (what happened when)
- `observed_at` / `received_at` (when the system learned)
- time uncertainty (range or ± seconds)

### Location as a single structured object
Split location fields are easy to drift (lat without lon, etc.). Prefer:
- `location.text`
- `location.point { lat, lon }`
- `location.uncertainty_m`

### Provenance without exposing raw content
Instead of requiring `raw_text`, publish:
- `report_count`
- minimal `sources[]` metadata (channel, received time, language) with no personal identifiers
- optional `evidence.excerpts[]` only if explicitly redacted

### Public-facing text
The docs mention “brief situation description” and “no panic language”. That should be a first-class field:
- `summary` (safe-to-broadcast, redacted)

---

## 4) Naming / semantics improvements (recommended)

Rename to reduce ambiguity:
- `id` → `event_id`
- `when_iso` → `time.occurred_at_best` (and add `time.observed_at`)
- `where_text` → `location.text`
- `lat`/`lon` → `location.point.lat` / `location.point.lon`
- `location_uncertainty_m` → `location.uncertainty_m`
- `cluster_id` → either remove (if `event_id` is canonical) or rename to `canonical_event_id`
- `explanation` → `audit.steps` (and restrict content to redacted, public-safe statements)

Contract semantics to document explicitly:
- severity/confidence meanings (already described well in the schema description; keep it)
- whether severity is ordinal only or tied to operational actions
- what “confidence” measures (truth of claim vs classification certainty vs geo certainty)

---

## 5) Revised **public** contract proposal (v2)

### Goal
Preserve HADRIAN’s principles (explicit uncertainty, separations of severity vs confidence, traceability) while making the payload safe and stable as a **public interface**.

### Proposal: publish **two contracts**
1. **Public event contract** (safe broadcast payload)
2. **Internal/audit contract** (contains raw text, detailed step-by-step trace)

This aligns with the repo’s stated “transparent, traceable” goal without forcing privacy-risking data into the public schema.

### 5.1 Minimal public event shape (recommended required fields)
- `schema_version` (string, e.g., `2.0`)
- `event_id` (string)
- `category` (object)
  - `code`: `A|B|C|D|E`
  - `kind`: descriptive string (`traffic_accident|medical|weather_infrastructure|assembly_demo|violence_vandalism`)
- `severity.level` (1–5)
- `confidence.score` (0–1)
- `summary` (string; safe-to-broadcast)
- `tags` (unique string array)

### 5.2 Optional (but strongly recommended) public fields
- `time`:
  - `occurred_at_best` (`date-time`)
  - `occurred_at_range` (`{ start, end }`)
  - `uncertainty_s` (number)
  - `observed_at` (`date-time`)
- `location`:
  - `text` (string)
  - `point` (`{ lat, lon }` with bounds)
  - `uncertainty_m`
- `lifecycle`:
  - `status`, `revision`, `created_at`, `updated_at`
- `provenance`:
  - `report_count`
  - `sources[]` (channel + received_at + language; avoid user identifiers)

### 5.3 Public example (illustrative)
```json
{
  "schema_version": "2.0",
  "event_id": "evt_01HZY7K9Q7J6WQ2A3Z9M2C9X1P",
  "category": { "code": "A", "kind": "traffic_accident" },
  "severity": { "level": 3 },
  "confidence": { "score": 0.72 },
  "summary": "Traffic accident reported near the central bridge; expect delays.",
  "time": {
    "observed_at": "2026-02-19T20:41:55Z",
    "occurred_at_best": "2026-02-19T20:41:00Z",
    "uncertainty_s": 1800
  },
  "location": {
    "text": "near the central bridge",
    "point": { "lat": 52.5201, "lon": 13.4049 },
    "uncertainty_m": 350
  },
  "tags": ["road_blocked", "multi_vehicle"],
  "lifecycle": { "status": "open", "revision": 1, "created_at": "2026-02-19T20:42:10Z" },
  "provenance": { "report_count": 2, "sources": [{ "channel": "telegram", "received_at": "2026-02-19T20:41:55Z" }] }
}
```

### 5.4 Contract tightening (public)
For interoperability, the public schema should enforce:
- `time.*` strings with `format: "date-time"`
- `location.point` requires both `lat` and `lon`, with numeric bounds
- `tags`: `uniqueItems: true`, item `minLength: 1`, consider `maxItems`
- consider `additionalProperties: false` plus `extensions` for forward-compat

---

## 6) Recommended next actions (smallest high-impact steps)

1. **Decide/declare identity semantics**: is `id` canonical or pre-merge? Encode it.
2. Add **instance-level** `schema_version`.
3. Create a **public schema** that does not require `raw_text` and restricts audit content.
4. Tighten validation for time (`date-time`), location (bounds + coupling), and tags (uniqueness).
5. Upgrade category from bare `A..E` to `category: { code, kind }` to be self-describing and extensible.
