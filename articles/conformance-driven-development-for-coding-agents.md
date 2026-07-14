# Conformance-Driven Development for Coding Agents

*How a long-running AI project taught me that specifications are only the beginning*

I did not set out to invent a software-development methodology.

I was trying to build an AI system.

The project is called the Cognitive Control Plane, or CCP. It started as a personal attempt to build something more durable than a chatbot: a system with memory, personas, policy boundaries, tool use, observable decisions, and eventually safe actions in the outside world.

Because the project was large and I was building it largely by myself, coding agents became part of the development process from the beginning. The arrangement seemed straightforward. I would discuss the architecture with one model, turn that into a plan, hand the plan to a coding agent, review the pull request, and move on.

For a while, this worked remarkably well.

Then the project became large enough that it stopped working.

Not catastrophically. That would have been easier to notice. The failures were subtler.

A feature would be implemented and tested, but one constraint from an earlier discussion would be missing. A service would produce the right policy decision, but another service would quietly ignore it. A pull request would say that a control was enforced when the code had only added a prompt telling the model what it should do.

Nothing looked obviously broken. The code was reasonable, the tests passed, and the completion report sounded convincing. The system was simply becoming a slightly different system than the one I thought I was building.

## The first answer was better specifications

The obvious problem seemed to be context.

The architecture had grown across several repositories. Decisions lived in long conversations, summaries, plans, and old implementation prompts. Even when a summary was accurate, it could be one decision behind. A small omitted constraint could change the correct implementation.

So I began writing specifications.

Instead of asking a coding agent to reconstruct intent from a conversation, I gave it a version-controlled contract. The specification described the required behaviour, boundaries, compatibility expectations, failure cases, and tests. This was a major improvement, but it also created a new kind of confidence. Now there was a document. The agent could point to it. The implementation could claim to satisfy it. The tests could use its terminology. Everything felt more rigorous.

But a written specification is not automatically an unambiguous specification.

Some of mine mixed several kinds of statements together:

- mandatory behaviour
- example endpoints
- proposed schemas
- illustrative architecture
- future-state ideas

A detailed example could look just as authoritative as a hard requirement. An agent might implement the example literally even when a simpler design already satisfied the intended behaviour. In another case, the agent might implement the broad idea while missing a mandatory detail buried in the prose.

I had solved the problem of remembering the specification. I had not solved the problem of interpreting it.

## Then came conformance review

The next step was to compare the specification against the code deliberately.

This sounds obvious, but it is different from ordinary code review. A code review usually asks whether the change is sensible, maintainable, tested, and free from obvious defects. A conformance review asks a narrower and more uncomfortable question:

**Does the implementation actually satisfy the original requirement?**

That distinction mattered more than I expected. A clean abstraction could still erase a behaviour that another service depended on. A field could exist in an API model without affecting runtime behaviour. A trace could say that a policy was evaluated without proving that the policy changed execution. A fallback could work while the observability layer described a stronger capability than the system had actually used.

The code could be good code and still fail the contract.

So I began reviewing requirements one by one against concrete evidence: the diff, current code paths, positive tests, negative tests, fallback behaviour, cross-service tests, and the final merged repository state. That was the beginning of what I now think of as **conformance-driven development**.

## The uncomfortable discovery: the whole process could agree and still be wrong

The hardest lesson arrived later.

By then, the workflow looked disciplined. We had specifications, implementation plans, pull requests, tests, completion reports, and audits. Work was divided into clusters so that each change remained bounded. Agents reported exactly which requirements they had completed.

And yet, during a broader audit, I found clusters that had been treated as delivered even though mandatory behaviour was still partial or absent.

This was not a case where one careless agent had made an obvious mistake. The implementation existed. The tests passed. The pull requests had merged. The completion reports described the work as finished. Later work had already built on top of it.

All of those signals agreed.

They agreed because they were all validating the same narrowed interpretation.

Scaffolding had been treated as capability. Trace-only behaviour had been treated as enforcement. A manual path had been treated as a completed integration. Tests proved the implementation that existed, but nobody had returned to the original requirement and asked whether that implementation was enough.

The process was internally consistent and externally wrong.

That changed the way I thought about progress. Implementation activity is not specification satisfaction. A pull request is a claim. A test is evidence about some behaviour. A completion report is the author’s account of the change. None of those, by themselves, earns completion.

Completion has to be awarded at the requirement level.

## PASS, GAP, and AMENDED

I needed a status model that was simple enough to resist interpretation. Every mandatory requirement would receive one of three results:

- **PASS** — the implementation and relevant evidence satisfy the requirement
- **GAP** — the behaviour is missing, partial, unproven, or incorrectly composed
- **AMENDED** — the specification itself has been deliberately changed with explicit human approval

The third category became important because code is not always wrong when it differs from a specification. Sometimes the specification is ambiguous. Sometimes an example was mistaken for a requirement. Sometimes the implementation found a simpler valid design. Sometimes the original requirement no longer makes sense.

Without an amendment category, there is a temptation to manufacture conformance in one of two ways: rewrite the specification until the code appears correct, or implement every old example merely to make the documents and code look alike. Neither is honest.

AMENDED means the contract changed intentionally. It is not a softer form of PASS, and it is not a way to excuse drift after the fact.

GAP also had to remain uncomfortable. A gap does not disappear because a correction pull request merged. The merged correction is another claim. A later evidence review starts again from the original requirement and verifies the resulting behaviour. Only then can the status change to PASS.

This prevented the correcting agent from grading its own work.

## Why clusters mattered

As the requirement set grew, reviewing everything at once became impossible. Large context windows did not solve this. In practice, more context often meant that the important constraint was technically present but functionally invisible, buried among architecture notes and historical decisions.

So the work was divided into bounded clusters.

Each cluster contained a coherent group of requirements and named the exact specifications, repositories, likely code seams, non-goals, compatibility constraints, and required evidence. A cluster could not close while mandatory requirements in its scope remained unresolved, and later clusters could not quietly assume that incomplete earlier behaviour existed.

The point of clusters was not project-management neatness. They were a context-control mechanism.

A coding agent did not need to understand the entire system. It needed the smallest complete working set for the change in front of it. The same was true for review: a reviewer could inspect a bounded claim rather than accepting a broad statement that an entire phase was finished.

This also made deferral more honest. Unfinished behaviour had to remain attached to a requirement, with the missing seam or dependency recorded. It could not dissolve into a vague future task once the nearby code had merged.

## Separate the roles

Another pattern emerged from repeated failures: the same agent should not own every interpretation of a change.

The workflow gradually separated into distinct roles:

1. Architectural work resolves the requirement, system boundary, acceptance cases, non-goals, and pull-request order.
2. A coding agent receives a closed execution brief and implements the bounded change.
3. Independent review inspects the actual diff and evidence rather than trusting the completion report.
4. Closeout verifies the merged state, reruns the relevant checks, and records what remains.
5. The human retains authority when requirements conflict, evidence is incomplete, or the specification itself may need to change.

This was not based on the idea that coding agents are uniquely dishonest or unreliable. Humans also describe the design they intended rather than the code they wrote. Humans write tests around their own assumptions. Humans become attached to a pull request after spending days on it.

The difference is speed.

A coding agent can carry one plausible misunderstanding from plan to implementation to tests to documentation in a single session, using consistent and confident language at every stage. The result can look unusually coherent because the same mistaken interpretation shaped every artifact.

Separation introduces friction at exactly the place where coherence becomes dangerous.

## Tests are evidence, not the verdict

This process made me much more careful about what tests prove. A happy-path test can show that a system retrieves memory, classifies a turn, calls a tool, or produces a response. It may not show that the system avoids broad retrieval for a restricted persona, refuses an unsupported action, ignores stale conversational context, or tells the truth when it falls back.

For governed systems, correctness includes what must not happen.

Negative tests became central evidence:

- the prohibited downstream operation does not occur
- an old assistant question does not turn a later reply into a confirmation
- a disabled capability is not invoked
- unsupported classifications fall back conservatively
- traces do not claim that stronger behaviour occurred

Even then, passing tests do not automatically prove a requirement. They prove the behaviours they exercise. Conformance review connects those behaviours back to the contract, and that connection is the part most development workflows leave implicit.

## The process itself can drift

At one point, an audit overstated some findings. Later, while adding new evidence, a documentation change accidentally altered the wording of an earlier historical finding.

The mechanism built to catch drift had begun to drift.

That was almost reassuring. It clarified that there is no final layer of authority that becomes correct merely because it is labelled “review” or “audit.” The code can be wrong. The specification can be wrong. The audit can be wrong. The correction can be wrong.

Every layer produces claims. Every important claim needs evidence and provenance.

Historical findings now remain intact, with later evidence and final dispositions added separately. Reviews distinguish implementation defects, specification defects, accepted variations, deliberate deferrals, rejected findings, and cases where more evidence is required.

Conformance is not obedience to a document. It is an evidence-based determination that the current system satisfies the intended contract.

## From spec-driven to conformance-driven

I still believe in specification-driven development, and I do not think conformance-driven development replaces it. It completes it.

A specification gives a coding agent a stable source of intent. A bounded implementation brief turns that intent into executable work. Tests provide evidence about the resulting behaviour. Conformance review then decides whether that evidence is sufficient to say the requirement has actually been satisfied.

The distinction became clear to me only after watching a disciplined process repeatedly produce code that was polished, tested, merged, and still incomplete. The lesson was not that agents cannot build serious systems. CCP exists because they can. The lesson was that agentic development makes claims extraordinarily cheap.

Code, tests, explanations, diagrams, and completion reports can all be produced quickly, and they can all reinforce one another. That coherence is useful, but it can also conceal a shared misunderstanding. The missing mechanism is one that returns to the requirement, examines the actual system, and is willing to say **GAP** even when everything around it looks finished.

I did not arrive at this methodology by reading about it first. It emerged from the accumulated corrections required to keep one long-running project aligned with its intent. The pieces now feel obvious in retrospect: specifications, bounded clusters, independent evidence review, PASS/GAP/AMENDED status, explicit deferrals, and separate closeout. They did not feel obvious while I was learning why each one was necessary.

That is usually how engineering methods are born: not as frameworks looking for problems, but as scar tissue that eventually acquires a name.
