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

## The value proposition

CCP's goal is not to add another chatbot interface. It is to make an AI assistant more dependable **over time and across the boundaries of any one model, provider, conversation, or device**. The underlying value comes from deciding which information is current, which claims deserve trust, and what actions the assistant is allowed to take — while keeping those decisions inspectable and under the system owner's control.

In practical terms, the intended advantages are:

| Need | CCP's approach | Benefit to the person using it |
| --- | --- | --- |
| Continuity beyond a chat window | Keep durable identity, memory, corrections, and current state outside disposable model context | Starting another conversation or changing interfaces should not require manually rebuilding the assistant's understanding. |
| Trustworthy long-term knowledge | Keep evidence, inference, contradictions, freshness, and accepted claims distinct | An outdated statement should not silently regain authority just because an old summary was retrieved. |
| Privacy and ownership | Govern access and select task-relevant context before sending it to a provider | Fewer unrelated records need to cross a model-provider boundary, while the durable source of truth can remain under the owner's control. |
| Consistency across surfaces | Reuse canonical identity and conversation ownership while applying surface-specific behavior policy | Chat, voice, and wearable clients can behave appropriately without becoming competing versions of the assistant. |
| Safer actions and delegation | Keep permission, confirmation, scope, and evidence rules outside the agent's own reasoning loop | An agent can choose how to work without deciding for itself whether it is authorized to act. |
| Freedom to change models | Treat hosted and local models as replaceable inference components | Model upgrades or provider changes need not force a migration of identity, long-term memory, and policy. |

These are **architectural goals and design contracts**, not a claim that every capability is already complete or that CCP has demonstrated superior real-world accuracy, latency, or operating cost.

### A concrete example: a corrected fact stays corrected

Suppose an assistant records that a service runs version A. Later, a deployment record establishes that it runs version B. Months afterward, an old conversation mentioning version A becomes relevant to a troubleshooting question.

A typical retrieval result might contain both statements and leave the model to infer which one is current. CCP's intended control plane instead preserves the evidence and lifecycle: version A was once valid, version B superseded it, and the assistant must distinguish historical context from current state. If the deployment evidence is missing or stale, it should qualify the conclusion rather than manufacture certainty.

The benefit is not that CCP has invented temporal memory. It is that **source validity, current-state authority, and the eventual answer are governed together** rather than relying solely on prompt instructions.

## Why compare complete systems, not product counts?

Existing products already address important parts of this problem. [Letta](https://docs.letta.com/concepts/stateful-agents) supports persistent agents across conversations; [Graphiti](https://www.getzep.com/platform/graphiti/) supports temporal facts and invalidation; [OpenClaw](https://docs.openclaw.ai/concepts/memory-architecture) documents provenance-aware memory controls; [LangGraph](https://github.com/langchain-ai/docs/blob/main/src/oss/langgraph/persistence.mdx) supports persistent and resumable agent execution. These are substantial capabilities, not trivial substitutes.

One way to build a comparable assistant is to assemble those kinds of components. Another is to define a common control plane whose storage, runtime, orchestration, and retrieval services share a consistent model of identity, claims, policy, and authority.

A collection of mature products can reduce the amount of infrastructure one must write or operate. But it can still require substantial integration to reconcile:

- what each component believes is the **current** user, conversation, or agent identity;
- which retrieved facts are merely candidates versus accepted knowledge;
- how privacy and source authority travel across product boundaries;
- who authorizes tool effects, reconciles uncertain outcomes, and stores the evidence; and
- how upgrades, state migration, failures, and changing providers affect continuity.

CCP's architectural bet is that **explicit shared contracts may be simpler to reason about than custom translations between several independently designed state and policy models**, even when the resulting CCP codebase contains multiple services. This is a whole-system complexity hypothesis, not a measured conclusion or a claim that commercial systems cannot satisfy the same requirements.

For a self-operated project, the right comparison includes **infrastructure, integration, correctness, and ongoing maintenance**, alongside usable features. A third-party stack may prove simpler or more economical; CCP should be evaluated against that complete alternative, not insulated from comparison.

## What would make the case convincing?

The architecture should earn its complexity through observed behavior. Meaningful evidence would include correct cross-session and cross-surface continuation, demonstrably respected corrections and stale-source boundaries, trustworthy action approvals, less irrelevant context disclosed to model providers, and acceptable operational cost and maintenance effort.

The aim is an assistant that is **useful to live with**, not an architecture that is only impressive to diagram. Performance and cost savings should be reported when measured against equivalent tasks — not inferred from service boundaries alone.

## Why I’m building CCP

I’m building CCP because I’m looking for a more precise way to handle memory in long-lived AI systems, while keeping that memory private and portable.

Many assistants treat recall partly as a salience problem, using signals such as recency, reinforcement, or inferred importance. Those might be useful, but I’m also interested in preserving the distinction between what is currently salient and what remains valid.

For example, a server might show a brief spike in errors that looks harmless on its own. But if similar spikes appeared before several earlier outages, the historical pattern changes how the new event should be interpreted.

I’m looking for a system that can retrieve those earlier incidents, connect them to the current one, and show why the pattern was considered relevant. It should preserve the difference between a single anomaly, a recurring pattern, and a conclusion that has not yet been established.

There are already services that offer persistent memory, integrations, and personal assistants. The trade-off is that the most sensitive part of the system, including memory, identity, history, and connected data, often becomes tied to that service.

CCP is my attempt to keep that data in systems I own, while allowing models and interfaces to change around it. I am not trying to rebuild every part of an assistant. I am trying to make sure the part that represents me remains private, inspectable, and mine.

## One durable agent, disposable context windows

CCP is intended to make the assistant’s continuity independent of any one chat thread, model session, or provider context window.

A chat thread is an interaction record and a useful local continuity scope. It is not the assistant’s memory container, identity boundary, or complete cognitive state. The product should feel like one durable relationship even when the implementation uses multiple threads, surfaces, providers, or model calls.

The intended model is:

```text
durable memory + current governed state + authorized sources
        ↓ retrieve what matters for this turn
bounded inference context
        ↓
replaceable model call
        ↓
new messages, claims, state, and evidence return to durable storage
```

A model context window is therefore a temporary **working set**, not the system of record. Context limits, summarization, token budgets, or provider quotas may change what is materialized for one inference call, but they must not silently become memory deletion or the authoritative definition of what the assistant knows.

When an older detail is omitted from one prompt, it should remain retrievable from durable state. When a later correction supersedes an earlier belief, retrieval should preserve that lifecycle rather than resurrecting a stale summary merely because it survived in a previous chat. Replacing a model or moving from web to voice should not require the user to manually resynchronize the assistant.

A useful shorthand for this direction is:

> **The model is transient. The relationship and cognitive state are durable.**

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

## More than retrieval

CCP includes many of the things people associate with advanced RAG: semantic retrieval, artifact search, ranking, context budgeting, freshness checks, provenance, and model routing. Those are important parts of the system, but they are not the whole system.

Retrieval mostly answers: **what should the model see for this request?** CCP also needs to answer what the system currently knows, whether that knowledge is still valid, which context belongs to this user and conversation, what may be disclosed, and what should be remembered or acted upon afterward.

A strong retrieval pipeline might find the right document. CCP still has to decide whether the document is stale, whether it conflicts with newer information, whether it is private, and whether a claim based on it has enough support to become durable memory or action authority.

That is why I think of retrieval as one layer inside CCP rather than the definition of CCP itself. The broader system also owns memory, runtime state, identity, policy, authority, and the evidence left behind after the model responds.

## Where agents fit

CCP is not designed as an agentic-first system, and that is intentional. Agents are useful when a task genuinely benefits from a model choosing its own sequence of steps: searching several sources, breaking down a problem, using tools, checking results, and adjusting along the way.

That does not mean every conversation needs to become an agent loop. A request might be handled by a single model response, a predictable workflow, or a bounded agent, depending on the work involved.

```text
user request
  -> single model response
  -> deterministic workflow
  -> bounded agent loop
```

When an agent is useful, it can decide what step to try next. CCP should still decide which capabilities exist, whether the agent may use them, when confirmation is required, what actually gets executed, how the result is verified, what information may leave the system, and how much time or cost the task is allowed to consume.

The architectural principle is:

> **CCP should govern agents; CCP does not need to become one giant agent.**

This means agent support can be added later without rebuilding the rest of the system around it. CCP would need additional concepts such as task state, step limits, pause and resume, cancellation, and agent-loop traces. Those are extensions to the orchestration layer. Durable memory, policy, authority, execution, and evidence can remain where they already belong.

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

Apache License 2.0
