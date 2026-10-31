# A conversation became a merged pull request

*Personal Access Bridge milestone — October 30, 2026, Toronto local time*

I started Personal Access Bridge because I wanted building software with AI agents to feel more like collaborating with a capable engineering partner.

I wanted to describe the problem, let the system carry out authorized work, give feedback in ordinary language, and see a tested, reviewable result. I wanted to keep the decisions that belong to me while spending less attention on the mechanics between conversations, coding sessions, tests, and GitHub.

On October 30, that experience came together in a live acceptance exercise.

## What happened

The test used a documentation-focused change in Browser Trust Inspector, a browser-extension inspection project. It deliberately kept the code and infrastructure stable so that the development workflow itself could be observed.

| Step | Conversation and system action | Verified outcome |
| --- | --- | --- |
| Create | A bounded PAB coding task produced a documentation draft PR | Initial draft PR published after the required checks |
| Revise | Feedback requested a clearer, more inviting README introduction | A new PAB task independently validated and reviewed the change, then advanced the same PR |
| Refine | Feedback requested a one-word wording change | Another new PAB task applied the exact substitution, ran the tests, passed independent review, and advanced the same PR again |
| Merge | The owner explicitly authorized final publication to the default branch | ChatGPT used the GitHub connector to merge the existing PR; GitHub confirmed the merged state |

The final result was **three distinct PAB tasks contributing to one GitHub pull request**, including **two successive feedback revisions**. No manual checkout, patch transfer, or Git command was required from the owner.

The wording change in the final task was intentionally small: “spot combinations” became “identify combinations.” The exactness of that change made scope preservation easy to verify. All other README content stayed unchanged, as did the adjacent privacy documentation.

## Evidence that made the result meaningful

- **Head-pinned revisions.** Each follow-up named the exact GitHub PR and expected commit. The controller created a fresh task from the previously published head.
- **Bounded execution.** The two revisions used local Codex, a maximum of two Codex turns, and zero allowed corrections; their deadlines were 900 seconds and 600 seconds.
- **Independent test execution.** The existing Python unittest suite completed successfully with exit code 0 for each revision.
- **Separate review.** Each revision received a qualified independent PASS tied to its candidate Git tree.
- **Exact-tree publication.** PAB confirmed that each published commit represented the tree that was reviewed.
- **GitHub verification.** After the final revision, the live PR head matched the published commit. The final merge used an expected-head safeguard; GitHub subsequently confirmed the PR closed and merged.
- **Human merge authority.** PAB published and revised the draft. The owner approved merging, and ChatGPT performed that action directly through GitHub.

These observations were read back from the live controller and GitHub during the acceptance exercise. The underlying task and PR records are in private repositories; this public account preserves the verified outcome without exposing private task identifiers or infrastructure details.

## Why this is a milestone

The defining moment was the second revision.

A completed coding task had already produced a reviewed and published result. I then asked for another change, and PAB opened a new bounded task against the same PR. The original coding session did not need to remain alive. The next task built on the accepted Git history, checked the new result, and updated the same review surface.

Then we repeated it once more.

That gives us a practical foundation for feedback-driven development: **a conversation can continue, tasks can finish, and the pull request can keep improving.**

The final merge also demonstrated a useful division of responsibility. The bridge handled implementation, validation, review, and draft publication. I retained the authority to accept the change into the default branch.

## What the milestone establishes

This was a successful end-to-end acceptance of the **conversation → bounded implementation → independent validation and review → draft PR → conversational feedback → further revisions → owner-authorized merge** workflow.

It exercised feedback **between completed tasks**. It did not exercise a correction turn within one task, prove behavior across all failure and recovery scenarios, or establish reliability for complex code changes. Those are separate areas for further acceptance.

The scenario was deliberately documentation-sized. That made it a useful first proof of the full interaction pattern, with a clear path to more demanding engineering work.

## What I want to preserve

The best part was the experience itself: the owner could stay focused on the work and its meaning. The assistant coordinated the routine steps. The controller supplied bounded execution and evidence. GitHub carried the durable, reviewable result.

It is the kind of collaboration I wanted when I started building PAB.

**The next milestone is to make this experience dependable for the more complex software work that motivated the project in the first place.**

[Back to Personal Access Bridge](../README.md)
