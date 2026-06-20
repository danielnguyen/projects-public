# R52 — Runtime World State Registry

Type: **Runtime / World State / Active Reality Model**  
Summary: Defines a structured, inspectable, low-latency registry for what the assistant currently believes is true about the active world, task, environment, entities, tools, and external systems without confusing transient state with canonical memory.

## Objective

Runtime World State is the assistant runtime's structured model of current reality as last observed, verified, inferred, or supplied by trusted systems.

It is not reality itself. It is a provisional, traceable representation of what appears to be true now, with freshness, provenance, confidence, and expiry attached to every meaningful claim.

Core doctrine:

```text
Memory records what may matter later.
Conversation state tracks the interaction.
World state represents what is currently believed true.
Events update world state.
Personas govern access and behavior.
Artifacts provide durable references.
Traces explain why state was trusted, ignored, or refreshed.
```

## Problem statement

Without explicit world state, the runtime can collapse several different concepts into one prompt-shaped blob:

- durable memory
- recent conversation
- stale observations
- current environment facts
- inferred task state
- pending actions
- external system status
- persona or surface defaults

That creates correctness and safety risks.

Examples:

```text
The system remembers that a vehicle battery was disconnected last month.
It later acts as if the battery is still disconnected today.
```

```text
The system saw an image in one conversation.
It later assumes the same condition is still current without a new observation.
```

```text
The system knows a deployment was in progress two hours ago.
It later gives advice as if that deployment is still active, even though no state refresh occurred.
```

The runtime needs a way to say:

```text
This was observed at this time.
This came from this source.
This is current, stale, expired, inferred, or unknown.
This state may be used for this type of decision.
This state requires confirmation before action.
This state must not be promoted into durable memory automatically.
```

## Goals

- Define Runtime World State as distinct from conversation state, durable memory, persona profile, artifacts, and event logs.
- Represent current entities, active situations, environment facts, tool sessions, pending actions, and external system status in a structured registry.
- Attach provenance, freshness, confidence, expiry, and verification requirements to state claims.
- Prevent stale state from being treated as current reality.
- Allow events, tools, user reports, sensors, integrations, and runtime services to update world state through explicit state transition rules.
- Support low-latency prompt assembly by providing active, relevant state overlays without requiring retrieval for every current fact.
- Preserve inspectability through traces and replay fixtures.

## Non-goals

- Not a replacement for canonical memory, provenance, or retrieval.
- Not a general knowledge graph for all durable facts.
- Not a claim that world state is always correct.
- Not a hidden prompt-state mechanism.
- Not a way to bypass memory hygiene or sensitivity policy.
- Not a long-term history archive.
- Not a source of authority for high-impact actions without confirmation.
- Not a replacement for conversational runtime state.
- Not a replacement for bounded persona governance.

## Core concepts

### Runtime World State

Runtime World State is the structured active-state layer representing what the system currently believes is true about relevant entities, situations, and systems.

It answers:

- What is currently active?
- What is currently true, as far as the runtime knows?
- When was this last observed or verified?
- Where did this claim come from?
- How confident is the runtime?
- Is the claim fresh, stale, expired, inferred, or unknown?
- Can the runtime rely on this claim for response shaping, retrieval, tool use, or action?

### World-state claim

A world-state claim is an atomic statement about an entity, situation, tool, environment, or external system.

Example:

```yaml
entity_id: vehicle:primary
attribute: battery_status
value: disconnected
observed_at: 2026-05-31T00:42:00-04:00
source_type: user_report
confidence: high
freshness_state: fresh
ttl: P7D
confirmation_policy: confirm_before_action
```

The claim is not simply `battery_status = disconnected`. It is a provisional statement with metadata.

### Freshness state

Recommended states:

```text
fresh       recently observed or verified within its expected validity window
aging       still usable, but nearing its confirmation threshold
stale       may no longer be true; should be qualified or confirmed
expired     should not be used as current reality
unknown     no current state is known
superseded  replaced by a newer claim
conflicted  multiple claims disagree and require resolution
```

### Provenance

Every meaningful world-state claim should record where it came from.

Sources may include:

- user report
- tool output
- integration event
- sensor or device update
- repository inspection
- calendar event
- automation workflow
- model inference
- derived runtime policy
- imported artifact metadata

Model-inferred state must be marked as inferred and should generally have lower authority than user reports, tool outputs, or integration events.

### State authority

Recommended authority levels:

```text
observed_user_report
verified_tool_output
trusted_integration_event
derived_from_multiple_sources
model_inferred
unverified_assumption
```

The runtime should avoid relying on `model_inferred` or `unverified_assumption` for high-impact actions.

### State expiry

Each claim should define an expiry or revalidation policy appropriate to the domain.

Examples:

```text
active_voice_session: minutes
current_device_context: minutes
repository_checkout_state: minutes to hours
active_deployment_status: minutes
vehicle_battery_status: days unless confirmed
trip_window: until trip ends
health_observation: short validity; requires re-observation before current assessment
```

No state should be considered indefinitely current by default.
