# R43 — Conversational Intent Arbitration

Type: **Runtime / Conversation Policy Selection**  
Summary: Classifies the kind of conversational turn before response generation so the system can choose whether to answer, clarify, store, suppress, confirm, defer, or route to a surface action.

## Goals

- Prevent the system from treating every utterance as a request for a full answer.
- Classify live turns into actionable runtime intent categories.
- Make mode, timing, retrieval, restraint, and surface behavior selection traceable.
- Support chat, voice-mediated surfaces, and native realtime voice consistently.
- Provide a bridge between runtime state, timing, restraint, and prompt assembly.

## Non-goals

- Replacing model reasoning or final response generation.
- Inferring durable user preferences without memory promotion policy.
- Creating hidden emotional or relationship state.
- Performing high-impact actions without explicit authorization.
- Defining voice audio transport.

## Runtime ownership

The runtime owns conversational intent arbitration.

The orchestrator and downstream layers may consume intent-arbitration outputs, but they do not own the arbitration decision itself.

The runtime may persist intent classification in runtime-turn state and summarized runtime events. The memory substrate may retain downstream traces or retrieval artifacts where appropriate, but it does not define or own intent-arbitration policy.

## MVP ownership and implementation shape

For an MVP, conversational intent arbitration may be implemented within a broader interaction-governance evaluation path rather than as a separate standalone service.

A separate `/v1/runtime/intent/arbitrate` endpoint or separate arbitration service is not mandatory for MVP.

Folded implementation is acceptable only if all of the following remain true:

- explicit intent classification is available before response generation
- downstream retrieval, timing, restraint, confirmation, and surface effects remain traceable
- intent classification remains attributable to runtime ownership rather than becoming prompt-only shaping

## Proposed Architecture

### Intent classes

Initial intent classes:

- `information_request`
- `action_command`
- `confirmation_response`
- `correction`
- `clarification_request`
- `continuation`
- `interruption`
- `topic_shift`
- `venting_signal`
- `context_update`
- `memory_candidate`
- `surface_action`
- `low_confidence_unclear`

### Arbitration flow

1. Receive normalized user input and surface metadata.
2. Resolve current runtime session and turn state.
3. Classify conversational intent with confidence and evidence.
4. Select downstream policy hints for retrieval, timing, restraint, and surface shaping.
5. Emit traceable arbitration metadata into prompt assembly and response traces.

### Policy hints

Intent arbitration may emit:

- `retrieval_policy`
- `response_policy`
- `timing_policy`
- `restraint_policy`
- `surface_policy`
- `memory_promotion_hint`
- `action_confirmation_required`

## Data Model

MVP-acceptable persistence may use:

- `conversation_runtime_turns.intent_class`
- summarized runtime governance or intent events that preserve downstream decision traceability without exposing raw private context

Dedicated intent-arbitration tables are optional future-state persistence for replay, analytics, auditing, or deeper evaluation. They are not mandatory MVP schema.

### `conversation_intent_arbitrations`

- `arbitration_id`
- `runtime_turn_id`
- `owner_id`
- `surface`
- `intent_class`
- `confidence`
- `evidence_json`
- `policy_hints_json`
- `created_at`

### `conversation_intent_policy_decisions`

- `decision_id`
- `arbitration_id`
- `policy_name`
- `policy_value`
- `reason`
- `created_at`

## API Surface

Possible internal endpoint:

- `POST /v1/runtime/intent/arbitrate`

For MVP, this endpoint is illustrative rather than mandatory. The same capability may be implemented inside a broader governance response so long as explicit intent classification still occurs before response generation and downstream policy effects remain traceable.

Input should include:

- `owner_id`
- `conversation_id`
- `runtime_session_id`
- `runtime_turn_id`
- `surface`
- `user_text`
- `surface_metadata_json`
- `recent_runtime_state_json`

Output should include:

- `intent_class`
- `confidence`
- `policy_hints_json`
- `trace_ref`

When MVP implementation is folded into a broader governance response, the system must still preserve an explicit, inspectable intent-classification outcome rather than reducing arbitration to freeform prompt text.

## Observability

Emit:

- intent class distribution by surface
- low-confidence arbitration rate
- overridden intent decisions
- retrieval skipped due to arbitration
- clarification chosen due to ambiguity
- command confirmation required vs skipped
- arbitration contribution to final prompt assembly

## Evaluation

Test for:

- accurate distinction between request, command, correction, continuation, and interruption
- preservation of clarification and confirmation-relevant distinctions needed for downstream policy behavior
- reduced over-answering on venting or context-update turns
- correct confirmation behavior for action commands
- stable arbitration across surfaces
- traceability of downstream response policy decisions

## Risks / Open Questions

- Over-classification may make simple chat feel mechanical.
- Low-confidence arbitration could suppress useful answers.
- Intent classes may drift unless backed by fixtures and traces.
- Some turns may require multiple simultaneous intent labels.
