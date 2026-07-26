---
name: intent
description: >
  Interview the user one question at a time to turn a vague idea into a clear,
  actionable intention statement. Outputs a structured intent brief suitable for
  feeding into /speckit.specify, superpowers:brainstorm, writing-plans, or any
  downstream tool that needs a WHAT and WHY before any HOW. Primary user is a
  product manager. Trigger: /sukiru:intent
---

# /sukiru:intent

An interview that extracts the minimum viable **intent** needed before any
planning, brainstorming, or specification work begins. The goal is to produce
a clean, business-readable statement of **WHAT** and **WHY** — never **HOW**.

This is the upstream of `/speckit.specify` and `superpowers:brainstorm`. It does
not produce a spec, a plan, or a technical design. It produces a statement of
*what is being sought and why it matters* — in language a non-engineer would
use.

---

## When this skill triggers

- User runs `/sukiru:intent`
- User has a vague direction ("I want to fix our onboarding", "We need better
  reporting", "Something's broken with X") and needs it sharpened
- User wants to brief a downstream tool (specify, brainstorm, plan) but isn't
  sure how to frame the input
- User says "let's figure out what we're actually trying to do"

---

## Interview protocol

### Opening

Greet with exactly this:

> I'll help you shape your intent into a clear statement. Let's start simple:
>
> **What would you like to change, add, or fix?**

Accept any level of response — a sentence, a paragraph, a brain dump. Never
judge the quality of the initial answer.

---

### Determine depth needed

After the opening response, assess:

- **Clear enough** → skip to synthesis (rare)
- **Missing actor or motivation** → ask the Actor question
- **Missing outcome or success signal** → ask the Outcome question
- **Scope is ambiguous** → ask the Boundary question

Ask **at most 3 follow-up questions**, one at a time. Do not ask all at once.
Stop asking when you have enough to synthesize. If a question can be reasonably
inferred, infer it and validate rather than ask.

---

### The question bank

Pick only the questions that fill genuine gaps. Order matters — ask in sequence,
don't skip ahead.

**1. Actor** (who experiences the change)

> Who is trying to do this, and what are they trying to accomplish right now
> that they can't?

**2. Outcome** (what success looks like, in observable terms)

> How would you know this is working? What changes for the person when it's done?

**3. Boundary** (scope containment — ask only if scope is genuinely unclear)

> What's explicitly out of scope for this? Is there anything you definitely
> don't want to touch?

**4. Constraint** (only ask if a hard constraint was hinted at but not stated)

> Is there a deadline, a dependency on another team or system, or a
> non-negotiable constraint I should capture?

---

### Synthesis

Once you have enough, produce the output — no preamble, no explanation. Just the
block below.

```
---
## Intent Statement

<2-5 sentence statement: who does what, why it matters, what gap is being closed>

### In scope
- <1-3 bullet items>

### Out of scope
- <1-3 bullet items>

### Success looks like
- <1-2 measurable outcomes>

### Constraints
- <if any; otherwise omit this section>
---
```

#### Intent statement rules

- Written for a non-technical reader (PM, stakeholder, executive)
- **WHAT** the user/group does + **WHY** it matters + the gap being closed
- No tech stack, no API names, no implementation detail
- No more than 5 sentences
- Present tense, declarative

#### After presenting the output

Ask once:

> Does this capture what you're after, or do you want to adjust anything
> before feeding it into a spec or brainstorm?

If they say it's good, remind them:

> You're ready. Copy the block above and paste it as your input when running
> `/speckit.specify`, `superpowers:brainstorm`, `writing-plans`, or any
> downstream planning tool.

---

## Quality checks before synthesizing

Before writing the output, verify:

- [ ] The actor is a person or role, not a system or technical component
- [ ] The outcome is observable (you can test whether it happened)
- [ ] No implementation detail has leaked in (framework, language, API, DB)
- [ ] The scope boundary is explicit enough that an agent won't over-build
- [ ] The statement could be read to a non-engineer and make complete sense

If any check fails, ask the relevant question rather than guessing.

---

## Anti-patterns to avoid

- **Don't ask all questions upfront** — one at a time, stop when you have enough
- **Don't paraphrase a PRD back at the user** — distill it, don't restate it
- **Don't let tech leak in** — if the user says "we need a Redis cache," strip it
  out and capture the underlying need ("they need fast reads for X")
- **Don't over-clarify** — 3 questions max; an imperfect intent that moves forward
  beats a perfect one that never gets written
- **Don't write a novel** — the output should be paste-ready in 10 seconds
- **Don't solve the problem** — you're clarifying intent, not designing the solution

---

## Example flow

```
User: /sukiru:intent

Agent: I'll help you shape your intent into a clear statement. Let's start simple:

What would you like to change, add, or fix?

---

User: When we process a document and it fails, nobody knows. We want some kind
of alerting.

Agent: Who needs to know when a document fails — is this for an internal ops
team monitoring the pipeline, or does the person who submitted the document
need to be notified?

---

User: Both, but the ops team is the priority. The submitter is a nice-to-have.

Agent: How would you know alerting is working? What does "notified" look like
for the ops team — a dashboard, a message somewhere, an email?

---

User: Slack message to a channel they already watch. Email is fine for submitters.

Agent: ---
## Intent Statement

When a document fails processing, the operations team currently has no
visibility unless they manually check. An automated notification should
alert the ops team immediately when a failure occurs, with enough context
to investigate or reprocess. Submitters should also receive a notification
so they're not left waiting on a document that silently failed.

### In scope
- Failure notifications to an ops Slack channel
- Optional submitter email on failure

### Out of scope
- Success notifications
- Retry logic
- Dashboard UI

### Success looks like
- Ops team is notified within 60 seconds of a failure
- Zero failures go undetected for more than 5 minutes during business hours
---

Does this capture what you're after, or do you want to adjust anything before
feeding it into a spec or brainstorm?
```

---

## Key design notes

- **This is NOT `/speckit.specify`** — it produces an *intent statement*, not a
  spec input. The output is a precursor. The user decides where to feed it next.
- **This is NOT implementation planning** — never ask "how" or "with what".
  If the user mentions a technology, ask "what need does that serve?" and move on.
- **Tolerance for ambiguity is higher** than `/speckit.specify` — the goal is
  directional clarity, not exhaustiveness. A good-enough intent that moves
  into a brainstorm or spec is better than a stalled perfect one.
