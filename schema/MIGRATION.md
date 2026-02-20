# Schema Migration Notes

## v1.0 -> v1.1 (2026-02-20)

`schema/event.schema.json` was hardened without changing core conceptual fields.

### Added/changed constraints
- Added `$id` for stable schema identification.
- `version` updated from `1` to `1.1`.
- `when_iso` now enforces `date-time` format for string values.
- `lat` now constrained to `[-90, 90]`.
- `lon` now constrained to `[-180, 180]`.
- `lat` and `lon` are now coupled (`dependentRequired`): if one exists, the other must exist.
- `tags` now requires:
  - at least one item,
  - unique items,
  - non-empty strings.
- `id`, `raw_text`, `normalized_text`, `explanation.step`, and `explanation.reason` now require non-empty strings.
- `location_uncertainty_m` now enforces non-negative numeric values.

### Compatibility impact
- Existing payloads with invalid datetime strings will fail validation.
- Existing payloads with out-of-range coordinates will fail validation.
- Existing payloads containing duplicate/empty tags will fail validation.
- Existing payloads with `lat` but missing `lon` (or vice versa) will fail validation.

### Migration guidance
1. Normalize `when_iso` to RFC3339/ISO date-time strings.
2. Validate and clamp or discard invalid coordinates before event emission.
3. Ensure tags are deduplicated and stripped of empty entries.
4. Emit both `lat` and `lon` together, or emit neither.
5. Enforce non-empty text fields at generation time.
