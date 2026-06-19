# R03 — Memory Tiering and Overlays

Type: **Infrastructure**  
Summary: Split memory into working, semantic, pinned, policy, and persona layers with explicit retrieval rules and guardrails.

## Goals

- Prevent all context from collapsing into one undifferentiated prompt blob.
- Give each memory/context tier a clear role and retrieval policy.
- Support stable behavior across surfaces and modes.
- Make prompt assembly inspectable and bounded.

## Non-goals

- Treating every tier as canonical memory.
- Storing everything forever.
- Requiring every surface to receive the same context.
- Replacing later memory hygiene, provenance, or privacy policy.

## Memory Tiers

### Working memory

Recent turns in the active conversation. Usually exact text or structured turn records.

Purpose:

- immediate continuity
- resolving short references like "yes", "that", "continue", or "what about this"
- preserving local turn context

### Semantic memory

Retrieved results from vector or lexical search over durable memory and derived artifact text.

Purpose:

- recall relevant prior information
- support long-horizon continuity
- recover useful context outside the current conversation window

### Pinned memory

Curated facts, decisions, or constraints explicitly promoted for stable availability.

Purpose:

- stable user preferences
- important project decisions
- durable operating constraints

### Policy memory

Rules and constraints that affect behavior.

Purpose:

- safety policy
- privacy constraints
- routing constraints
- surface-specific restrictions

### Persona overlay

Optional behavior/profile context scoped by surface, mode, and runtime policy.

Purpose:

- tone and response shape
- interaction posture
- companion behavior

Persona overlays are not canonical user memory by themselves.

## Retrieval Algorithm

Suggested order:

1. Working: last N messages, with N configurable by surface.
2. Pinned: top K pinned items relevant to request.
3. Semantic: vector/lexical search over messages and derived artifact text.
4. Apply time-aware reranking if available.
5. Compose context with hard token budgets per tier.
6. Attach retrieval metadata and trace references.

## Guardrails

- Do not allow semantic recall to silently override pinned constraints.
- Do not allow persona overlays to invent durable preferences.
- Do not allow policy context to become hidden prompt magic.
- Do not overfill prompts with low-relevance semantic results.
- Do not treat old retrieved memories as current reality without freshness signals.

## Contract Shape

Illustrative prompt assembly object:

```yaml
context_bundle:
  working_memory:
    source: recent_turns
    budget_tokens: 1200
  pinned_memory:
    source: curated_memory
    budget_tokens: 800
  semantic_memory:
    source: retrieval
    budget_tokens: 1600
  policy_overlay:
    source: runtime_policy
    budget_tokens: 600
  persona_overlay:
    source: scoped_profile
    budget_tokens: 500
```

## Observability

Trace:

- which tiers were queried
- which items were selected
- which items were suppressed
- token budget per tier
- surface-specific limits
- fallback behavior when a tier is unavailable

## Evaluation

Test for:

- stable prompt assembly under token pressure
- pinned constraints surviving semantic noise
- working memory preserving local continuity
- persona/policy overlays remaining scoped
- semantic retrieval not overfitting to stale context
