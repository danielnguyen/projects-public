# Phase 4 — Trust

Phase 4 makes the assistant safer, less stale, more bounded, more explainable, and more respectful of privacy and surface constraints.

## Included public specs

- `R43 — Conversational Intent Arbitration`
- `R56 — Memory Hygiene and Staleness`
- `R57 — Privacy Zones and Surface Sensitivity`
- `R58 — Behavior Explanation and Trace Summary`
- `R71 — Answer Calibration and Evidence Grounding`

## Why this phase matters

Trust in long-lived LLM systems depends on more than model quality.

The runtime needs explicit behavior for:

- classifying the kind of conversational turn before answering
- deciding whether old memory can still be treated as current
- adapting disclosure to the active surface
- explaining governed behavior safely
- calibrating factual claims against evidence quality

These specs are intended to make behavior predictable, inspectable, and bounded without turning every interaction into a rigid rules engine.
