# R71 — Answer Calibration and Evidence Grounding

Type: **Runtime / Trust / Answer Quality Governance**  
Summary: Defines how the assistant calibrates factual claims, uncertainty, evidence strength, confidence, and quantitative statements before presenting an answer to a user.

## Objective

Prevent the runtime from presenting inferred, weakly-supported, stale, speculative, or unsourced claims as established facts.

The goal is not perfect truth determination.

The goal is disciplined calibration.

Core doctrine:

```text
Classify the claim.
Inspect the evidence.
Calibrate the confidence.
Separate fact from inference.
Prefer uncertainty over fabrication.
```

## Problem Statement

Even when memory, retrieval, world state, and governance behave correctly, the assistant may still produce poorly calibrated answers.

Examples:

```text
No source exists.
The assistant invents a percentage.
```

```text
Manufacturer guidance exists.
The assistant presents it as proven scientific evidence.
```

```text
The assistant has weak supporting information.
It speaks with excessive certainty.
```

```text
The assistant has relevant personal context.
It overextends that context into unsupported conclusions.
```

Trust failures frequently arise not from retrieval failure, but from calibration failure.

## Goals

- Prevent fabricated quantitative claims.
- Distinguish evidence from inference.
- Distinguish guidance from proof.
- Make uncertainty explicit.
- Support confidence-aware responses.
- Provide traceable claim justification.
- Reduce hallucinated precision.
- Improve answer trustworthiness in health, finance, legal, operational, safety, and other high-impact domains.

## Non-goals

- Determining absolute truth.
- Replacing retrieval systems.
- Replacing world-state management.
- Replacing memory provenance.
- Requiring sources for every casual conversation.
- Blocking useful reasoning when evidence is incomplete.

## Claim Classification

Meaningful claims should be classified before response generation.

Recommended claim classes:

```text
verified_fact
source_backed_fact
manufacturer_guidance
expert_consensus
runtime_inference
speculation
unknown
```

Examples:

```text
"ResMed recommends weekly cleaning."
-> manufacturer_guidance
```

```text
"Weekly cleaning reduces infection risk by 37%."
-> source_backed_fact only if evidence exists; otherwise invalid
```

```text
"The risk is probably somewhat higher."
-> runtime_inference
```

## Evidence Hierarchy

Suggested trust ordering:

```text
peer_reviewed_evidence
clinical_guidance
manufacturer_guidance
tool_output
trusted_integration
user_report
runtime_inference
speculation
```

Higher-ranked evidence should generally override lower-ranked evidence.

## Quantitative Discipline

The runtime must not fabricate:

- percentages
- probabilities
- prevalence estimates
- timelines
- confidence values
- growth estimates
- risk multipliers

unless supported by evidence.

Bad:

```text
Skipping cleaning for two weeks likely doubles contamination risk.
```

Good:

```text
I am not aware of evidence quantifying the increase.
The risk is likely higher, but I cannot estimate the magnitude.
```

## Confidence Model

Confidence should be tied to evidence quality.

Confidence should not be generated solely because a model feels certain.

Guidelines:

```text
high
- strong evidence
- multiple corroborating sources

medium
- reasonable support
- limited uncertainty

low
- weak support
- inference dominant

unknown
- insufficient evidence
```

Confidence must never replace provenance.

## Uncertainty Handling

The runtime should be allowed to answer:

```text
Unknown.
```

```text
Evidence is limited.
```

```text
No reliable estimate exists.
```

```text
I cannot quantify that.
```

without treating such responses as failures.

## Personalization Boundaries

Personal context may influence framing.

Personal context must not manufacture evidence.

Example:

```text
User uses CPAP.
User uses distilled water.
User reports no recurring infections.
```

Allowed:

```text
Those details may reduce concern and provide context.
```

Not allowed:

```text
Therefore infection risk is low.
```

unless evidence supports the conclusion.

## Runtime Outputs

Illustrative object:

```yaml
answer_calibration:
  claim_class: runtime_inference
  evidence_level: manufacturer_guidance
  confidence: low
  quantitative_claim_allowed: false
  uncertainty_disclosure_required: true
  evidence_refs:
    - source_001
```

## Integration

### Interaction Governance

Interaction governance determines whether calibration-sensitive handling is required.

### Memory Hygiene and Staleness

Memory hygiene provides freshness and contradiction inputs.

### Behavior Explanation and Trace Summary

Trace summaries may expose summarized calibration rationale.

## Evaluation

Implementation is sufficient when tests prove:

1. Unsourced percentages are rejected.
2. Manufacturer guidance is not presented as scientific proof.
3. Weak evidence produces lower confidence.
4. Unknown remains an acceptable outcome.
5. Personal context does not become evidence.
6. High-impact domains show improved calibration.

## Example Fixture

Bad:

```text
Skipping one week of CPAP cleaning likely doubles contamination risk.
```

Good:

```text
Weekly cleaning is the manufacturer recommendation.

I am not aware of evidence quantifying the increase in risk from one week to two weeks.

The risk is likely higher, but I cannot estimate the magnitude.
```

## Notes

This spec governs answer calibration.

It does not replace:

- memory provenance
- world-state provenance
- interaction governance
- privacy policy
- action authorization

It consumes those systems and determines how confidently the assistant should speak.
