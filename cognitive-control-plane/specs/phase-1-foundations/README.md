# Phase 1 — Foundations

Phase 1 defines the first control-plane foundations for a memory-aware LLM system.

The phase focuses on the minimum infrastructure needed before adding companion behavior, runtime state, trust governance, or delegation:

- model routing
- artifact storage and retrieval
- memory tiering
- traceability and observability

## Included public specs

- `R01 — Policy-Based Model Router`
- `R02 — Artifact Storage and Retrieval`
- `R03 — Memory Tiering and Overlays`
- `R04 — Observability and Explainability Traces`

## Why this phase matters

Before an assistant can become durable or trustworthy, the system needs stable infrastructure boundaries:

- raw evidence must be stored separately from derived context
- model routing must be policy-aware rather than hardcoded
- retrieval must distinguish working, semantic, pinned, policy, and persona context
- traces must explain what context, artifacts, model, and routing decisions contributed to an answer

Phase 1 is intentionally infrastructure-first. It establishes the substrate that later phases build on.
