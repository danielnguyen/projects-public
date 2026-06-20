# Phase 3 — Grounding

Phase 3 establishes runtime state, scoped identity, current world state, and lightweight relationship context before broader trust, action, presence, or delegation behavior.

## Included public specs

- `R41 — Conversational Runtime State Engine`
- `R52 — Runtime World State Registry` (public excerpt)

## Why this phase matters

A long-lived assistant needs to distinguish:

- what is happening in the current conversation
- what it currently believes is true about the active world
- what it remembers durably
- which persona or role is active
- which entities and relationships are relevant

Without this grounding layer, the model tends to collapse everything into prompt-shaped context and can treat stale observations, durable memory, recent dialogue, and active state as if they were the same thing.
