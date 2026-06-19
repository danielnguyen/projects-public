# Example — Dialogue-Act Confirmation Failure

This example illustrates why long-lived conversational systems need explicit runtime state and intent arbitration.

## Interaction

```text
User: I'd like something similar to Dopapen.

Assistant: Do you mean a digital stylus like the Dopapen for touchscreen devices?

User: No, Dopapen Coffee in Markham.

Assistant: Do you want recommendations for coffee shops in Markham similar to Dopapen Coffee?

User: yes

Assistant: You're unsure about something. Can you clarify what's unclear?
```

## Observed failure

The assistant repaired the entity correctly after clarification:

```text
Dopapen -> Dopapen Coffee in Markham
```

It then asked a valid confirmation question:

```text
Do you want recommendations for coffee shops in Markham similar to Dopapen Coffee?
```

But the next user turn:

```text
yes
```

was not resolved against the prior assistant question. The system treated it as an underspecified standalone request and triggered a clarification fallback.

## Root cause

The word `yes` has little standalone semantic content. Its meaning depends on recent dialogue state.

The runtime should have preserved an active pending question:

```yaml
pending_question:
  type: boolean_confirmation
  proposition: recommend coffee shops in Markham similar to Dopapen Coffee
```

Then the next `yes` should resolve into that proposition.

## Relevant specs

- `R41 — Conversational Runtime State Engine`
- `R43 — Conversational Intent Arbitration`

## Required behavior

A confirmation response must be classified against recent runtime state before ordinary query interpretation.

Expected resolution:

```yaml
intent_class: confirmation_response
resolved_against:
  prior_turn: assistant_confirmation_question
  proposition: recommend coffee shops in Markham similar to Dopapen Coffee
response_policy:
  answer_confirmed_request: true
  clarification_required: false
```

## Why this matters

This is not a knowledge failure. The system eventually produced a reasonable recommendation list once the full request was restated.

The failure was in the bridge between dialogue state and response generation.

That is the kind of failure that appears in everyday use and slowly erodes trust, especially in long-lived assistants where short replies, corrections, confirmations, and continuations are common.
