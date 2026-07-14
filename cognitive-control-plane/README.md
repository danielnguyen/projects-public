# Cognitive Control Plane

Cognitive Control Plane (CCP) is a specification-first architecture for durable, governed, stateful LLM systems.

Its central value is **evidence-governed knowledge**: LLM output is treated as a fallible claim, not as authoritative truth. Generated claims should not become durable memory, world state, or action authority without traceable evidence, explicit validation, and governed lifecycle controls.

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

## Why I’m building CCP

I’m building CCP because I’m looking for a more precise way to handle memory in long-lived AI systems, while keeping that memory private and portable.

Many assistants treat recall partly as a salience problem, using signals such as recency, reinforcement, or inferred importance. Those might be useful, but I’m also interested in preserving the distinction between what is currently salient and what remains valid.

For example, a server might show a brief spike in errors that looks harmless on its own. But if similar spikes appeared before several earlier outages, the historical pattern changes how the new event should be interpreted.

I’m looking for a system that can retrieve those earlier incidents, connect them to the current one, and show why the pattern was considered relevant. It should preserve the difference between a single anomaly, a recurring pattern, and a conclusion that has not yet been established.

There are already services that offer persistent memory, integrations, and personal assistants. The trade-off is that the most sensitive part of the system, including memory, identity, history, and connected data, often becomes tied to that service.

CCP is my attempt to keep that data in systems I own, while allowing models and interfaces to change around it. I am not trying to rebuild every part of an assistant. I am trying to make sure the part that represents me remains private, inspectable, and mine.

## Evidence-governed knowledge

CCP separates three things that LLM products often collapse together:

- what the model generated
- what the available evidence supports
- what the system is permitted to treat as authoritative

Those are not the same object.

A generated claim must not acquire authority merely because it is fluent, repeated, recent, or convenient. The surrounding system should preserve provenance, uncertainty, and conflict; apply explicit acceptance rules; and support rejection, supersession, correction, replay, and audit.

This makes the LLM one replaceable producer and consumer of claims inside a governed knowledge system. The control plane—not the model—owns durability, policy, evidence, and the boundaries that determine when information may be trusted or acted upon.

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

## Broader than enhanced RAG

Parts of CCP overlap with enhanced retrieval-augmented generation:

- semantic and artifact retrieval
- candidate ranking and filtering
- context budgeting
- freshness and provenance handling
- policy-aware prompt assembly
- model and provider routing

Those capabilities form the system's information-acquisition layer, but they are not the whole system.

Enhanced RAG primarily asks: **what context should the model receive for this request?** CCP also asks:

- What does the system currently know, and what remains uncertain or disputed?
- Which information is durable, stale, superseded, private, or restricted?
- Which identity, relationship, conversation, and world-state context applies?
- Which model or provider is appropriate for the request?
- What is the system permitted to disclose, remember, recommend, or execute?
- What evidence and decision trace should survive after the response?

CCP therefore treats retrieval as one subsystem inside a broader governed cognitive runtime. Memory, policy, identity, authority, execution, and evidence remain outside the model and outside any single retrieval pipeline.

## Compatibility with agentic evolution

CCP is not designed as an agentic-first system. Agents are useful when a model needs to choose and revise a sequence of steps: searching several sources, decomposing a problem, invoking tools, checking results, or continuing until a bounded task is complete.

That kind of agency is an **execution strategy**, not the identity of the cognitive system itself.

A future CCP request could be routed through any of these modes:

```text
user request
  -> single model response
  -> deterministic workflow
  -> bounded agent loop
```

In an agent loop, the model may propose the next retrieval step or action. CCP should still retain control over:

- which capabilities exist
- whether the active persona and surface may use them
- risk and authority decisions
- privacy and disclosure limits
- confirmation requirements
- actual connector execution
- post-action verification
- evidence capture, replay, and audit
- step, token, time, and cost limits

The architectural principle is:

> **CCP should govern agents; CCP does not need to become one giant agent.**

This separation allows future agent runtimes to be added as another orchestration mode without replacing the existing foundations. Task state, step budgets, pause or resume behavior, cancellation, and agent-loop traces can be introduced later while durable memory, policy, authority, execution, and evidence remain stable control-plane responsibilities.

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

## Implementation repositories

CCP is implemented across four services:

- [Basic Memory Store (BMS)](https://github.com/danielnguyen/basic-memory-store) — durable memory, retrieval, provenance, and lifecycle
- [Chat Orchestrator (CO)](https://github.com/danielnguyen/chat-orchestrator) — request orchestration, prompt assembly, routing, and response governance
- [Cognitive Runtime (CR)](https://github.com/danielnguyen/cognitive-runtime) — runtime state, world state, companion policy, and behavioral governance
- [Data Source Aggregator (DSA)](https://github.com/danielnguyen/data-source-aggregator) — read-only access to structured personal and external data sources

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
