# Cognitive Control Plane

Cognitive Control Plane (CCP) is a specification-first architecture for durable, governed, stateful LLM systems.

The project focuses on problems that appear after a chatbot becomes a long-lived system:

- model routing and fallback
- artifact storage and retrieval
- memory governance
- retrieval hardening
- conversational state
- current world-state modeling
- memory hygiene and staleness
- privacy and surface sensitivity
- behavior traceability
- answer calibration
- delegated work and action boundaries

The core idea is that useful long-lived AI systems are not only a model. They need explicit runtime state, governed memory, durable contracts, surface-aware policy, and traceable decision boundaries around the model.

## Architecture thesis

Most LLM prototypes collapse too much into prompt text:

```text
user message + chat history + retrieved context + model = answer
```

CCP separates those concerns:

```text
surface input
  -> orchestration
  -> runtime state
  -> world state
  -> memory/retrieval
  -> governance policy
  -> calibrated response
  -> traceable output
```

This lets the model remain replaceable while the surrounding system owns durability, policy, privacy, evidence, and behavioral consistency.

## Repository map

```text
architecture/
  diagrams and system overview

specs/
  phase-1-foundations/
  phase-2-memory/
  phase-3-grounding/
  phase-4-trust/

examples/
  failure-mode examples showing why the specs exist

agent-workflow/
  rules for AI coding agents and spec-driven implementation
```

## Starting points

Recommended first reads:

1. [`lessons-learned.md`](lessons-learned.md)
2. [`architecture/overview.md`](architecture/overview.md)
3. [`specs/phase-1-foundations/README.md`](specs/phase-1-foundations/README.md)
4. [`specs/phase-2-memory/R34_Memory_Doctrine_And_Invariants.md`](specs/phase-2-memory/R34_Memory_Doctrine_And_Invariants.md)
5. [`specs/phase-2-memory/R39_Prompt_Assembly_Contract.md`](specs/phase-2-memory/R39_Prompt_Assembly_Contract.md)
6. [`specs/phase-3-grounding/R41_Conversational_Runtime_State_Engine.md`](specs/phase-3-grounding/R41_Conversational_Runtime_State_Engine.md)
7. [`specs/phase-4-trust/R43_Conversational_Intent_Arbitration.md`](specs/phase-4-trust/R43_Conversational_Intent_Arbitration.md)
8. [`examples/dialogue-act-confirmation.md`](examples/dialogue-act-confirmation.md)
9. [`specs/phase-4-trust/R71_Answer_Calibration_And_Evidence_Grounding.md`](specs/phase-4-trust/R71_Answer_Calibration_And_Evidence_Grounding.md)

## Status

This public repository is a curated publication channel. It contains settled or representative artifacts from a larger private working repository. It is not a full mirror of active implementation history, deployment configuration, backlog planning, or private operating context.

## License

Apache License 2.0.
