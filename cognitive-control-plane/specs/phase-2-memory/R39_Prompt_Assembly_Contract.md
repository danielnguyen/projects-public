# R39 — Prompt Assembly Contract

Type: **Runtime Infrastructure / Context Management**  
Summary: Defines deterministic rules for assembling prompts from memory, retrieval, overlays, profiles, runtime state, and user messages.

## Motivation

As the system evolves, prompt assembly becomes a hidden operating system.

Without explicit contracts:

- precedence becomes inconsistent
- suppression becomes accidental
- retrieval injection becomes unstable
- runtime behavior drifts implicitly
- debugging becomes extremely difficult

R39 formalizes prompt assembly as inspectable infrastructure.

## Goals

- Define deterministic prompt assembly behavior.
- Standardize precedence and ordering.
- Preserve provenance visibility.
- Prevent hidden prompt drift.
- Support future runtime complexity safely.

## Non-goals

- Defining model-specific prompt wording.
- Locking the system into a single prompt format.
- Preventing additive runtime overlays.

## Core principles

Prompt assembly must be:

- deterministic
- inspectable
- bounded
- provenance-aware
- override-safe
- debuggable

## Prompt layers

Recommended layers:

1. system doctrine
2. interaction contract
3. profile overlays
4. runtime state overlays
5. retrieval augmentation
6. recent conversation history
7. current user turn
8. tool/runtime annotations

## Ordering invariants

- higher-order doctrine must not be silently overridden by retrieval text
- retrieval augmentation must remain attributable
- runtime overlays must be visible in traces
- user input remains the final conversational turn

## Retrieval injection rules

The contract should define:

- dedupe behavior
- retrieval truncation behavior
- snippet attribution
- retrieval ordering
- augmentation suppression
- provenance annotations

## Token budgeting

The system should support:

- bounded retrieval budgets
- bounded history budgets
- profile-aware truncation
- graceful degradation
- visibility into dropped context

## Traceability

Prompt assembly traces should expose:

- included layers
- excluded layers
- truncation reasons
- retrieval provenance
- token allocation
- runtime overlays applied

## Evaluation

Test for:

- deterministic assembly
- stable ordering
- retrieval provenance visibility
- bounded token growth
- no hidden override paths

## Runtime overlay extension

Runtime overlays must be provided by a runtime owner or explicitly named runtime adapter. They must not be hidden string concatenation inside orchestration logic.

Trace metadata should record:

- whether runtime overlay resolution was attempted
- runtime state identifier, when available
- runtime overlay identifier, when available
- whether the runtime overlay was included or omitted
- omission or failure reason
- final prompt layer order

This contract does not define companion identity, relationship continuity, or full mode adaptation behavior. It only defines how runtime overlays become inspectable prompt assembly inputs.
