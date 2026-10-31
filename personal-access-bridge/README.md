# Personal Access Bridge

Imagine describing an engineering change in a conversation, getting back a tested pull request, and refining the result with a sentence of feedback. When the work is ready, you decide when it becomes part of the codebase.

**That is the development experience I'm building with Personal Access Bridge (PAB).**

PAB connects a conversational assistant to local coding agents through a controlled development workflow. I can focus on the problem, the design, and the decisions that matter. The bridge carries a bounded task through implementation, automated tests, independent review, and publication as a draft pull request.

Feedback can begin another scoped task against the existing pull request. Every revision has its own starting commit, execution limits, validation, and review. The owner retains the final merge decision.

## Why I'm building it

AI coding agents have made it possible to build ambitious software with a small team. The work around the agents can still consume enormous attention: preparing handoffs, managing checkouts, moving reports between tools, requesting reviews, tracking revisions, and verifying what was actually delivered.

I want a more natural way to collaborate.

A conversation should be enough to explain a goal or challenge a design. The assistant should be able to translate that intent into an authorized task, coordinate the routine development steps, and return with evidence I can understand. I want to spend more of my time on architecture, creativity, and judgment, while retaining confidence in how the work was produced.

PAB is a step toward that experience: **a development partner that can carry work forward within clear boundaries and bring people back in when their judgment matters.**

## The workflow

1. **Describe the change.** The owner and conversational assistant agree on the goal, affected repository, allowed files, constraints, and success criteria.
2. **Delegate bounded implementation.** PAB starts an isolated local coding task against a pinned repository baseline.
3. **Verify the result.** The controller runs approved tests and sends the exact candidate to a separate reviewer. Successful validation and a qualified review are required for publication.
4. **Publish a draft PR.** The controller publishes the reviewed Git tree, preserving a reviewable outcome.
5. **Keep collaborating.** New feedback can start another bounded task against the current PR head and update that same draft after fresh verification.
6. **Choose when to merge.** The owner authorizes the final merge through GitHub. PAB's development and publication authority ends before merging.

## A milestone: feedback all the way to merge

On **October 30, 2026 (Toronto time)**, we completed a live demonstration of this workflow using a documentation change to Browser Trust Inspector, a browser-extension inspection project.

The initial PAB task created a draft pull request. A second PAB task rewrote the README introduction to explain the project's purpose and benefits. A third task changed a single word in response to another conversational request. Both revisions updated the **same pull request**, with independent validation and review before publication. The owner then authorized a merge through the GitHub connector.

**Three PAB tasks. One pull request. Two feedback revisions. An independently reviewed result, merged under human control.**

The outcome matters because feedback remained a normal part of the conversation. Completing one coding task did not close the door on improving its pull request. Each follow-up began from the previously published head and produced another verifiable result.

Read the [dated milestone record](milestones/2026-10-30-conversation-to-merge.md) for what was observed and what this demonstration establishes.

## Where the work goes next

This acceptance demonstrated the complete feedback-to-merge experience on a tightly scoped documentation change. The next useful evidence will come from substantial source-code work, more complex feedback, and recovery from interrupted or failed execution.

PAB will keep evolving around the same principle: **make progress feel natural, preserve evidence, and keep consequential decisions in human hands.**
