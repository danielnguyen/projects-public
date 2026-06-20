# R41 — Conversational Runtime State Engine

Type: **Runtime / Live Conversation State**  
Summary: Defines the canonical live conversation state model used by chat, voice-mediated surfaces, and native realtime voice without turning transient interaction state into canonical memory.

## Goals

- Provide a shared runtime state model for live conversation beyond single request/response turns.
- Represent session, turn, attention, interruption, pause, resume, continuation, and idle state explicitly.
- Keep transient runtime state separate from canonical memory and derived memory.
- Give timing, intent arbitration, restraint, presence, pacing, and native voice policies a common substrate.
- Make runtime decisions inspectable through traces and replay fixtures.

## Non-goals

- Replacing canonical memory, retrieval, or provenance doctrine.
- Creating hidden prompt-state that cannot be inspected or replayed.
- Persisting every transient conversational signal as durable memory.
- Defining native audio capture, VAD, STT, TTS, or playback behavior.
- Defining bounded persona scope.

## Proposed Architecture

### Runtime state layers

- **Session state**: identifies the current live interaction session, surface, active mode, and continuity boundary.
- **Turn state**: tracks the current turn lifecycle such as `received`, `classifying`, `retrieving`, `responding`, `paused`, `interrupted`, `completed`, or `abandoned`.
- **Attention state**: captures whether the user appears active, idle, driving, multitasking, returning after a gap, or low-attention.
- **Continuation state**: records whether the system has pending unsaid, deferred, or expandable content.
- **Policy state**: records active runtime decisions from intent arbitration, timing, restraint, and surface shaping.

### State ownership

R41 defines runtime state contracts, not every implementation detail. A dedicated runtime service may own the state, while the orchestrator consumes it through explicit overlays and traceable request metadata.

### Relationship to memory

Runtime state should not introduce hidden prompt variables or duplicate state ownership inside prompt templates. Conversation state is transient interaction state. It is not canonical memory.

## Data Model

### `conversation_runtime_sessions`

- `runtime_session_id`
- `owner_id`
- `conversation_id`
- `surface`
- `surface_session_id`
- `status` (`opening`, `active`, `paused`, `idle`, `closing`, `closed`)
- `active_mode`
- `attention_state`
- `started_at`
- `last_activity_at`
- `closed_at`

### `conversation_runtime_turns`

- `runtime_turn_id`
- `runtime_session_id`
- `input_message_id`
- `turn_status`
- `intent_class`
- `timing_policy`
- `restraint_policy`
- `continuation_state`
- `created_at`
- `updated_at`

### `conversation_runtime_events`

- `event_id`
- `runtime_session_id`
- `runtime_turn_id`
- `event_type`
- `event_payload_json`
- `created_at`

## API Surface

Possible internal endpoints:

- `POST /v1/runtime/sessions/resolve`
- `POST /v1/runtime/turns/start`
- `POST /v1/runtime/turns/update`
- `POST /v1/runtime/turns/complete`
- `GET /v1/runtime/sessions/{runtime_session_id}`

External surfaces should continue using the normal chat/orchestration entrypoint unless native runtime integration is required.

## Observability

Emit:

- runtime session resolution traces
- turn state transitions
- attention state changes
- continuation state creation and completion
- policy decisions applied to each turn
- runtime state included in prompt assembly overlays
- replay fixtures for representative live conversation sessions

## Evaluation

Test for:

- stable state transitions across normal, interrupted, and resumed turns
- no leakage of transient state into canonical memory
- replayability of runtime decisions
- consistent behavior across chat, voice, and surface-mediated adapters
- compatibility with future realtime voice state
- compatibility with scoped runtime identity

## Risks / Open Questions

- Runtime state may duplicate existing conversation or trace state if ownership is unclear.
- Over-persisting transient signals may create misleading memory artifacts.
- Under-specifying state transitions may lead to hidden prompt behavior.
- Native voice may require additional low-latency state paths that should remain compatible with this model.
