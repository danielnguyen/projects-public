# R40 — Runtime State Model

Type: **Runtime Architecture / Cognitive State**  
Summary: Defines explicit runtime state management for the companion operating layer.

## Motivation

As companion behavior becomes more adaptive, systems often drift into implicit state hidden in prompts and transient runtime heuristics.

Without explicit runtime state:

- behavior becomes inconsistent
- mode transitions become opaque
- interruption handling becomes unstable
- debugging becomes extremely difficult

R40 establishes inspectable runtime-state infrastructure.

## Goals

- Define explicit runtime state.
- Prevent hidden prompt-state accumulation.
- Standardize mode transitions.
- Support interruption and scene handling.
- Preserve inspectability.

## Non-goals

- Simulating consciousness.
- Persistent emotional modeling.
- Autonomous personality drift.
- Replacing canonical memory.

## Runtime state categories

Recommended categories:

- active scene
- interaction mode
- attention focus
- interruption context
- temporary task state
- active briefing state
- pending follow-through state

## State principles

Runtime state should be:

- temporary by default
- bounded in scope
- explicitly resettable
- inspectable
- traceable
- separable from canonical memory

## Persistence rules

Runtime state:

- may persist across turns
- should not automatically become long-term memory
- may be promoted through explicit memory mechanisms
- must remain distinguishable from canonical memory

## Scene model

Scenes may include:

- driving
- briefing
- coding
- planning
- interruption-sensitive interaction
- voice-first interaction

Scene changes should be traceable.

## Mode transitions

The runtime should support:

- explicit transitions
- inferred transitions
- interruption-aware transitions
- cooldown/reset behavior
- transition trace visibility

## Attention handling

The runtime may track:

- active topic focus
- unresolved tasks
- pending callbacks
- interruption resumability

Attention state should remain bounded and resettable.

## Observability

Trace:

- active runtime state
- scene transitions
- mode transitions
- interruption handling
- runtime overlay generation
- state resets

## Evaluation

Test for:

- stable transitions
- bounded runtime growth
- interruption recovery
- deterministic reset behavior
- no hidden state leakage

## Boundary

R40 defines the boundary between temporary runtime state, prompt assembly, derived memory, and canonical memory.

A minimal runtime-state envelope may include owner/session/conversation identity, active scene or mode, temporary task state, reset semantics, and trace references. This envelope must remain distinguishable from canonical memory and must not silently promote itself into long-term memory.

Runtime state and runtime overlay generation should be owned by the runtime layer. The orchestrator may consume the overlay, include it in prompt assembly, and trace its participation, but it should not become the hidden owner of runtime state.

This keeps runtime state explicit, inspectable, resettable, and separable from canonical memory before behavior becomes adaptive.
