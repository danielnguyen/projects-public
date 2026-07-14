# Why I Started Building a Cognitive Control Plane

*The model was never supposed to become the whole system*

I did not begin by trying to build a control plane. I wanted a better personal AI system: something persistent enough to remember useful context, flexible enough to work across different interfaces, and capable enough to eventually take bounded actions on my behalf.

At first, the shape of the solution seemed familiar. Connect a language model to memory, add some tools, give it a persona, and build a few interfaces around it. That gets you surprisingly far, which is probably why so many AI systems begin there.

It also hides most of the hard problems.

As the system became more persistent, I became less comfortable allowing the model to decide what counted as memory, what was currently true, which identity it was speaking as, or whether an action should happen. These initially looked like intelligence problems. Over time, they began to look more like governance problems.

That shift in perspective became the Cognitive Control Plane, or CCP.

## It started with memory

The first goal was straightforward: I wanted the system to remember useful things about me and the world around me. That included long-running projects, preferences, past decisions, and historical patterns that might become relevant again later.

But a request as simple as “remember this” contains several decisions. What exactly should be stored? Is it a direct fact, a summary, or an inference produced by the model? How long should it remain valid? What happens when newer evidence contradicts it? Who should be allowed to retrieve it, and on which interfaces?

Most memory systems focus primarily on recall: how to find something relevant later. Recall matters, but relevance is not the same as validity. An old preference may still be semantically relevant while no longer being true. A repeated claim may seem important without ever being well supported. A model-generated summary may be useful for search without deserving the authority of a fact.

I did not want fluent language to become durable knowledge by accident.

That led me to treat memory as a governed lifecycle rather than a collection of embeddings. Durable claims need provenance, freshness, scope, correction, supersession, and sometimes expiration. Retrieval also needs policy, because finding something does not automatically mean the system should use it.

This produced one of CCP’s central distinctions:

- what the model generated
- what the available evidence supports
- what the system is permitted to treat as authoritative

I call this **evidence-governed knowledge**. The model can propose, summarize, infer, and explain, but the surrounding system decides what becomes durable and how much authority it receives.

## Identity became more than a persona prompt

Once memory existed, personas became more complicated.

A persona is often implemented as presentation: a name, a tone, behavioral instructions, perhaps a fictional history. That is usually sufficient when the persona only changes how an answer sounds. It becomes much more consequential when the persona can access durable memory, external data, or tools.

If two personas share the same underlying system, should they also share every memory? Should a casual companion persona have access to private work context? Should a public-facing interface know what a private assistant knows? If one persona infers something sensitive, can another retrieve it later?

A prompt can tell the model not to cross those boundaries, but a prompt cannot create the boundary by itself. If a retrieval service has already searched a broader memory space, filtering the result before prompt assembly may protect the final answer, but the prohibited operation has still happened.

This changed how I thought about governance. Guidance, observability, and enforcement are different things. A policy becomes enforcement only when code at a real boundary prevents or changes the operation.

CCP therefore treats identity scope, surface sensitivity, conversation state, and policy as runtime inputs. They determine what context may be requested before retrieval begins. The goal is not to isolate every persona by default, but to make sharing deliberate rather than accidental.

## Conversations need temporary state

Persistent assistants also need to understand what is happening in the current interaction. The user may be answering a question, continuing a task, interrupting, confirming an action, changing topics, or returning after a long gap.

It is tempting to treat all of this as memory, but most of it is temporary. A confirmation may be valid for the next turn and meaningless later. An unanswered question may matter until another message interrupts it. A temporary focus of attention should not quietly become part of the user’s long-term profile.

This led to a separate runtime-state layer that tracks the immediate interaction: the current turn, relevant preceding messages, active work, pending confirmations, interruptions, and temporary policy.

Consider a simple example. The assistant asks, “Should I restart the server?” The user ignores the question and talks about something else. Later, the user says, “Yes.” A system that searches conversation history as a bag of text may connect that answer to the old restart question. A system that models temporal boundaries recognizes that the conversational relationship has expired.

The words are still in the transcript, but their meaning depends on order and adjacency. That is why current interaction state needs its own representation rather than being folded into durable memory.

## The system also needs a provisional view of the world

A persistent assistant eventually needs some representation of current reality: a server is unhealthy, a project is blocked, a reservation exists, a package is expected tomorrow, or a device is offline.

The system’s representation, however, is assembled from observations, external data, prior memory, and model interpretation. Some claims are fresh, some are stale, some conflict, and some were never verified. Treating all of them as ordinary prompt text makes those distinctions easy to lose.

CCP models world state as provisional. A useful claim should carry enough context to answer where it came from, when it was observed, how confident the system is, when it should expire, whether it requires verification, and whether newer evidence has replaced it.

This matters because language models are very good at turning partial information into coherent language. Coherence can make a tentative belief feel settled. The runtime has to preserve uncertainty even when the model can describe the situation smoothly.

## Answer quality depends on the relationship between claims and evidence

As the system gained memory and world state, another weakness became obvious: a model can produce the right answer for the wrong reasons, or a plausible answer with no reliable basis at all. The response may sound nearly identical either way.

That makes answer calibration a system concern rather than merely a writing style. The runtime should know whether an answer is based on direct evidence, retrieved memory, current external data, an inference, or general model knowledge. It should also know when sources conflict, when information is stale, and when the evidence does not support a confident conclusion.

The goal is not to attach a wall of citations to every casual sentence. It is to preserve the relationship between claim and evidence so the system can respond appropriately. Sometimes that means answering directly. Sometimes it means qualifying the answer, checking another source, or stating that the system does not know.

The model still generates the language. The control plane determines what level of confidence the available evidence permits.

## Tools made authority unavoidable

Memory and answers can be wrong without immediately changing the outside world. Actions are different.

Once an assistant can restart a service, send a message, alter a calendar, control a device, or trigger an external workflow, the architecture needs an explicit concept of authority. The fact that a model can call an endpoint does not mean it should decide when that endpoint is used.

A request may be ambiguous. The action may require confirmation. Conditions may change between proposal and execution. A tool may partially succeed, or execution may complete while verification remains unavailable. These are ordinary distributed-systems problems, but they become easy to obscure when everything is presented as one conversational exchange.

CCP gives actions a governed path:

1. interpret the user’s intent
2. determine whether the action is permitted
3. summarize the proposed effect
4. obtain confirmation when required
5. revalidate conditions before execution
6. perform the action at most once
7. verify the result when possible
8. report what actually happened, including partial or uncertain outcomes

The model can explain the action and help reason about it, while the surrounding runtime owns permission, confirmation, execution boundaries, and verification.

This also allows the system to represent failure honestly. An action may fail safely before execution, execute only partially, succeed without independent verification, or leave the final state uncertain. Those outcomes should remain distinct rather than being compressed into a confident success-or-failure sentence.

## Why I call it a control plane

In infrastructure, a control plane coordinates policy, state, permissions, and decisions across the components that perform the actual work. That is the role I want CCP to play around language models.

The model remains an important component. It contributes reasoning, interpretation, planning, and language generation. The surrounding system owns the more durable and consequential responsibilities: memory and provenance, runtime conversation state, provisional world state, persona and identity scope, privacy policy, model routing, answer calibration, action authority, and traceability.

This separation also makes the model replaceable. Hosted models will change, local models will improve, and different tasks will favor different providers. A personal AI system should not have to surrender its memory, identity, policies, and behavioral continuity every time the underlying model changes.

Ownership matters here as much as architecture. Many assistant products offer persistent memory, integrations, and personalization, but the most sensitive parts of the system often become inseparable from the service providing the model. The user’s history, identity, connected data, and accumulated context live inside someone else’s product boundary.

CCP is my attempt to keep the part that represents me private, inspectable, and portable while allowing the models and interfaces around it to evolve. I am not trying to rebuild every component of an AI assistant. I am trying to make sure the assistant does not become the owner of the person it represents.

## The model should not be the whole system

The simplest version of an LLM application often looks roughly like this:

```text
user message + conversation history + retrieved context + model = answer
```

That structure is useful for prototypes, but it is easy to mistake prompt assembly for architecture. As soon as the system becomes persistent, personalized, multi-surface, and capable of action, too many responsibilities begin to collapse into the prompt. Privacy becomes an instruction. Memory becomes whatever was retrieved. Current state becomes chat history. Truth becomes fluent text. Authority becomes tool availability.

CCP separates those concerns into explicit runtime components. The system decides what context is relevant and permitted, what state is temporary, what knowledge is durable, what evidence supports a claim, and what authority exists for an action. The model then receives a bounded problem to reason about.

This is not an argument against powerful models. It is an argument for placing them inside an environment that preserves the distinctions their language tends to blur.

## What CCP is becoming

CCP began as a personal architecture for durable memory, then expanded as each capability exposed another unanswered question. Memory raised questions about evidence and validity. Personas raised questions about privacy and identity. Continuous conversation required temporary state. External information required freshness and provenance. Tools introduced authority, confirmation, and verification.

The control plane emerged from connecting those boundaries into one runtime rather than handling each concern as another prompt instruction.

I still think of CCP as a personal system. The immediate goal is practical: a private AI stack that can support different personas and interfaces, remember responsibly, explain the basis of its answers, and perform bounded actions without making the model the sole authority.

The underlying problem is broader. As AI systems become persistent and capable of affecting the world, intelligence is no longer the only design challenge. They also need durable answers to questions about what may be remembered, what may be believed, what may be exposed, and what may be done.

Those decisions belong in the system around the model. That is why I am building a Cognitive Control Plane.
