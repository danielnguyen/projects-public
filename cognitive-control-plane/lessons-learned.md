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
   - Findings could lead to an implementation correction, a specification clarification, a deferral, or rejection rather than automatic code changes.
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
8. [Service boundaries need contracts](#8-service-boundaries-need-contracts)
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

New work often began from a summary of previous conversations. Those summaries were useful, but they could be one decision behind or omit a small constraint that changed the correct implementation.

A confident summary made the problem worse because it felt authoritative.

### What we changed

Before planning or changing code, we read the exact specifications and current implementation for the affected area. Plans list the files and specifications they rely on so reviewers can check the same sources.

Conversation memory is used to find the right place to look. It does not decide what the system should do.

### What we learned

The repository is the source of truth. Conversation history is only a guide to it.

## 2. More context is not always better context

### What happened

As the project grew, we responded by giving agents more context: more specifications, more history, more architecture, and more previous decisions.

This created a new failure mode. The answer could be somewhere in the prompt, but buried deeply enough that it no longer affected the result.

### What we changed

Each task now gets a bounded working set:

- the exact specifications involved
- the repositories and files likely to change
- the current implementation seam
- explicit non-goals
- compatibility constraints
- required tests and exit criteria

We no longer ask an agent to understand the entire system before making a local change.

### What we learned

Useful context is selected context. More text does not guarantee more understanding.

## 3. Agent self-report is not verification

### What happened

Agents produced polished completion reports listing files changed, tests passed, and requirements satisfied.

Most reports were accurate. Some omitted files, described the intended design rather than the implemented behaviour, or claimed enforcement where the code only added guidance.

### What we changed

Execution and review are separate steps. Review checks:

- the actual diff
- the relevant specifications
- the current code path
- the tests that ran
- the final repository state

Closeout then confirms what merged, what passed, and what remains deferred.

### What we learned

A completion report is a claim from the author of the change. It is useful, but it is not proof.

## 4. Guidance is not enforcement

### What happened

The system could produce the correct policy decision without having a mechanism that forced downstream code to obey it.

For example, a policy could say that a persona must use restricted memory while another service still performed a broader search. The model was told the rule, but the operation had already happened.

### What we changed

We now describe each control by what it actually does:

- **request prevention** stops an unsafe operation before it starts
- **runtime enforcement** rejects or constrains an operation while it runs
- **result filtering** removes unsafe results after the operation
- **prompt guidance** tells the model how it should behave
- **trace-only behaviour** records a decision without changing execution

We call something enforced only when code at a real boundary prevents or rejects the prohibited behaviour, and tests prove that it does.

### What we learned

“The model was told not to” is not enforcement. Neither is “the trace says it should not.”

## 5. Specifications can drift too

### What happened

Some specifications mixed several kinds of information:

- required behaviour
- example endpoints
- proposed schemas
- illustrative architecture
- future-state ideas

Detailed examples looked authoritative, so agents sometimes implemented them as requirements even when a simpler design already satisfied the intended behaviour.

### What we changed

Specifications now label statements by intent. A reader should be able to tell whether something is required, optional, illustrative, proposed, or deferred.

Reviews check that classification before turning text into code.

### What we learned

Writing something down does not make it unambiguous. A good specification explains both what is required and what is allowed to vary.

## 6. Test what must not happen

### What happened

Happy-path tests showed that the system could classify a turn, retrieve memory, apply a policy, or produce a response.

They did not always prove that the system avoided stale context, broad retrieval, disabled artifact searches, or unsupported classifications.

### What we changed

We added tests for prohibited outcomes. Examples include:

- an old assistant question must not turn a later reply into a confirmation
- restricted personas must not trigger broad memory retrieval
- artifact search must not run when artifacts are disabled
- unsupported intent classes must fall back conservatively
- fallback traces must not claim that stronger behaviour occurred

### What we learned

In governed systems, correctness includes proving what the system refuses to do.

## 7. A plan must be executable

### What happened

AI-generated plans could sound thoughtful while leaving the real work unresolved. They summarized goals and architecture without identifying the files, contracts, or code paths that needed to change.

The next agent then repeated the same discovery work.

### What we changed

An execution plan now identifies:

- the exact repositories and specification paths
- the files or code seams likely to change
- the required behaviour
- service and data ownership
- non-goals
- backward-compatibility constraints
- tests and smoke checks
- PR order when several repositories are involved
- a clear definition of done

When the implementation seam is already known, the agent checks only for blockers and then starts the work.

### What we learned

A plan is useful when another engineer can execute it without having to rediscover the task.

## 8. Service boundaries need contracts

### What happened

As CCP grew, important behaviour began crossing service boundaries. A feature could look complete inside one repository while another service ignored, weakened, or bypassed it.

Persona containment exposed this clearly. The orchestrator resolved a restricted policy, but the memory service could still receive and execute a broader retrieval request.

### What we changed

When behaviour crosses a service boundary, we define an explicit contract between the services. The contract states:

- what data is passed
- what each field means
- which values are required or optional
- which service owns each decision
- where each rule is enforced
- how errors and fallbacks behave
- what compatibility must be preserved

Changes to the contract are implemented in ordered PRs across the affected repositories. Tests cover both sides: the caller must send the right request, and the receiving service must honour it.

### What we learned

A policy inside one service cannot govern a distributed system by itself. Cross-service behaviour needs a shared, tested contract.

## 9. Traces must tell the truth

### What happened

A trace could report that a policy was evaluated without saying whether the policy changed execution. It could describe a capability as active when only guidance existed, or describe it as absent when one layer was already working.

The trace looked clean while hiding the important distinction.

### What we changed

Trace fields now describe concrete events and outcomes. They distinguish among:

- a decision being calculated
- a request being changed
- an operation being blocked
- results being filtered
- a fallback being used
- work remaining deferred

Tests check trace output for negative and fallback paths as well as successful ones.

### What we learned

Observability is part of correctness. A trace must describe what the system did, not what the architecture intended.

## 10. Do not manufacture conformance

### What happened

When code and specifications disagreed, there were two easy but wrong responses:

- rewrite the specification until the code appeared correct
- implement every detailed example even when the required behaviour was already satisfied another way

Both approaches optimize for visual agreement rather than correctness.

### What we changed

Every conformance finding receives an explicit disposition:

- implementation defect
- specification defect or ambiguity
- accepted implementation variation
- deliberate deferral
- rejected finding
- more evidence required

The disposition must explain the required behaviour and the evidence behind the decision.

### What we learned

Conformance means satisfying the intended contract. It does not mean making the code resemble every example in a document.

## 11. Defense-in-depth is not the primary control

### What happened

The orchestrator could remove unsafe memory results before sending context to the model. That protected the final prompt, but the memory service had already performed the unsafe search.

The output was contained. The operation was not.

### What we changed

The primary control now acts before retrieval: the request is restricted so the unsafe search is never issued.

Result filtering remains as a second safety layer in case an upstream or downstream defect still produces disallowed data.

### What we learned

Cleaning up the result of an unsafe operation is not the same as preventing the operation.

## 12. A contract field is not a capability

### What happened

A request model already contained an `include_artifacts` field. Its presence suggested that callers could disable artifact retrieval.

The active retrieval path ignored the field and searched artifacts anyway.

### What we changed

Contract tests now follow important fields through the full execution path:

1. the caller sets the field correctly
2. the receiving service parses it correctly
3. the runtime changes its behaviour
4. the prohibited downstream operation does not occur
5. the trace reports the outcome accurately

### What we learned

A field in a schema is only a promise. The capability exists when the runtime honours that promise.

## 13. Abstractions must preserve behaviour

### What happened

Intent arbitration was folded into a broader interaction-governance component. The consolidation reduced duplication, but the new runtime representation collapsed several useful intent distinctions into generic values.

The architecture became simpler while the behaviour became less expressive.

### What we changed

We listed the distinctions that downstream consumers actually use, then preserved those distinctions in the folded design. We only added categories that could be derived reliably from existing signals.

Tests verify the externally visible behaviour rather than the number or names of internal components.

### What we learned

An abstraction is successful when it simplifies implementation without erasing behaviour that other parts of the system depend on.

## 14. Not every taxonomy belongs in the MVP

### What happened

Specifications sometimes described complete taxonomies before the runtime had reliable signals or consumers for every category.

Implementing the entire list would have required guessing classifications that the system could not actually support.

### What we changed

Each taxonomy is divided into three groups:

- supported now with existing evidence
- derivable with a small deterministic rule
- unsupported and explicitly deferred

Unsupported cases use a conservative fallback instead of a fabricated classification.

### What we learned

A complete conceptual model does not require a complete first implementation.

## 15. Conversation context has temporal boundaries

### What happened

An old assistant question could affect a later user reply even after another user message had intervened.

The relevant words were still in the conversation history, but they were no longer the immediate conversational context. This produced false confirmation and continuation classifications.

### What we changed

Dialogue acts that depend on an assistant prompt now examine only the immediately preceding assistant message for the user turn being evaluated.

Regression tests include interrupted and interleaved conversations so stale questions cannot leak forward.

### What we learned

Conversation history is ordered interaction, not a bag of text. Adjacency changes meaning.

## 16. Do not build the future too early

### What happened

Specifications described plausible future tables, endpoints, services, and extension points. They were useful design notes, but many had no current consumer.

Building them immediately would have added migrations, persistence, and ownership complexity without improving current behaviour.

### What we changed

Future-state designs remain documented as proposals. They move into implementation only when a concrete need appears, such as replay, auditing, evaluation, or a real integration seam.

Until then, we use the smallest existing structure that satisfies the current contract.

### What we learned

Good architecture preserves room for future change. It does not pre-build every possible future.

## 17. Agent quota is an engineering resource

### What happened

Coding agents spent expensive context repeatedly reading repository structure, rewriting approved plans, and explaining decisions that were already settled.

The implementation then received whatever budget remained.

### What we changed

We introduced shorter execution briefs that contain the already-verified context an agent needs. The agent performs a blocker check, executes in the same session, and addresses review feedback as a focused delta rather than restarting the task.

Full replanning is reserved for unresolved architecture or newly discovered constraints.

### What we learned

Agent quota should be spent on inspection, reasoning, implementation, and validation—not repeated ceremony.

## 18. Agent workflows belong in source control

### What happened

Important working rules accumulated in conversations: read the specs, inspect current code, preserve service boundaries, use bounded PRs, test negative paths, and report exact evidence.

Those rules changed over time and were too important to depend on the latest prompt being copied correctly.

### What we changed

The repository now contains a versioned agent operating guide. It defines the required workflow for planning, implementation, review, validation, and closeout.

Changes to the workflow are reviewed like other engineering changes, and agents are instructed to read it before working in the repository.

### What we learned

Once an AI-assisted process becomes repeatable, its rules are part of the engineering system and belong in source control.

## 19. Merged is not the same as finished

### What happened

A pull request could merge while local repositories remained on old commits, temporary branches survived, documentation lagged behind, or deferred gaps were no longer visible.

The code had landed, but the work was not truly closed.

### What we changed

We separated two checkpoints:

- **merge complete:** the PR is merged, branches are cleaned up, and repositories are back on a clean, current `main`
- **closeout complete:** merged code, tests, smoke checks, documentation, and remaining deviations have been independently verified

### What we learned

Completion includes the final repository state and the record of what remains—not only the merge event.

## 20. Deferred work must remain visible

### What happened

Once the immediate blocker was fixed, nearby gaps could disappear from attention or be loosely described as part of the completed capability.

This happened when infrastructure existed for a feature but the real enforcement or integration seam did not yet exist.

### What we changed

Every closeout records deferred work with:

- the unfinished behaviour
- why it is not complete
- the dependency or missing seam
- the specification or backlog reference that keeps it visible

Completed documentation does not describe deferred behaviour as active.

### What we learned

Deferral is a valid decision only when the unfinished boundary remains explicit and discoverable.

## 21. Keep project history out of production concepts

### What happened

Phase numbers, cluster numbers, specification IDs, and historical explanations began appearing in runtime names and operational documentation.

They explained how the system was built, but they did not explain how the current system behaves.

### What we changed

Planning history stays in plans, retrospectives, and closeout records. Production code and operational documentation use domain terms that describe current behaviour.

Specification IDs remain references for people and tools; they do not become runtime concepts unless the runtime genuinely needs them.

### What we learned

The history of a system and the operating model of a system are different kinds of information. Keep both, but keep them separate.

## 22. Personal systems still need data hygiene

### What happened

Because CCP began as a personal system, real names and real-life examples were convenient during design and testing.

Those details became a liability when artifacts were reused, reviewed, or prepared for public release.

### What we changed

Specifications, tests, examples, and fixtures now use neutral data unless personal information is specifically required by the test.

Before publication, we review files for names, private events, credentials, local paths, and other identifying context.

### What we learned

A personal project can become public or reusable later. Data hygiene is cheaper when it starts early.

## 23. Human-maintained counters eventually collide

### What happened

A new specification ID appeared available within one directory but was already used elsewhere in the repository.

The namespace had grown beyond what a person or conversation summary could reliably track.

### What we changed

A repository-wide script scans all specification IDs and reports duplicates. A registry helps people choose the next ID, but the scan of the actual repository remains authoritative.

The check runs before new specifications are accepted.

### What we learned

A counter is a convenience. Uniqueness requires checking the real namespace.

## 24. The correction process also needs correction

### What happened

A conformance audit contained findings that were partly valid and partly overstated. Later, a documentation update accidentally changed the wording of an earlier historical finding while adding new closeout evidence.

The process created to prevent drift was itself capable of drifting.

### What we changed

Audits are treated as evidence to review, not final authority. Corrections preserve the original finding, add the new evidence separately, and state the final disposition clearly.

Changes to historical records are kept narrow and reviewed against both the earlier document and the current implementation.

### What we learned

No layer is above verification: not the code, the specification, the audit, or the process used to correct them.

## Closing thought

These lessons are not based on the idea that AI coding agents fail in uniquely mysterious ways. Humans also misremember requirements, overread diagrams, trust summaries, build abstractions too early, and write optimistic completion notes.

AI changes the speed and consistency with which those mistakes can spread. One plausible misunderstanding can move from plan to implementation to tests to documentation in a single session, using the same confident vocabulary throughout.

The answer is not to remove the agent from the process. It is to build a process in which claims are cheap, contracts are explicit, and evidence is required.
