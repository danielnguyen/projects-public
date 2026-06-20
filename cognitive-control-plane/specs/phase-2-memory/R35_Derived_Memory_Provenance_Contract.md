# R35 — Derived Memory Provenance Contract

Type: **Data Contract / Retrieval Safety**  
Summary: Standardizes how non-canonical memory objects represent provenance, confidence, validation state, and explanation so derived memory remains inspectable and safe to use.

## Goals

- Define a shared contract for summaries, entities, overlays, graph edges, hygiene flags, recommendations, and similar derivative objects.
- Ensure every meaningful derived object can be traced to supporting source evidence.
- Make derivative memory easier to debug, validate, and expire.
- Reduce ambiguous one-off schemas across future specs and services.

## Non-goals

- Requiring all existing tables to migrate immediately.
- Forcing every derivative type to use identical confidence semantics.
- Replacing canonical records with derivative records.
- Deciding full storage schema for every implementation in advance.

## Proposed Contract

### Required fields

- `derivation_id`
- `derivation_type`
- `is_canonical = false`
- `source_refs`
- `created_at`
- `status`
- `owner_id`

### Recommended fields

- `confidence`
- `explanation`
- `last_validated_at`
- `validation_state`
- `supersedes`
- `superseded_by`
- `expires_at`
- `scope_json`

## `source_refs` contract

`source_refs` should be an array of structured references. Each entry should include at least:

- `ref_type` — such as `message`, `event_log`, `artifact`, `document`, or `trace`
- `ref_id` — canonical identifier for the referenced source item
- `support_kind` — such as `direct`, `inferred_from`, or `corroborating`

Optional fields may include:

- `span` — relevant text span, offsets, or section identifier when finer-grained evidence is available
- `field_path` — specific field within a structured record
- `note` — short explanation of how the source supports the derivative

Source references should be sufficient to retrieve the underlying evidence without guesswork.

## Status model

Suggested values:

- `draft`
- `active`
- `stale`
- `contradicted`
- `superseded`
- `expired`
- `retracted`

Status transitions should be auditable. Derivative objects should move to `stale`, `contradicted`, `superseded`, `expired`, or `retracted` rather than being silently overwritten when support weakens or changes.

## Confidence model

Confidence is optional but recommended when inference is involved.

- Confidence should not be fabricated for deterministic transforms.
- Confidence should be low or omitted when support is weak.
- Confidence should never be treated as a substitute for provenance.

## Object classes in scope

- profile inferences
- user preference summaries
- relationship notes
- memory overlays
- salience labels
- hygiene flags
- contradiction flags
- graph nodes and edges
- proactive suggestions and briefings

## Runtime Contract

### Inputs

- canonical evidence references
- derivation logic metadata
- optional confidence/validation outputs

### Outputs

- derivative object with provenance and explanation
- validation state suitable for retrieval and audit

## API / Integration Guidance

- Derivation-producing endpoints should return `source_refs` in responses or make them fetchable.
- Retrieval endpoints should be able to expose provenance when derivative context is returned.
- Validation and hygiene jobs should update derivative status rather than mutate source evidence.
- UI surfaces should be able to show “because of these items” when needed.

## Observability

Emit structured logs for:

- derivative creation by type
- missing or invalid source refs
- validation state changes
- contradiction detection results
- derivative expiry and supersession events

## Evaluation

Test for:

- every sampled derivative object has valid provenance
- stale derivatives do not outlive contradicted source evidence indefinitely
- graph edges and summaries can be explained from source refs
- proactive outputs can cite their trigger evidence

## Concrete examples

Good:

- A hygiene flag references two conflicting source messages and marks itself `contradicted` after validation.
- A graph edge includes `source_refs`, a confidence score, and an explanation of the inferred relation.

Bad:

- A preference summary exists with no way to inspect what conversation established it.
- A suggestion claims urgency but does not cite the event or memory that triggered it.
