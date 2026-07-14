# Why I Started Building a Cognitive Control Plane

*The model was never supposed to become the whole system*

I did not begin by trying to build a control plane.

I wanted a better personal AI system.

Something persistent. Something that could remember the right things, carry context across time, use different interfaces, and eventually take useful actions on my behalf. Not just a chatbot that reset every time the conversation ended, but a system that could become more useful as it learned how I worked.

At first, the shape of the solution seemed familiar: connect a language model to memory, add some tools, give it a persona, and build a few interfaces around it.

That gets you surprisingly far.

It also hides most of the hard problems.

The more persistent and capable the system became, the less comfortable I was letting the model decide what counted as memory, what was currently true, which identity it was speaking as, or whether an action should happen.

Those decisions looked like intelligence problems at first.

Eventually, I realized they were governance problems.

That realization became the Cognitive Control Plane, or CCP.

## It started with memory

The first problem was simple to describe.

I wanted the system to remember useful things about me and the world around me. Long-running projects. Preferences. Past decisions. Patterns that might matter again later.

But “remember this” turns out to contain several different questions.

What exactly should be stored? How long should it remain valid? Is it a direct fact, an interpretation, or something the model inferred from a conversation? What happens when newer evidence contradicts it? Should every persona be allowed to retrieve it? Should it appear on every interface?

Most memory systems focus on recall: how to find something relevant later. That matters, but relevance is not the same as validity.

An old preference may still be relevant and no longer be true. A repeated claim may feel important while remaining unsupported. A model-generated summary may be useful as a search aid without deserving the authority of a fact.

I did not want fluency to become durability by accident.

So memory stopped looking like a bag of embeddings and started looking like a governed lifecycle. Claims needed provenance. They needed confidence, freshness, scope, correction, supersession, and sometimes expiration. Retrieval needed policy, not just similarity.

The system had to preserve a distinction that language models often blur:

- what the model generated
- what the available evidence supports
- what the system is allowed to treat as authoritative

Those are not the same thing.

That distinction became one of CCP’s central ideas: **evidence-governed knowledge**.

## Then identity became a boundary

Once memory existed, personas became more complicated.

A persona is often treated as a prompt: a name, a tone, some behavioral instructions, perhaps a character history. That works when the persona is mostly presentation.

It becomes dangerous when the persona can access durable memory and tools.

If two personas share the same underlying system, should they also share every memory? Should a casual companion persona have access to private work context? Should a public-facing interface know what the private assistant knows? If one persona infers something sensitive, can another retrieve it later?

The prompt could say no.

But a prompt saying no is not a boundary.

If the retrieval service already searched the broader memory space, the privacy decision came too late. Filtering the result before sending it to the model might protect the final prompt, but the prohibited operation had still happened.

That was an important shift in how I thought about governance.

A rule is only enforced when code at a real boundary changes or prevents the operation. Telling the model to behave is guidance. Recording a policy decision in a trace is observability. Neither is the same as enforcement.

CCP therefore treats identity scope and surface sensitivity as runtime inputs. The active persona, interface, conversation state, and policy determine what context may be requested before retrieval begins.

The point is not to make every persona isolated by default. The point is to make sharing deliberate rather than accidental.

## Current state is not memory

Another problem appeared when conversations became more continuous.

The system needed to understand what was happening now: whether the user was answering a question, continuing a task, interrupting, confirming an action, changing topics, or returning after a gap.

It was tempting to put all of that into memory.

But current interaction state is not durable memory.

A confirmation may be valid for the next turn and meaningless ten minutes later. An unanswered question may matter until another user message interrupts it. A temporary focus of attention should not quietly become part of the user’s long-term profile.

This led to a separate runtime-state layer.

Conversation state tracks the immediate interaction: the current turn, the relevant preceding message, active work, pending confirmations, interruptions, and temporary policy. It is intentionally short-lived.

That separation sounds technical, but it prevents a very human kind of misunderstanding.

Imagine the assistant asks, “Should I restart the server?” The user ignores the question and talks about something else. Later, the user says, “Yes.” A system that treats conversation history as a bag of text may connect that “yes” to the old restart question.

A system that understands temporal boundaries knows the question is no longer adjacent and should not treat the reply as confirmation.

The words are still present.

The conversational meaning has changed.

## World state is not reality

Persistent assistants also need some representation of the current world.

The server is unhealthy. A project is blocked. A reservation exists. A package is expected tomorrow. A person has changed roles. A device is offline.

But the system’s representation of the world is not the world itself.

It is a collection of claims assembled from observations, external data, prior memory, and model interpretation. Some claims are fresh. Some are stale. Some conflict. Some were never verified.

I wanted CCP to make that uncertainty visible instead of flattening everything into prompt text.

A world-state claim should carry enough context to answer basic questions: Where did this come from? When was it observed? How confident are we? When should it expire? Does it require verification before use? Has newer evidence replaced it?

This matters because language models are extremely good at turning partial information into coherent language. Coherence can make a provisional belief feel settled.

The surrounding system has to resist that pressure.

CCP treats world state as a provisional model of what the runtime currently believes, not as a database of unquestioned truth.

## A confident answer still needs evidence

As the system gained memory and world state, another weakness became obvious: a model can produce the right answer for the wrong reasons, or a plausible answer with no reliable basis at all.

The response may sound identical either way.

That makes answer calibration a system concern, not merely a writing style.

A governed system should know whether a response is based on direct evidence, retrieved memory, current external data, an inference, or the model’s general knowledge. It should also know when sources conflict, when information is stale, and when the available evidence does not support a confident conclusion.

The goal is not to attach a wall of citations to every casual sentence. It is to preserve the internal relationship between claim and evidence so the system can behave appropriately.

Sometimes that means answering directly. Sometimes it means qualifying the answer. Sometimes it means checking another source. Sometimes it means saying that the system does not know.

The model generates language.

The control plane decides what level of confidence the evidence permits.

## Tools turned authority into a runtime problem

Memory and answers can be wrong without immediately changing the outside world.

Actions are different.

The moment an assistant can restart a service, send a message, alter a calendar, control a device, or trigger an external workflow, the architecture needs a concept of authority.

Tool access alone is not authority.

The fact that a model can call an endpoint does not mean it should decide when that endpoint is used. A user request may be ambiguous. The action may require confirmation. The state may have changed since the action was proposed. The tool may succeed only partially. Verification may fail even though execution occurred.

This is where the control-plane idea became unavoidable for me.

An action needs a governed path:

1. interpret the user’s intent
2. determine whether the action is permitted
3. summarize the proposed effect
4. obtain confirmation when required
5. revalidate the conditions before execution
6. perform the action at most once
7. verify the result when possible
8. report what actually happened, including partial outcomes

The model can help explain and reason about the action, but the surrounding runtime owns the boundaries.

This also changes how failure is represented. “Success” and “failure” are often too simple. An action can partially execute. It can succeed while verification is unavailable. It can fail safely before execution. It can execute and then return an uncertain final state.

The system should preserve those distinctions rather than force every outcome into a confident sentence.

## Why I call it a control plane

In infrastructure, a control plane does not usually perform every unit of work itself. It coordinates policy, state, permissions, and decisions across the components that do.

That is the role I want CCP to play around language models.

The model is still important. It contributes reasoning, interpretation, planning, and language generation. But it sits inside a larger system that owns the durable and consequential parts:

- memory and provenance
- runtime conversation state
- provisional world state
- persona and identity scope
- privacy and surface policy
- model routing and fallback
- answer calibration
- action authority and confirmation
- traceability and audit

This architecture also makes the model replaceable.

Hosted models will change. Local models will improve. Different tasks will favor different providers. A personal system should not have to surrender its memory, identity, policies, and behavioral continuity every time the underlying model changes.

The control plane remains stable while models come and go.

That matters to me for another reason: ownership.

Many assistant products offer persistent memory, integrations, and personalization, but the most sensitive parts of the system often become inseparable from the service providing the model. The user’s history, identity, connected data, and accumulated context live inside someone else’s product boundary.

CCP is my attempt to keep the part that represents me private, inspectable, and portable, while allowing the models and interfaces around it to evolve.

I am not trying to rebuild every component of an AI assistant.

I am trying to make sure the assistant does not become the owner of the person it represents.

## The model should not be the system

The simplest version of an LLM application looks roughly like this:

```text
user message + conversation history + retrieved context + model = answer
```

That structure is useful for prototypes. It is also easy to mistake prompt assembly for architecture.

As soon as the system becomes persistent, personalized, multi-surface, and capable of action, too many responsibilities collapse into the prompt. Privacy becomes an instruction. Memory becomes whatever was retrieved. current state becomes chat history. truth becomes fluent text. authority becomes tool availability.

CCP separates those concerns into explicit runtime components. The system decides what context is relevant and permitted, what state is temporary, what knowledge is durable, what evidence supports a claim, and what authority exists for an action. Only then does the model receive the bounded problem it is being asked to reason about.

This is not an argument against powerful models.

It is an argument for giving powerful models a well-governed environment in which to operate.

## What CCP is becoming

CCP began as a personal architecture for durable memory.

It expanded because each capability exposed the next unanswered question. Memory raised questions about evidence. Personas raised questions about privacy. continuous conversation raised questions about temporal state. external information raised questions about truth and freshness. Tools raised questions about authority and verification.

The control plane emerged from connecting those boundaries.

I still think of CCP as a personal system. The immediate goal is practical: a private AI stack that can support different personas and interfaces, remember responsibly, explain its reasoning, and perform bounded actions without making the model the sole authority.

But the underlying problem is broader.

As AI systems become persistent and capable of affecting the world, intelligence is no longer the only design challenge. The system must also decide what may be remembered, what may be believed, what may be exposed, and what may be done.

Those decisions should not live only in the model’s prompt.

They belong in the runtime.

That is the reason I am building a Cognitive Control Plane.