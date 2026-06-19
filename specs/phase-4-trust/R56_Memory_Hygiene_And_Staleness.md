# R56 — Memory Hygiene and Staleness

## User capability

The assistant can distinguish active, parked, stale, corrected, superseded, and forgotten/demoted context instead of treating every memory as permanently current.

Example:

```text
User: What is next on the project?
Assistant: Current active item is e2e validation. GHCR migration is parked unless deployment blocks testing.
```

## Problem

Durable memory becomes risky when old facts remain active forever.

Without hygiene and staleness handling, the assistant may:

- treat old decisions as current
- keep surfacing parked topics
- repeat corrected mistakes
- overfit to old preferences
- confuse brainstorms with commitments
- use stale world state as present reality

## MVP behavior

The runtime should support simple lifecycle labels for memory/world-state use.

Minimum lifecycle labels:

- `active`
- `parked`
- `stale`
- `superseded`
- `corrected`
- `forgotten_or_demoted`
- `unknown_freshness`

Minimum outputs:

- `freshness_state`
- `last_verified_at`
- `source_kind`
- `confidence`
- `supersedes`
- `superseded_by`
- `use_allowed`
- `mention_as_current_allowed`

## Runtime ownership

The runtime owns active use policy for freshness and staleness.

The memory substrate owns durable storage, provenance, and retrieval metadata.

The orchestrator consumes freshness summaries when assembling responses.

## Contract shape

Illustrative object:

```yaml
memory_hygiene:
  item_id: project_ghcr_migration
  freshness_state: parked
  last_verified_at: 2026-06-06T00:00:00Z
  confidence: high
  use_allowed: true
  mention_as_current_allowed: false
  reason_summary:
    - explicitly_parked_by_user
    - not_active_work_item
```

## Answer Calibration Integration

Answer calibration consumes freshness, contradiction, supersession, and staleness signals when calibrating factual responses.

Stale or contradicted memories may remain retrievable, but they should reduce answer confidence and may require explicit uncertainty disclosure.

Memory hygiene determines whether a memory is safe to retrieve or mention. Answer calibration determines how strongly the assistant may rely on that memory when making factual claims.

## Must not happen

The assistant must not:

- present stale observations as current reality
- keep old project plans active after they are parked
- ignore user corrections
- treat brainstorms as commitments without evidence
- delete durable memory when demotion is sufficient
- hide uncertainty about freshness when it matters

## Minimal tests

Implementation is sufficient when tests can prove:

1. Active context is preferred over parked or stale context for next-action answers.
2. Parked context may be mentioned as parked but not treated as active.
3. Corrected context supersedes older incorrect context.
4. Unknown freshness causes conservative wording when current state matters.
5. Trace summaries include freshness state without raw private memory content.
6. Fallback behavior does not claim stale state as current when freshness metadata is unavailable.

## Notes for later phases

Action decisions must treat stale/unknown system state conservatively.

Presence and commentary should not surface stale memories as casual observations.

Delegated tasks should record freshness for task inputs and assumptions.
