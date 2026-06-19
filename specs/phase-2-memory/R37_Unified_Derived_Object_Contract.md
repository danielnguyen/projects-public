# R37 — Unified Derived Object Contract

Type: **Memory Infrastructure / Hardening**  
Summary: Defines a canonical contract for derived cognition artifacts produced by the system.

## Motivation

The system produces multiple forms of derived state:

- summaries
- proactive suggestions
- graph edges
- hygiene flags
- overlays
- retrieval augmentations
- OCR/caption text
- synthesized briefings

Without a shared contract, these artifacts drift into inconsistent provenance, lifecycle, confidence, and rebuild semantics.

R37 standardizes the minimum structure and invariants for derived cognition objects.

## Goals

- Unify provenance semantics across derived objects.
- Preserve rebuildability and inspectability.
- Prevent hidden mutation of canonical memory.
- Standardize lifecycle and invalidation behavior.
- Allow future derivative systems without ad-hoc schema growth.

## Non-goals

- Replacing canonical source memory.
- Defining ranking/retrieval algorithms.
- Preventing additive domain-specific fields.
- Forcing all derived objects into a single database table.

## Canonical doctrine

Derived objects:

- are additive
- are reconstructable
- must reference source evidence
- must not silently replace source memory
- may be deleted and regenerated
- must remain attributable to a derivation process

## Required fields

Every derived object must expose:

- `derived_id`
- `owner_id`
- `derivation_type`
- `source_refs`
- `derivation_version`
- `created_at`
- `status`

Optional but recommended:

- `confidence`
- `explanation`
- `expires_at`
- `invalidated_at`
- `generation_trace_id`

## Source reference contract

`source_refs` may reference:

- message ids
- artifact ids
- derived ids
- event ids
- graph entity ids

All references must remain inspectable.

## Lifecycle states

Recommended states:

- `active`
- `stale`
- `invalidated`
- `superseded`
- `rebuilding`

## Invalidation rules

Derived objects should be invalidated when:

- source evidence changes materially
- derivation logic version changes
- source refs disappear
- contradiction detection invalidates assumptions

## API implications

Derived-object APIs should support:

- provenance inspection
- raw source traversal
- derivation replay
- invalidation visibility
- rebuild triggers

## Evaluation

Test for:

- provenance completeness
- source inspectability
- rebuild success
- deterministic replay where appropriate
- no hidden canonical mutation

## Risks

- over-normalization reduces implementation flexibility
- provenance graphs become excessively verbose
- rebuild logic becomes operationally expensive

## Implementation guidance

R37 should be implemented through adapter-level or additive contract views first. Existing tables may expose the derived-object contract through helpers, response metadata, or compatibility views without requiring a universal `derived_objects` table.

The minimum implementation should preserve existing API shapes and records while making high-value derived objects inspectable.
