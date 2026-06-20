# R04 — Observability and Explainability Traces

Type: **Infrastructure**  
Summary: End-to-end tracing for retrieval, routing, model calls, artifact use, cost, and latency.

## Goals

- Make runtime behavior inspectable before additional complexity is layered on.
- Record enough information to debug retrieval, routing, artifact use, and model behavior.
- Support user-safe explanations later without exposing raw private context or hidden reasoning.
- Preserve cost and latency visibility as model routing becomes dynamic.

## Non-goals

- Exposing raw internal traces directly to users.
- Capturing hidden chain-of-thought.
- Logging sensitive data without redaction policy.
- Replacing product-level behavior explanations.

## Trace Scope

A useful trace should capture:

- `request_id`
- `conversation_id`
- `surface`
- retrieval queries, ids, scores, and tiers
- selected model and router rule
- fallback candidates
- model calls, provider, latency, token counts, and errors
- cost estimate when available
- artifacts used and derivations referenced
- policy decisions that materially changed behavior

## Trace Shape

Illustrative object:

```yaml
trace:
  request_id: req_123
  conversation_id: conv_456
  surface: telegram_private
  retrieval:
    mode: tiered
    tiers_used:
      - working
      - pinned
      - semantic
    selected_item_ids:
      - memory_001
      - artifact_text_002
  router:
    selected_model: model_name
    rule_id: balanced_private_text
    fallbacks:
      - local_small
  model_calls:
    - provider: llm_gateway
      latency_ms: 850
      input_tokens: 1200
      output_tokens: 280
  cost:
    estimated_usd: 0.0021
  artifacts_used:
    - artifact_123
```

## Redaction

Traces should distinguish:

- full internal debug traces
- implementation-safe traces
- user-safe explanation summaries

Sensitive source text, raw prompts, raw memory, and private artifacts should not be exposed by default.

## Debug Surface

Possible future surfaces:

- `/traces/:request_id` endpoint
- internal debug page
- developer CLI
- user-facing "why did you answer like that?" summary built from safe trace fields

## Observability Events

Emit structured logs for:

- retrieval
- routing
- model calls
- cost
- latency
- artifact derivation
- fallback events
- policy suppression
- redaction decisions

## Evaluation

Test for:

- trace creation for normal requests
- trace creation for fallback paths
- retrieval and routing decisions visible in traces
- sensitive data redacted from user-safe summaries
- no hidden reasoning exposure
- enough metadata to reproduce or diagnose surprising outputs
