# R01 — Policy-Based Model Router

Type: **Infrastructure**  
Summary: Central routing layer that selects LLM/tooling based on request type, artifacts, cost/latency, privacy/sensitivity, and user overrides.

## Goals

- Deliver the smallest useful version that unlocks downstream items.
- Keep behavior inspectable: decisions should be visible in code and traceable in logs.
- Preserve the principle that durable stores are truth and vector/model-derived state is rebuildable.

## Non-goals

- Perfect UX polish.
- Building a general agent that executes dangerous actions without explicit permission.
- Premature multi-tenant productization.

## Proposed Architecture

### Components

- **Orchestrator**: receives request and context, performs retrieval, selects model/tool, returns response.
- **Memory Store API**: persists messages, artifacts, links, derived chunks, and traces.
- **Vector Store**: semantic retrieval for text and derived artifact text.
- **Object Store**: raw artifact blobs such as images, PDFs, zips, and other files.
- **LLM Gateway**: unified API for multiple model backends.

### Data Flow

1. Request enters the orchestrator. Surface adapter adds metadata such as surface type.
2. Orchestrator calls memory services to resolve conversation context and retrieve relevant memory/artifacts.
3. Orchestrator selects model/tool through routing policy.
4. Orchestrator executes model/tool calls and collects traces.
5. Orchestrator writes user/assistant messages, artifact links, traces, and cost metrics.

## Data Model

Suggested core tables:

- `artifacts` — id, owner, hash, mime, size, object URI, source surface, filename, content hash version.
- `artifact_links` — artifact, conversation, message, relationship, creation time.
- `derived_text` — artifact, kind, language, text, creation time.
- `embeddings` — reference type/id, model, vector id, creation time.
- `traces` — request/conversation ids, router decision, retrieval metadata, model calls, cost, latency.

Derived artifacts should be rebuildable from raw blobs and deterministic derivation parameters.

## API Surface

Minimal endpoints:

- `POST /v1/artifacts`
- `GET /v1/artifacts/:id`
- `POST /v1/conversations/:id/retrieve`
- `POST /v1/orchestrate/chat`
- `GET /v1/traces/:request_id`

## Security and Privacy

- Authenticate all requests.
- Allow per-surface policy, such as denying artifact access for voice surfaces by default.
- Use short-lived presigned URLs for object storage access.
- Encrypt at rest and in transit.
- Support local-only sensitivity labels that forbid cloud model routing.

## Router Design

### Inputs

- Request metadata: `owner_id`, `conversation_id`, `surface`, optional intent, optional model override.
- Payload signals: images, files, code, large diffs, estimated prompt tokens.
- Policy signals: sensitivity, cost mode, latency mode.

### Outputs

- `selected_model`
- `tool_plan`
- `retrieval_plan`
- `fallbacks`

### Implementation

A declarative rule engine can evaluate rules top-down. Each rule should have a predicate, action, and priority.

Example rules:

- If request has images, require vision capability.
- If voice surface and private sensitivity, prefer a local model.
- If code refactor intent and code payload, prefer a code-strong model.
- If estimated tokens exceed threshold, choose larger context or reduce retrieval.

## Testing

- Golden tests: matrix of inputs to expected routing outputs.
- Cost regression tests: ensure cheap mode remains cheap.
- Fallback tests: confirm routing degrades predictably when preferred models are unavailable.
