---
layout: note
title: "How I use coding agents without outsourcing engineering judgment"
slug: coding-agents-judgment
date: 2026-11-15 16:00:00 +0100
last_modified: 2026-11-15
order: 1
category: Delivery
read_time: 5
description: "A practical workflow for using coding agents while keeping architecture, acceptance conditions, and final verification under human control."
homepage_description: "Agent-assisted delivery with explicit boundaries, acceptance conditions, and human-owned verification."
archive_description: "Where coding agents add leverage, which decisions remain human-owned, and why an agent's report is never the final proof."
deck: "Agents can increase throughput. They cannot own the definition of correct."
visual: agent-loop
source_prefix: "The delivery model described here is the same one summarized in"
source_label: "my working method"
source_url: "/#method"
cta_label: "Discuss a delivery workflow"
---

<p class="article-lead">A coding agent can inspect a repository, implement a bounded change, run tests, and return a plausible explanation in the time it takes a human to build the full context. That is useful leverage. It is also an easy way to produce the wrong change faster if nobody owns the boundary around the work.</p>

My workflow treats agents as execution capacity inside an engineering process—not as autonomous owners of the system. Architecture, acceptance conditions, and the final decision to ship remain human responsibilities.

## Frame the work before delegating it

An agent performs best when the task has a closed edge. Before delegation, I define the observable outcome, the relevant part of the system, the constraints that must remain true, and the proof required at the end.

“Improve the authentication code” is not a useful unit of work. “Reject expired sessions at the existing authorization boundary, preserve current responses for valid sessions, and prove both paths with the focused test suite” is much closer.

This framing does more than improve the prompt. It exposes uncertainty before code exists. If I cannot state who owns the invariant or how the result will be verified, the task is not ready to be parallelized.

<aside class="article-callout"><strong>The delegation rule</strong><span>Delegate execution only after the acceptance boundary is clear enough to review independently of the implementation.</span></aside>

## Parallelize independent evidence, not shared ownership

Agents are particularly useful for work that can progress independently: locating an existing pattern, tracing a call path, implementing a contained component, reviewing a diff, or running a focused verification pass.

The work becomes risky when multiple agents edit the same ownership boundary or make interacting architectural decisions in parallel. At that point, the apparent speed comes from postponing integration work. Conflicts are not limited to Git; two individually reasonable changes can encode incompatible assumptions.

I therefore keep the system-level decision sequential and parallelize bounded investigation or implementation beneath it. One owner retains the full constraint set and decides how the pieces fit.

## An agent report is a lead, not proof

“Implemented and all tests pass” is a useful status update. It is not acceptance evidence on its own.

The final pass starts from the diff and works outward:

- Does the change live at the layer that owns the rule?
- Did it preserve the surrounding behavior, including error paths?
- Are the tests capable of failing for the regression they claim to cover?
- Was the real integration boundary exercised where that boundary matters?
- Did the task introduce unrelated surface area, configuration, or dependencies?

The proof should match the risk. A copy change may need a rendered-page check. A persistence invariant needs the real database boundary. A public API change needs contract-level verification. The agent may run those checks, but I still inspect what was executed and what the result actually establishes.

## Keep context narrow enough to reason about

Giving an agent the entire repository is not the same as giving it the right context. Large undifferentiated context encourages locally plausible changes that miss the project’s actual conventions.

I start with the files and invariants that own the task, then expand only when the trace requires it. Existing patterns are preferable to a new abstraction. Explicit non-goals help prevent a small change from turning into a framework, provider layer, or generalized configuration system.

This is also why I separate investigation, implementation, and review when the risk justifies it. Each pass has a different question: what is true, what is the narrowest responsible change, and what evidence shows that behavior was preserved?

## The useful outcome is a tighter feedback loop

The value of agent-assisted delivery is not that code appears quickly. Code has never been the scarce final artifact. The useful outcome is a shorter loop between a precise requirement, an implementation, and evidence that the implementation satisfies it.

I do not publish a universal productivity multiplier for this workflow because I have not measured one against a controlled baseline. The defensible claim is narrower: bounded tasks can run concurrently, mechanical work consumes less attention, and more of the human review budget can stay focused on architecture, failure modes, and acceptance.

That is the contract I want a client to care about. The system is not shipped because an agent said it was done. It is shipped when the responsible engineer can explain what changed, why it belongs there, and what evidence makes the result trustworthy.
