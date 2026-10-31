# Cognitive Control Plane

Imagine an AI assistant that remembers the decisions you've made together, the reasons behind them, and the evidence that changed your mind. You can pick up a conversation months later, move from your desk to your phone, or try a newer model while carrying that understanding forward.

**That's the experience I want to build.**

Cognitive Control Plane (CCP) is my exploration of a durable foundation for a personal AI companion: one that can develop useful context over time, distinguish established knowledge from emerging conclusions, and act within boundaries that its owner understands and controls.

> **The model is transient. The relationship and cognitive state are durable.**

## Why I’m building CCP

As AI becomes more capable, I want it to become more dependable in the parts of life and work that unfold over months and years.

A long-running project accumulates decisions, experiments, reversals, and lessons. A personal assistant accumulates context about what matters to its owner, which information is sensitive, and how circumstances evolve. I want an assistant that can carry those histories forward and use them thoughtfully.

Consider an engineering incident: a brief spike in errors might look harmless by itself. But the same pattern may have appeared before earlier outages. A useful assistant should be able to surface those incidents, connect their evidence to the current situation, and explain the strength and limits of the connection. Months later, it should remember the conclusion we reached and incorporate what we've learned since.

That quality of continuity is what motivates me. I want to be able to ask *“Why did we make that decision?”* and receive an answer grounded in the actual history. I want corrections to endure. I want an assistant that can help me think more clearly because it understands the path we've taken.

I also want that context to remain mine. Memory, identity, history, and connected sources are worth protecting. By keeping them in infrastructure I control, I can choose what to share with a model, change providers as the technology advances, and inspect how the assistant arrived at a conclusion.

## What CCP is designed to make possible

**Continuity that grows with use.** Conversations, decisions, and relevant context form a durable history. Starting a new session or changing devices should feel like continuing the same work.

**Memory with an evidence trail.** Facts, observations, inferences, and corrections retain their sources and lifecycle. The assistant can explain what supports a conclusion, what changed, and where uncertainty remains.

**One companion across many surfaces.** Chat, voice, wearables, and other interfaces can share an identity and history while adapting how and when they communicate. The same companion might offer a detailed explanation at a desk and a brief, timely update while on the move.

**Privacy and ownership.** The system selects relevant context and applies disclosure policy before sending information to an inference provider. The owner retains control over durable state and the ability to choose local or hosted models.

**Freedom to evolve.** Models, interfaces, and agent frameworks will keep improving. CCP aims to preserve continuity while allowing those pieces to be replaced or upgraded independently.

**Useful autonomy with clear authority.** Agents can research, plan, and execute bounded tasks. A governing runtime keeps approval, permissions, evidence, and resource limits tied to the owner's intent.

These capabilities describe the direction of the project. Their implementation and acceptance advance through explicit specifications, tests, and composed-system evidence.

## A corrected fact should stay corrected

Imagine that an assistant remembers an application running version A. A deployment record later establishes that it has moved to version B. Several months later, an older discussion about version A is retrieved during troubleshooting.

The assistant should understand the sequence: version A describes the past; the deployment record supports version B as the newer state. It can still use the older discussion to explain historical decisions while grounding its current recommendation in the newer evidence.

This is the idea behind **evidence-governed knowledge**. CCP treats generated statements as claims that can be checked against their sources and accepted through a defined lifecycle. Provenance, freshness, conflicts, correction, and supersession become part of the assistant's lasting understanding.

The result I am working toward is a companion that learns with discipline: retaining the useful history and remaining open to better evidence.

## One durable agent, disposable context windows

Each model call receives a focused working set assembled from the system's durable state.

```text
durable memory + current governed state + authorized sources
        ↓ retrieve what matters for this turn
bounded inference context
        ↓
replaceable model call
        ↓
new messages, claims, state, and evidence return to durable storage
```

Conversation threads preserve interaction history and immediate context. The durable system carries identity, accepted knowledge, current state, corrections, and source-backed conclusions across those threads.

This separation makes it possible to select a smaller context for an ordinary request while keeping the broader history available for later retrieval. A change of provider or interface can draw on the same governed state.

## Evidence-governed knowledge

CCP keeps distinct records of:

- what the model proposed or inferred;
- what the available sources and observations support;
- what the system has accepted as current, usable knowledge; and
- which actions that knowledge is sufficient to authorize.

The surrounding services own the rules for accepting, correcting, superseding, and retrieving claims. That makes the history inspectable and allows the assistant's conclusions to improve as the evidence evolves.

For me, this is the foundation of trust: an assistant capable of showing its work and updating its understanding.

## Architecture thesis

CCP separates responsibilities so they can evolve behind shared contracts:

```text
surface input
  -> chat orchestration
  -> runtime and interaction policy
  -> governed memory and world state
  -> evidence and source access
  -> context selection and model reasoning
  -> calibrated response and traceable outcomes
```

- **Chat Orchestrator** coordinates requests, context assembly, model selection, and response delivery.
- **Cognitive Runtime** governs interaction state, identity, behavior, capability permissions, and action policy.
- **Basic Memory Store** retains conversations, artifacts, sources, and the lifecycle of durable knowledge.
- **Data Source Aggregator** connects read-only sources under explicit retrieval and scope rules.
- **Model providers and agent harnesses** contribute reasoning and task execution through replaceable interfaces.

The goal is a coherent control plane whose services agree on identity, evidence, state, and authority.

### Why the whole system matters

There is impressive work already happening across this field. [Letta](https://docs.letta.com/concepts/stateful-agents) explores persistent agents and memory; [Graphiti](https://www.getzep.com/platform/graphiti/) models how facts and relationships evolve over time; [OpenClaw](https://docs.openclaw.ai/concepts/memory-architecture) brings together multi-channel assistants and provenance-aware memory controls; and [LangGraph](https://github.com/langchain-ai/docs/blob/main/src/oss/langgraph/persistence.mdx) provides durable, resumable agent workflows.

These projects demonstrate how much of the foundation is becoming reusable. They also highlight the challenge that interests me most: making memory, identity, policy, and execution work together as **one dependable system**.

A complete assistant may combine several mature products. Those components still need to agree on whose data they hold, which facts are current, what information can cross a boundary, and who can authorize an action. Integrating those shared responsibilities is itself an architectural undertaking.

CCP is a bet that clearly defined ownership and shared contracts can make that full system easier to understand, adapt, and govern. I'm evaluating that bet against complete alternatives, including their infrastructure, integration work, correctness, and long-term maintenance.

## More than retrieval

Retrieval brings relevant information into view. CCP connects that information to durable memory, current-world understanding, source authority, sensitivity, and the purpose of the present request.

A document may be relevant while describing an earlier version of reality. A personal memory may be important while remaining inappropriate to disclose on a particular interface. A recommendation may be plausible while its evidence is still incomplete.

CCP's runtime is designed to carry these distinctions through context assembly, reasoning, and response generation. The goal is an assistant that can make good use of what it knows, with a clear account of why that knowledge applies now.

## Where agents fit

Some tasks need a quick answer. Others follow a predictable series of steps. Complex work may benefit from an agent that explores options, uses tools, checks results, and revises its plan.

```text
user request
  -> single model response
  -> deterministic workflow
  -> bounded agent loop
```

CCP's intended delegation model lets an agent harness handle the mechanics of execution while the control plane governs the task's purpose, permissions, resource limits, evidence, and completion criteria.

The principle is simple:

> **CCP governs agents. The harnesses do the work.**

This creates room to adopt better agent technology as it emerges while keeping the owner's authority and the companion's continuity in one place.

## What success looks like

I want CCP to become an assistant I can genuinely rely on in everyday use: one that recalls the right context, recognizes when information has changed, follows conversations across surfaces, respects attention and privacy, and carries useful work forward with appropriate safeguards.

I also want the engineering choices to earn their keep. That means evaluating real tasks for response quality, evidence accuracy, continuity, latency, provider costs, and maintenance effort. A well-designed system should be rewarding to use and sustainable to operate.

This repository shares the architecture, representative specifications, and lessons behind that journey.

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

This public repository is a curated publication of CCP's architectural direction and representative artifacts. Active implementation, deployment configuration, and detailed backlog management remain in the working repositories. The design is evolving through ongoing implementation, tests, and independent conformance review.

## License

Apache License 2.0
