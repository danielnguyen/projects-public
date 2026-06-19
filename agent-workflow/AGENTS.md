# CCP Agent Workflow

This file defines stable operating rules for AI coding agents working on Cognitive Control Plane artifacts.

The repository specifications are authoritative for product behavior and architecture. This file governs how work is planned, executed, reviewed, and closed out. It must not be used as a substitute for the relevant specifications.

## Authoritative sources

Before planning or changing CCP work:

1. Read the exact referenced specifications.
2. Read the current implementation in every affected repository.
3. Read any active conformance report, correction plan, or closeout document named by the task.
4. List the exact specification paths used in the plan, PR description, or completion summary.
5. Do not rely on reconstructed memory when repository evidence is available.

When specifications and implementation disagree, identify the discrepancy explicitly. Do not silently rewrite requirements to match current code or change code to match an illustrative example without determining which language is authoritative.

Distinguish mandatory requirements from illustrative endpoints, proposed schemas, examples, and future-state notes.

## Repository and documentation boundaries

- Production repositories describe current behavior and current operating requirements.
- Do not add phase, cluster, historical implementation, or future-roadmap narration to production READMEs unless it is required for present operation.
- Specification IDs are planning references, not production domain concepts. Do not embed identifiers such as `R43` or cluster numbers into runtime APIs, database fields, trace fields, class names, or user-facing behavior.
- Use neutral examples in code, tests, and docs. Do not introduce personal names or private-life details.
- Preserve established ownership boundaries between runtime, orchestration, memory, integrations, and other services.

## Default Git workflow

Unless the task explicitly says otherwise:

1. Start from an up-to-date `main` branch.
2. Create a short-lived branch named for the bounded change.
3. Make only the changes required by the task.
4. Run the relevant tests and validation before committing.
5. Commit and push the branch.
6. Open a pull request to `main`.
7. Return the PR link, changed files, validation performed, and any unresolved deviation.

Do not commit directly to `main` unless explicitly instructed.

Keep PRs bounded by repository and coherent behavior. Small, tightly related documentation corrections in the same repository may be combined. Cross-repository implementation changes should normally use separate PRs with explicit dependencies.

## Command semantics

Interpret user instructions literally:

### `commit`

Commit the current validated changes on the current branch. Do not push and do not open a PR.

### `commit and push`

Commit the current validated changes and push the current branch. Do not open a PR.

### `commit, push, open a PR`

Commit the validated changes, push the short-lived branch, and open a PR to `main`. Return the PR link and validation summary.

### `review`

Inspect the actual diff, affected code, relevant specifications, and test evidence. Do not merely restate the PR description or plan. Report material findings first. If there are no material findings, say that the PR is ready to merge.

### `merge`

Merge the approved PR, delete the remote feature branch, delete the local feature branch where applicable, switch back to `main`, pull the latest `main`, and confirm the working tree is clean. Do not interpret `merge` as only approving or marking the PR ready.

## Quota-efficient execution workflow

Do not repeat expensive planning and repository discovery when the task already provides a verified execution brief.

### When the implementation seam is known

Use this loop:

1. Read the named specifications and implementation files.
2. Perform a blocker-only preflight.
3. Report only material deviations, missing dependencies, or unsafe assumptions.
4. If there are no blockers, execute in the same agent session.
5. Apply review feedback as a delta rather than regenerating the entire plan.
6. Run the specified tests and validation.
7. Open the PR and provide a concise completion report.

Do not produce a full restatement of the task as a new plan unless requested.

A blocker-only preflight should answer:

- Do the named files and implementation seams still exist?
- Does current code materially contradict the execution brief?
- Is an additional repository or contract change required for correctness?
- Is there a safety, data-loss, migration, or compatibility risk not covered by the brief?

If the answer is no, proceed.

### When a full plan is justified

Generate a full plan only when one or more of these are true:

- the implementation seam is genuinely unknown
- multiple architectural options remain unresolved
- affected repositories are not yet known
- the specification is ambiguous or internally inconsistent
- migration, compatibility, or data-loss risk requires design work first

## Planning requirements

A plan must be executable, not a paraphrase. Include:

- exact repositories
- exact specification paths
- likely files and implementation seams
- required behavior
- ownership boundaries
- non-goals
- compatibility constraints
- tests and smoke validation
- PR order and dependencies when multiple repositories are involved
- explicit exit criteria

Do not pre-decide findings before checking the authoritative specs and current code. Recommendations may guide inspection but must be confirmed by evidence.

Avoid arbitrary intent registries, hardcoded taxonomies, or new abstractions unless required by the specifications and current architecture.

## Implementation rules

- Prefer the smallest coherent change that satisfies the authoritative requirement.
- Preserve backward compatibility unless the task explicitly authorizes a breaking change.
- Do not claim enforcement where behavior is only prompt guidance, tracing, or result suppression.
- Distinguish request-boundary prevention, runtime enforcement, result-boundary suppression, and prompt guidance accurately.
- Do not add speculative future hooks merely because they might be useful later.
- Do not duplicate existing mechanisms when a current service already owns the capability.
- Do not weaken requirements solely to declare the current implementation conformant.
- Treat fallback behavior, trace truthfulness, privacy, and failure handling as part of correctness.

## Validation checklist

Before opening a PR, confirm:

- the exact referenced specs were reread
- changed behavior matches ownership boundaries
- changed files are limited to the bounded task
- relevant unit and integration tests pass
- fallback and negative cases are covered where applicable
- traces and documentation describe enforcement truthfully
- no personal examples or spec IDs leaked into production concepts
- no unrelated README history or roadmap language was added
- no uncommitted changes remain outside the task

For documentation-only work, validate consistency against the current implementation and neighboring specs. Do not claim runtime tests were executed when no runtime code changed.

## Completion response

After execution, report only:

- PR link
- repositories and files changed
- tests or validation performed
- any unresolved blocker, deferred item, or deviation

Do not produce a long retrospective unless requested.
