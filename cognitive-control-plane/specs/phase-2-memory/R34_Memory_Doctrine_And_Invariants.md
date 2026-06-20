# R34 — Memory Doctrine and Invariants

Type: **Architecture / Doctrine Foundation**  
Summary: Defines the non-negotiable rules for canonical memory, derived memory, provenance, inspectability, and complexity control across the memory platform.

## Goals

- Make explicit that raw evidence is canonical and all higher-order memory objects are derivative.
- Prevent future specs and implementations from silently treating summaries, graph edges, overlays, or suggestions as truth.
- Establish shared design laws that all memory, retrieval, and proactive layers must obey.
- Preserve debuggability as the system grows more capable.

## Non-goals

- Replacing existing foundation behavior with a new storage model.
- Forbidding summaries, entities, graphs, overlays, or proactive logic.
- Turning the platform into a raw-only system with no augmentation.
- Requiring every implementation detail to be finalized before future work continues.

## Core doctrine

- **Canonical evidence**: raw messages, raw event records, artifact references, and persisted source documents in the memory store are the canonical source of truth.
- **Derived memory**: summaries, entities, knowledge graph edges, hygiene flags, overlays, profiles, and proactive suggestions are non-canonical derivatives.
- **Additive layering**: derived layers may augment retrieval and actionability, but may not silently supersede canonical evidence.
- **Traceability**: any important derived object must retain enough provenance for a human to answer, “What source memory caused this?”
- **Inspectability**: advanced retrieval paths must remain explainable and comparable against a simpler raw baseline.
- **Complexity tax**: new layers must justify themselves with measurable usefulness, not aesthetic cleverness.

## Design laws

- Source evidence wins when source and derivative conflict.
- Derived objects must be invalidated, superseded, or revalidated when their supporting source evidence changes materially.
- No inference-only mutation of canonical memory.
- No important derived object without provenance.
- No retrieval layer that cannot be bypassed for debugging.
- No new memory abstraction without an explicit failure-mode story.

## Layer model

- **Canonical substrate**: persisted messages, events, artifacts, and source records.
- **Derived retrieval augmentation**: reranking, overlays, summaries, entities, graph support, hygiene analysis.
- **Operational control plane**: routing, profiles, traces, validation, observability.
- **Action/proactive layer**: suggestions, briefings, nudges, follow-through.

## Data / Contract Expectations

All non-canonical memory objects should eventually converge on a shared derivative contract with at least:

- `is_canonical = false`
- `derivation_type`
- `source_refs`
- `confidence` when applicable
- `explanation` or `rationale`
- `created_at`
- `last_validated_at` when applicable

## Runtime Contract

### Inputs

- canonical records from memory store
- retrieval request and mode/profile context
- optional derived-memory outputs
- observability metadata

### Outputs

- response-ready memory context
- provenance-aware derivative references
- debug metadata sufficient to inspect how context was assembled

## Integration Requirements

This doctrine constrains future and amended specs:

- Retrieval APIs must support access to raw canonical evidence.
- Derived-memory producers must record provenance and derivation metadata.
- Proactive and advisory systems must cite their trigger evidence.
- Evaluation layers must compare augmented behavior against a simpler baseline.
- Systems that consume derived memory must tolerate stale, contradicted, or superseded derivatives safely.

## Observability

Emit structured logs for:

- canonical vs derived source usage
- provenance gaps
- derivative objects lacking source refs
- retrieval path chosen
- fallback from advanced retrieval to raw retrieval
- doctrine violations detected by validation tooling

## Evaluation

Test for:

- source evidence available for important outputs
- summaries and overlays not replacing source records
- retrieval debuggability under advanced augmentation
- consistent behavior when advanced layers are disabled
- low incidence of provenance-less derivatives

## Concrete examples

Good:

- A summary says “user prefers concise answers” and links to source messages supporting that preference.
- A proactive suggestion cites the triggering event and prior related memory items.

Bad:

- A graph edge is treated as truth with no supporting evidence.
- A summary conflicts with raw history and wins by default.
