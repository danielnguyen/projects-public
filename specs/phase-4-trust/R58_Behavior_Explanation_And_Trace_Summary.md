# R58 — Behavior Explanation and Trace Summary

## User capability

The assistant can explain why it answered a certain way without exposing raw prompts, raw private memory, or hidden reasoning.

Example:

```text
User: Why were you so brief?
Assistant: I answered briefly because the active surface was glasses, the request was a status check, and no high-detail explanation was needed.
```

## Problem

As runtime governance increases, the assistant needs inspectability. Without summarized explanation, behavior can feel arbitrary or opaque.

The system needs to explain:

- why response shape changed
- why humor/commentary was suppressed
- why an action was blocked
- why a persona scope was selected
- why stale context was not used as current
- why sensitive details were summarized

## MVP behavior

The runtime should emit summarized trace information for governance decisions.

Minimum trace summary fields:

- `interaction_kind`
- `response_posture`
- `active_persona_id`
- `freshness_summary`
- `privacy_zone`
- `commentary_allowed`
- `action_allowed`
- `blocked_reason_summary`
- `source_summary`

The trace summary must be user-safe and implementation-safe.

## Runtime ownership

The runtime owns governance trace summaries.

The orchestrator may expose summaries in responses when the user asks why behavior occurred.

The memory substrate may provide provenance summaries but does not expose raw memory content by default.

## Contract shape

Illustrative object:

```yaml
behavior_trace_summary:
  interaction_kind: status_check
  response_posture: brief
  active_persona_id: operations_assistant
  freshness_summary: active_project_context
  privacy_zone: glasses_public_or_semi_public
  commentary_allowed: false
  action_allowed: false
  blocked_reason_summary:
    - surface_prefers_brief
    - status_request_no_action_needed
```

## Must not happen

The assistant must not:

- expose hidden chain-of-thought
- expose raw prompt layers
- expose raw private memory content
- expose raw reviewed/original answer text from overlays
- leak implementation/meta planning identifiers into user-facing traces
- fabricate explanations for behavior that was not actually governed

## Minimal tests

Implementation is sufficient when tests can prove:

1. A brief response can produce a safe summary explaining brevity.
2. A blocked action can produce a safe reason summary.
3. Privacy redaction can produce a safe reason summary without exposing suppressed content.
4. Trace summaries do not include raw prompt text or raw memory content.
5. Trace summaries avoid implementation planning identifiers.
6. Fallback behavior does not invent unavailable diagnostic details.

## Calibration Trace Integration

When answer calibration is active, behavior explanations may include summarized calibration information.

Examples:

- evidence level used
- claim classification
- confidence level
- uncertainty disclosures emitted
- quantitative claim restrictions applied

Behavior explanations should summarize calibration decisions without exposing hidden prompts or internal chain-of-thought reasoning.
