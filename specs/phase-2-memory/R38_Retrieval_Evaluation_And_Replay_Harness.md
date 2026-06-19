# R38 — Retrieval Evaluation and Replay Harness

Type: **Retrieval Infrastructure / Validation**  
Summary: Defines replayable retrieval evaluation infrastructure for regression detection, raw-vs-augmented comparison, and retrieval observability.

## Motivation

Retrieval systems degrade gradually.

Without replay tooling and comparison harnesses:

- ranking drift becomes invisible
- augmentation layers become uninspectable
- retrieval regressions become anecdotal
- debugging becomes intuition-driven instead of evidence-driven

R38 creates deterministic and inspectable retrieval evaluation infrastructure.

## Goals

- Enable replayable retrieval evaluation.
- Compare raw vs augmented retrieval behavior.
- Detect ranking regressions.
- Make retrieval evolution measurable.
- Preserve inspectability during retrieval hardening.

## Non-goals

- Fully automated retrieval optimization.
- ML-based relevance tuning.
- Replacing human evaluation.

## Core principles

Retrieval systems must remain:

- replayable
- inspectable
- diffable
- baseline-comparable
- explainable

## Replay harness requirements

The system should support:

- stored retrieval scenarios
- deterministic replay inputs
- snapshot comparison
- retrieval trace export
- score comparison
- augmentation visibility

## Baseline comparison modes

The harness should support:

- raw-only retrieval
- augmented retrieval
- graph-expanded retrieval
- reranked retrieval
- profile-conditioned retrieval

## Evaluation dimensions

Track:

- retrieved ids
- ranking order
- score deltas
- provenance visibility
- augmentation source visibility
- latency
- token impact

## Regression detection

The harness should surface:

- retrieval disappearance
- unstable ranking shifts
- irrelevant augmentation introduction
- provenance loss
- excessive retrieval growth

## Dataset structure

Recommended dataset fields:

- query
- expected evidence ids
- retrieval mode
- profile
- source conversation ids
- timestamp context
- expected ranking constraints

## Observability

Store:

- retrieval snapshots
- replay results
- retrieval traces
- ranking explanations
- augmentation paths

## API / Tooling

Suggested capabilities:

- replay retrieval traces
- compare retrieval outputs
- export retrieval snapshots
- run retrieval regression suites

## Initial implementation target

The initial implementation should be a lightweight raw-vs-augmented replay helper around existing retrieval behavior.

It must remain deterministic and fake-friendly: unit tests should not require live providers, embeddings, vector databases, relational databases, or object stores.

Raw mode means canonical-message retrieval without artifact or derivative augmentation. Augmented mode means the current retrieval bundle behavior. The replay harness should compare IDs, order, artifact inclusion, token estimates, and debug metadata without becoming a benchmark platform or duplicating ranking logic.
