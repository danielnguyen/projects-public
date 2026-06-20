# R36 — Retrieval Hardening and Raw-First Validation

Type: **Retrieval / Evaluation Hardening**  
Summary: Requires all advanced retrieval paths to remain comparable against a raw baseline, debuggable in operation, and measurable in terms of usefulness.

## Goals

- Protect the system from retrieval complexity that becomes hard to inspect or justify.
- Require a raw-first fallback path for memory retrieval and debugging.
- Standardize validation expectations for reranking, overlays, graph assistance, temporal weighting, and proactive retrieval inputs.
- Make regressions visible before new retrieval layers become entrenched.

## Non-goals

- Removing advanced retrieval features.
- Forcing production to use raw retrieval by default in all contexts.
- Building a public benchmark suite for internet debate.
- Replacing product judgment with benchmark scores alone.

## Proposed Architecture

### Retrieval modes

- **Raw baseline**: semantic and/or lexical retrieval directly over canonical evidence only, with minimal metadata filtering and no derivative-memory signals.
- **Augmented retrieval**: raw baseline plus reranking, temporal adjustments, overlays, graph support, salience logic, or scene-aware weighting.
- **Debug compare mode**: returns enough information to compare raw baseline and augmented output for the same request.

### Hardening rules

- Every advanced retrieval path must be comparable to a simpler baseline.
- Every advanced retrieval path must be bypassable for debugging.
- Retrieval logs must record what adjustments were applied.
- New retrieval logic must state expected improvement and likely failure modes.
- Features that do not improve retrieval quality, actionability, or operability should not graduate by default.
- Raw baseline behavior must remain free of graph edges, overlays, summaries, salience labels, or other derivative-memory assistance.

### Debug surface expectations

A debug-capable retrieval path should be able to expose:

- raw hits
- augmented hits
- ranking adjustments applied
- provenance of any derivative features used
- explanation of why high-ranking items moved up or down
- explicit indication of whether derivative-memory inputs were stale, contradicted, or unvalidated

## Validation Framework

### Minimum recurring checks

- fixed retrieval scenarios across recent, balanced, and historical contexts
- a few end-to-end task flows using real memory patterns
- raw baseline vs augmented comparison
- spot checks for provenance on derivative-assisted retrieval
- regression checks after major retrieval or salience changes

### Suggested evaluation dimensions

- relevance
- recency appropriateness
- continuity usefulness
- explainability
- stability under small prompt variation
- failure containment when derivative layers are stale or wrong

### Release gating guidance

A retrieval feature should document:

- problem solved
- baseline behavior today
- expected measurable gain
- rollback path
- disable/bypass mechanism
- observed regressions or tradeoffs
- justification for why raw baseline alone is insufficient

## Runtime Contract

### Inputs

- retrieval query
- owner/profile/mode context
- canonical memory corpus
- optional derivative memory signals
- debug flag

### Outputs

- result set
- retrieval metadata
- optional raw-vs-augmented comparison payload
- explanation fields for inspection and trace logging

## API / Integration Guidance

Possible patterns:

- `debug_compare=true` style query support on retrieval endpoints
- structured trace payloads recording rerank steps
- optional raw-only mode for diagnosis and evaluation
- validation harnesses that replay representative queries across retrieval modes

## Observability

Emit structured logs for:

- retrieval mode used
- raw result IDs
- augmented result IDs
- rerank components applied
- derivative inputs used and their provenance state
- fallback-to-raw events
- validation failures and regression alerts

## Evaluation

Test for:

- advanced retrieval outperforming or justifying itself against raw baseline
- ability to inspect surprising retrieval results
- graceful degradation when graph, overlay, or salience layers are stale
- low unexplained divergence between raw and augmented paths

## Concrete examples

Good:

- A time-aware retrieval change shows raw hits, reranked hits, and the temporal adjustments that changed order.
- A proactive suggestion pipeline can explain which source memories and event items triggered context selection.

Bad:

- A graph-assisted retriever ships with no raw fallback and no way to inspect why a node influenced ranking.
- A feature changes retrieval behavior materially but cannot be compared against prior baseline behavior.
