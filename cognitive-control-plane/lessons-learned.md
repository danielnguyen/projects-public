# Lessons learned building the Cognitive Control Plane

The Cognitive Control Plane began as an attempt to build a durable, stateful AI system. It also became an extended experiment in how to build serious software with AI coding agents without letting the work slowly drift away from its intent.

The second problem turned out to be nearly as interesting as the first.

## The larger retrospective

We did not begin CCP with a complete methodology for AI-assisted engineering. We built one because the project kept exposing weaknesses in the way humans and coding agents work together.

1. We used LLMs to help build a system.
   - The early workflow was simple: discuss a feature, plan it, implement it, review it, and move on.
2. We encountered drift and added specifications.
   - As the architecture spread across services, conversation summaries stopped being precise enough to govern implementation.
3. We discovered that specifications could also drift or be misread.
   - Mandatory behaviour, examples, proposed schemas, and future-state ideas were too easy to treat as equivalent.
4. We added conformance review.
   - We needed a deliberate comparison between intended behaviour and the code that actually existed.
5. We discovered that audits could overstate findings.
   - A polished review could correctly notice a mismatch and still be wrong about whether the mismatched example was required.
6. We added evidence-based correction categories.
   - Findings could now lead to an implementation correction, a specification clarification, a deferral, or rejection rather than automatic code changes.
7. We discovered that agent completion reports were insufficient.
   - The same agent that made a change could overlook files, repeat the intended design, or overstate what the implementation enforced.
8. We added independent diff review, validation, and closeout.
   - Review inspected the actual change; closeout separately verified what merged, what passed, and what remained unfinished.
9. We discovered that the workflow itself depended too heavily on conversation memory.
   - Rules about specs, service boundaries, testing, and review were too important to survive only as prompts and remembered conventions.
10. We moved the workflow into version-controlled governance.
    - The operating rules now live beside the code and specifications, where agents and humans can inspect and revise them.

The result is more than a set of safeguards for one AI project. It is an emerging method for specification-driven, evidence-checked, AI-assisted software development across long-running, multi-repository systems.

## The five lessons that mattered most

These are the lessons that changed how we work most directly.

### 1. [Memory is not a source of truth](#1-memory-is-not-a-source-of-truth)

Conversation summaries are useful for orientation. They are not reliable enough to govern implementation. The current specifications and code must win.

### 2. [More context is not always better context](#2-more-context-is-not-always-better-context)

A giant prompt can create the feeling of completeness while burying the few details that matter. Carefully selected context is usually safer than maximum context.

### 3. [Agent self-report is not verification](#3-agent-self-report-is-not-verification)

A completion report is a useful lead, not proof. Review the diff, the tests, the relevant code paths, and the final repository state.

### 4. [Guidance is not enforcement](#4-guidance-is-not-enforcement)

Putting a rule in a prompt, trace, or policy object does not mean the system enforces it. Enforcement must exist at a real boundary and be testable.

### 5. [Specifications can drift too](#5-specifications-can-drift-too)

Specifications reduce ambiguity, but they are not magically precise. They must separate requirements from examples, proposals, and future-state ideas.

## Table of contents

1. [Memory is not a source of truth](#1-memory-is-not-a-source-of-truth)
2. [More context is not always better context](#2-more-context-is-not-always-better-context)
3. [Agent self-report is not verification](#3-agent-self-report-is-not-verification)
4. [Guidance is not enforcement](#4-guidance-is-not-enforcement)
5. [Specifications can drift too](#5-specifications-can-drift-too)
6. [Test what must not happen](#6-test-what-must-not-happen)
7. [A plan must be executable](#7-a-plan-must-be-executable)
8. [Service boundaries matter](#8-service-boundaries-matter)
9. [Traces must tell the truth](#9-traces-must-tell-the-truth)
10. [Do not manufacture conformance](#10-do-not-manufacture-conformance)
11. [Defense-in-depth is not the primary control](#11-defense-in-depth-is-not-the-primary-control)
12. [A contract field is not a capability](#12-a-contract-field-is-not-a-capability)
13. [Abstractions must preserve behaviour](#13-abstractions-must-preserve-behaviour)
14. [Not every taxonomy belongs in the MVP](#14-not-every-taxonomy-belongs-in-the-mvp)
15. [Conversation context has temporal boundaries](#15-conversation-context-has-temporal-boundaries)
16. [Do not build the future too early](#16-do-not-build-the-future-too-early)
17. [Agent quota is an engineering resource](#17-agent-quota-is-an-engineering-resource)
18. [Agent workflows belong in source control](#18-agent-workflows-belong-in-source-control)
19. [Merged is not the same as finished](#19-merged-is-not-the-same-as-finished)
20. [Deferred work must remain visible](#20-deferred-work-must-remain-visible)
21. [Keep project history out of production concepts](#21-keep-project-history-out-of-production-concepts)
22. [Personal systems still need data hygiene](#22-personal-systems-still-need-data-hygiene)
23. [Human-maintained counters eventually collide](#23-human-maintained-counters-eventually-collide)
24. [The correction process also needs correction](#24-the-correction-process-also-needs-correction)

---

## 1. Memory is not a source of truth

### What happened

A new cluster often began with a summary of earlier work. The summary was usually good. It was also sometimes one revision behind, missing a boundary condition, or based on an architecture that had already changed.

That is enough to send an implementation in the wrong direction while everyone involved feels well informed.

### What we changed

Every plan, implementation, review, and closeout now starts by reading the exact specifications and current code for the affected area. Conversation memory provides orientation, but repository evidence decides the work.

### What we learned

Long-running projects cannot rely on reconstructed context, no matter how articulate the reconstruction sounds. Memory is a map. The repository is the terrain.

## 2. More context is not always better context

### What happened

As CCP grew, the natural response was to give agents more context: more specifications, more history, more architecture, more previous decisions.

That helped until it did not. Large context windows can hide omission. The agent appears to have seen everything, but a crucial distinction from several thousand tokens ago may no longer influence the answer.

### What we changed

Tasks are now bounded around exact specifications, repositories, files, implementation seams, non-goals, tests, and exit criteria. We stopped asking an agent to “understand all of CCP” before making a small change.

### What we learned

Context quality matters more than context volume. A carefully chosen slice of the system is often safer than an enormous prompt that contains the answer somewhere inside it.

## 3. Agent self-report is not verification

### What happened

Agents routinely returned polished completion reports: files changed, tests passed, requirements satisfied, PR ready.

Most were accurate. Some were subtly incomplete. A report could omit a changed file, repeat an intended design rather than the implemented one, or declare a policy enforced when the code only produced guidance.

### What we changed

Review became independent from execution. It inspects the actual diff, current code, specifications, tests, and runtime evidence. Closeout separately verifies merged PRs, deferred items, and final conformance.

### What we learned

An agent's report is a claim made by the author of the change. Treat it the way you would treat any author's summary: useful, but not a substitute for review.

## 4. Guidance is not enforcement

### What happened

CCP frequently produced the right policy decision before it had a real mechanism to enforce that decision.

A persona policy might say that retrieval should stay inside one scope. A prompt might instruct the model not to use unrelated memory. A trace might record the intended restriction. Meanwhile, a downstream service could still perform the broader retrieval.

The system knew the rule. It did not yet enforce the rule.

### What we changed

We began describing controls precisely:

- request-boundary prevention
- runtime enforcement
- result-boundary suppression
- prompt guidance
- trace-only behaviour

A capability is only called enforced when a concrete boundary prevents or rejects the prohibited operation and tests prove it.

### What we learned

“The model was told not to” is not an enforcement mechanism. Neither is “the trace says it should not.”

## 5. Specifications can drift too

### What happened

Specifications solved many problems, but not all specification text carried the same weight. Some documents mixed mandatory behaviour with possible endpoints, proposed tables, illustrative schemas, and future-state architecture.

Agents understandably treated detailed examples as instructions. The more concrete the example looked, the more likely it was to become accidental scope.

### What we changed

Specifications now distinguish required behaviour from proposed design. Reviews ask whether a statement is mandatory, illustrative, optional, or deferred before turning it into code.

### What we learned

A specification is not automatically unambiguous because it is written down. Good specs explain not only what the system must do, but which parts of the document are allowed to change.

## 6. Test what must not happen

### What happened

Happy-path tests showed that the system could classify a turn, retrieve context, apply a policy, or produce a response. They did not always prove that stale context, broader retrieval, disabled artifacts, or unsupported intent classes were excluded.

Those negative cases were where the real behavioural failures lived.

### What we changed

Regression coverage increasingly focused on forbidden outcomes:

- an older assistant question must not classify a later response as confirmation
- broader retrieval must not occur under containment
- artifact search must not run when disabled
- unsupported inputs must remain conservative
- fallbacks must not overstate what happened

### What we learned

For governed systems, correctness is often defined as much by what does not happen as by what does.

## 7. A plan must be executable

### What happened

AI-generated plans can be excellent prose and poor engineering instructions. They restate goals, summarize architecture, and list broad phases without identifying where the change belongs.

That kind of plan feels productive while moving very little work forward.

### What we changed

A plan must now identify exact repositories, specification paths, likely files, implementation seams, ownership boundaries, non-goals, compatibility constraints, validation, PR order, and exit criteria.

When the seam is already known, the agent performs a blocker-only preflight and then executes instead of writing another essay about the task.

### What we learned

A useful plan should let another engineer begin work without repeating the discovery process.

## 8. Service boundaries matter

### What happened

CCP spans runtime state, orchestration, memory, retrieval, and integrations. A feature could appear complete inside one service while another service quietly bypassed it.

Persona containment was a good example: the orchestrator resolved the policy, but the memory service still accepted retrieval requests that were broader than the policy allowed.

### What we changed

Cross-service work now names ownership explicitly and traces behaviour from the initiating request through every affected boundary. Related repositories use separate, ordered PRs rather than one vague cross-repository change.

### What we learned

In a distributed system, a correct policy object in one service does not guarantee correct behaviour across the system.

## 9. Traces must tell the truth

### What happened

A trace can be technically valid and still misleading. It may show that a policy was evaluated without showing whether it was enforced. It may describe a whole capability as unavailable even though one layer is active, or describe it as active when only guidance exists.

### What we changed

Trace language now distinguishes prevention, suppression, guidance, and deferral. Observability must report what the system actually did, not the architecture we hope to finish later.

### What we learned

A misleading trace is not harmless documentation debt. It can hide a design gap from the next engineer, reviewer, or agent.

## 10. Do not manufacture conformance

### What happened

When code and specifications disagreed, one easy path was to edit the specification until the implementation appeared correct.

The opposite failure was also possible: implement every detailed example even when the actual required behaviour was already satisfied in a simpler way.

### What we changed

Each conformance finding is classified. It may require an implementation correction, a specification correction, a deliberate deferral, an accepted extension, rejection, or more evidence.

### What we learned

Conformance is not visual similarity between a document and a codebase. It is an evidence-backed decision about required behaviour.

## 11. Defense-in-depth is not the primary control

### What happened

The orchestrator could remove unsafe artifact results before building the final prompt. That protected the model from seeing them, but the memory service had already searched for them.

The final output looked contained. The operation itself was not.

### What we changed

Unsafe retrieval is now prevented at the request boundary where possible. Result suppression remains valuable as a second layer, but it is described honestly as defense-in-depth.

### What we learned

Cleaning up an unsafe result is not the same as preventing the unsafe action.

## 12. A contract field is not a capability

### What happened

The memory request model already contained an `include_artifacts` field. On paper, callers could disable artifact retrieval.

The active retrieval path did not actually honour the field before issuing the search.

### What we changed

Contract validation now follows the full execution path. Tests assert not only that a field exists or is passed, but that downstream work is skipped or changed as intended.

### What we learned

A schema can advertise a capability the runtime does not possess. Interfaces are promises; execution paths determine whether the promise is real.

## 13. Abstractions must preserve behaviour

### What happened

Intent arbitration was folded into broader interaction governance. The architectural consolidation was sensible, but the persisted runtime projection collapsed useful distinctions into a small set of generic values.

The abstraction removed duplication and also removed information.

### What we changed

We kept the folded architecture while expanding only the intent classes that existing signals could support deterministically.

### What we learned

Combining systems is not successful merely because there are fewer components. The new abstraction must preserve the behaviour downstream consumers need.

## 14. Not every taxonomy belongs in the MVP

### What happened

Once a specification listed a comprehensive taxonomy, it was tempting to implement every category immediately. Some categories had no reliable signal, no current consumer, or no reason to exist outside the conceptual model.

### What we changed

Taxonomies are split into three groups:

- supported now
- derivable with a small deterministic extension
- not currently derivable and therefore deferred

Unsupported cases fall back conservatively instead of being guessed into existence.

### What we learned

A complete conceptual model does not require a complete first implementation.

## 15. Conversation context has temporal boundaries

### What happened

An older assistant question could influence a later user message even after another user turn had intervened. The words still existed in history, but they were no longer the immediate conversational context.

This caused stale questions to produce false confirmation or continuation classifications.

### What we changed

Those dialogue acts now use the immediately preceding assistant message for the evaluated user turn. Regression tests cover intervening messages explicitly.

### What we learned

Conversation history is not one flat bag of text. Order and adjacency are part of meaning.

## 16. Do not build the future too early

### What happened

Detailed specs naturally suggested dedicated tables, endpoints, service seams, and extension hooks. Many of them were plausible future needs. Few of them were necessary for the current behaviour.

Building them early would have created more persistence, migration, and ownership complexity before there was a consumer.

### What we changed

Future-state structures remain documented but deferred until replay, auditing, evaluation, or operational needs justify them. Current runtime state and summarized events are used where they are sufficient.

### What we learned

Good architecture leaves room for the future. It does not require constructing the future in advance.

## 17. Agent quota is an engineering resource

### What happened

Coding agents repeatedly spent expensive context rediscovering the same repository structure, rewriting approved plans, and narrating work that had already been decided.

The actual implementation received whatever attention remained.

### What we changed

We introduced verified execution briefs, blocker-only preflights, same-session execution, and delta-based review fixes. Full planning is reserved for genuinely unresolved architecture.

### What we learned

Agent quota should be spent on inspection, reasoning, implementation, and validation—not ceremonial replanning.

## 18. Agent workflows belong in source control

### What happened

Rules accumulated across conversations: read the specs, inspect current code, preserve service ownership, use bounded PRs, distinguish review from execution, test negative paths, and report exact evidence.

Those rules were too important to depend on anyone remembering the latest prompt.

### What we changed

The workflow became a versioned repository artifact that coding agents must read before working on CCP.

### What we learned

Once an AI-assisted process becomes repeatable, its operating contract belongs beside the code and specifications it governs.

## 19. Merged is not the same as finished

### What happened

A PR could merge while local branches remained, repositories stayed on stale commits, documentation lagged behind, or the working tree contained unrelated changes.

The feature was in `main`, but the workflow was not actually closed.

### What we changed

Merge and closeout have explicit meanings. Merge includes cleanup and returning to a clean, current `main`. Closeout verifies merged evidence, tests, smoke results, and remaining deviations.

### What we learned

Completion includes repository state and institutional memory, not just code landing.

## 20. Deferred work must remain visible

### What happened

Once a blocking issue was fixed, nearby gaps could disappear from attention or become loosely described as part of the completed capability.

This was especially risky for domain-aware retrieval and tool-boundary enforcement, where partial infrastructure existed but the true enforcement seam did not.

### What we changed

Closeout documents list residual deferred work explicitly, along with the reason and dependency that prevents honest completion.

### What we learned

Deferral is a sound engineering decision only when the unfinished boundary remains visible.

## 21. Keep project history out of production concepts

### What happened

Agents naturally wanted to include phase numbers, cluster numbers, historical narratives, and roadmap notes in runtime names and production READMEs.

Those details helped explain how the system arrived here. They did not help operate the current system.

### What we changed

Planning history stays in planning artifacts. Production repositories describe present behaviour. Specification IDs remain references for humans, not runtime domain concepts.

### What we learned

The history of a system and the current truth of a system are both valuable. They should not be the same document.

## 22. Personal systems still need data hygiene

### What happened

CCP is built around personal use, which made real names and real-life examples convenient during design and testing.

Convenience becomes a problem when tests, specifications, or examples are later published.

### What we changed

Implementation artifacts use neutral examples and avoid personal names, private events, and sensitive context unless the data itself is the subject under test.

### What we learned

A personal project can still become public, reusable, or shared. Data hygiene should begin before that transition.

## 23. Human-maintained counters eventually collide

### What happened

Specification IDs looked unique within a phase while already being used elsewhere in the repository. The project had grown beyond what anyone could reliably scan from memory.

### What we changed

ID allocation now uses a repository-wide scan. A registry records the current range, but the automated scan remains authoritative because the registry can become stale.

### What we learned

A counter is a convenience. Uniqueness requires checking the actual namespace.

## 24. The correction process also needs correction

### What happened

The first conformance review contained findings that were partly valid and partly overstated. Later documentation work accidentally rewrote a historical recommendation while appending new closeout evidence.

The governance artifacts designed to prevent drift were themselves vulnerable to drift. Naturally.

### What we changed

Audits are treated as inputs rather than authority. Historical findings are preserved, new evidence is appended carefully, and corrections are reviewed against both the original record and current implementation.

### What we learned

No layer of the process is above review—not the code, not the specification, not the audit, and not the rules governing the agents.

## Closing thought

The common thread through all of these lessons is not that AI coding agents are unreliable. Human engineers also misremember requirements, overread diagrams, trust summaries, build abstractions too early, and write optimistic completion notes.

AI changes the scale and speed of those failures. A plausible mistake can travel from plan to implementation to tests to documentation in one session, with consistent vocabulary and impressive confidence.

The answer is not to remove the agent from the process. It is to build a process in which confidence is cheap and evidence is required.
