# Microkernel Dispatcher Thought Experiment

TE ID: `TE-20260426-184246`  
TODO: `002.17`

## Decision under test

What if the kernel is really just a microkernel-style message dispatcher?

In that world:

- the kernel receives `grid([pCID, payload])`,
- chooses a handler,
- dispatches the message,
- and the only conversation between kernel and handlers is about resource usage,
  compensation, admission budgets, or throttling.

Then we must ask:

- does the richer `inspect / verify / execute` framing from `002.10` still
  belong in the kernel/handler boundary?
- or should `inspect` and `verify` become entirely handler-internal phases, with
  the kernel seeing only dispatch, resource usage, and outcomes?

## Grounding from earlier thought experiments

From `TE-20260425-162242-promises.md`:

- the kernel is intentionally minimal,
- handlers carry semantic knowledge,
- capability tokens and trust history matter more than universal cryptographic
  abstraction.

From `TE-20260426-022628-kernel-handler-abi.md`:

- the richer lifecycle model wants `inspect -> verify -> execute`,
- the kernel may want visibility into pre-execution gates,
- execution is a real admission boundary.

From `TE-20260426-180233-pcid-handler-routing.md`:

- the kernel is the local policy owner for handler choice,
- more than one handler may be eligible for a `pCID`,
- resource scarcity and promise history matter directly.

From `TE-20260426-184245-system-promises.md`:

- the kernel can enforce some local promises strongly,
- but many broader promises are social or economic and remain outside its direct
  control.

## Assumptions

- Handler selection has already happened by some local routing policy.
- The kernel remains policy-owning for resource use and local dispatch.
- Resource usage may be managed by throttling, quotas, or plural/promise-based
  compensation.
- The system still rejects single universal currency framing.
- The TE is about boundary shape, not a final implementation API.

## Alternatives

### A. Pure dispatcher

The kernel dispatches by `pCID` and selected handler identity. It meters
resources and nothing else. Semantic preflight is fully internal to the handler.

### B. Dispatcher + resource contract

The kernel still does not ask semantic questions, but it does require the
handler to negotiate or report resource budgets, compensation terms, throttling
state, and queue/admission cost.

### C. Dispatcher + minimal admission contract

The kernel and handler still avoid rich semantic dialogue, but the handler may
return one coarse preflight answer such as:

- cheap reject,
- estimated cost,
- or "not worth dispatching further."

This is more than pure resource accounting, but less than a full inspect/verify
contract.

### D. Richer lifecycle kernel

The kernel keeps the `inspect / verify / execute` model visible at its boundary,
as in `002.10`.

## Scenario analysis

## 1) Obviously bad or junk messages

The message is malformed or trivial junk after basic envelope routing.

Alternative A can work only if the handler pays the cost of discovering the junk
after dispatch. The kernel itself cannot ask for cheap semantic triage.

Alternative B improves accounting, not semantic gating. The kernel can meter the
waste, but not avoid dispatching it unless the handler is already penalized or
throttled enough to make dispatch unattractive.

Alternative C helps: one coarse preflight can cheaply reject junk without
exposing the full richer lifecycle.

Alternative D handles this cleanly through explicit inspect.

Observation: if the kernel wants early cheap discard based on payload facts, pure
dispatch is too blind.

## 2) Payload-contained signatures and capability claims

The message contains claims that matter to whether execution should occur at all.

Alternative A pushes all such checking into the handler after dispatch. This is
coherent if the kernel trusts the handler enough to let it perform internal
gating. In that model, `inspect` and `verify` still exist, but they are entirely
handler-private.

Alternative B adds resource terms, but not semantic visibility.

Alternative C can surface one coarse answer like "requires expensive
verification" or "cheaply rejectable," but cannot express the richer lifecycle
cleanly.

Alternative D keeps message-admission semantics visible to the kernel.

Observation: the real choice is not whether inspect/verify exist. It is whether
they are kernel-visible or handler-private.

## 3) Scarce local resources

The kernel must ration CPU, memory, storage, or I/O.

Alternative A is strong here if the kernel only cares about resource sovereignty.
It can dispatch conservatively, throttle aggressively, and judge handlers by cost
and outcomes without knowing protocol semantics.

Alternative B is stronger still because resource conversation is first-class.
Handlers can compensate, bid, or accept throttling under explicit local policy.

Alternative C is useful if the kernel wants one cheap signal before spending
more.

Alternative D also works, but now the kernel is learning more about message
semantics, not just resources.

Observation: resource scarcity is the best argument for a microkernel-style
dispatcher.

## 4) Multiple handlers for one `pCID`

There are several eligible handlers.

Alternative A can still function: the kernel chooses using routing policy and
then lets the chosen handler deal with message semantics internally.

Alternative B adds the possibility that handlers compete or are prioritized by
resource terms, quotas, or compensation offers.

Alternative C lets the kernel ask for one cheap preflight before committing to a
heavier handler.

Alternative D gives the kernel the richest cross-handler comparability, but at
the cost of a larger boundary.

Observation: routing competition does not by itself require kernel-visible
inspect/verify, but it does benefit from some cheap comparability.

## 5) Buggy or malicious handlers

A trusted handler can still misbehave.

Alternative A contains the kernel's exposure to resource and outcome metrics
only. That keeps the kernel simple, but makes semantic auditing weaker.

Alternative B gives more operational leverage because the kernel can revoke,
throttle, or deprioritize based on resource abuse and broken compensation terms.

Alternative C gives a little more visibility without embracing a rich semantic
ABI.

Alternative D gives the kernel the most visibility into handler-provided
pre-execution facts, but it also enlarges the surface that must stay stable and
honest.

Observation: microkernel purity is attractive if local trust is mostly about
resource and operational discipline rather than semantic introspection.

## 6) Relay or archival nodes

Some nodes only want to route, queue, or store, not deeply understand payloads.

Alternative A is very attractive here.

Alternative B is also attractive if storage/queueing cost must be compensated or
throttled.

Alternative D can be overkill for these nodes if they do not need semantic
admission beyond choosing a trusted handler.

Observation: the smaller the node's ambitions, the stronger the dispatcher model
looks.

## 7) Compensation vs throttling

Handlers might compensate the kernel for resource use, or the kernel might simply
throttle them.

Compensation is not required for a workable kernel. Throttling alone can be
enough if the kernel is purely local and policy-driven.

Compensation becomes interesting when:

- multiple handlers compete,
- resources are scarce,
- reciprocal service matters,
- or local policy wants costs to be partly externalized.

But plural-currency compensation still does not force the kernel itself to be an
exchange. The kernel can accept offers, ignore them, or defer comparison to
adjacent policy logic.

Observation: compensation is optional. Resource sovereignty does not depend on
having a market.

## What survives from `002.10`

`002.10` argued that the generic lifecycle should be
`inspect -> verify -> execute`.

This TE shows that there are two coherent interpretations:

### Interpretation 1: kernel-visible lifecycle

The kernel talks to handlers about inspect and verify before execution. This is
the richer ABI model.

### Interpretation 2: handler-private lifecycle

The handler still internally performs inspect and verify, but the kernel sees
only:

- dispatch,
- resource terms,
- throttling,
- completion/failure outcomes,
- and maybe one coarse preflight result.

So `002.10` is not simply wrong. It may have identified a real lifecycle while
placing too much of it at the kernel boundary for a microkernel design.

## What the kernel needs to know in the minimal model

If the microkernel dispatcher model wins, the kernel likely needs to know only:

- handler identity,
- local trust/eligibility,
- resource budgets,
- optional compensation or offer terms,
- queue/admission state,
- whether the handler accepts or rejects dispatch under current budgets,
- outcome/failure/resource reports after execution.

It does **not** need to know detailed message semantics.

## What the handler needs to own in the minimal model

The handler would own:

- payload inspection,
- payload verification,
- protocol meaning,
- internal admission logic,
- protocol-specific execution logic,
- and any finer-grained semantics of provider or capability claims.

## Conclusions

Rejected as the only model:

- **richer lifecycle kernel as the mandatory shape for every deployment**, because
  some nodes really do want a smaller dispatch-and-resource boundary;
- **pure dispatcher with no resource conversation at all**, because the kernel
  still needs resource sovereignty.

Surviving alternatives:

- **dispatcher + resource contract** survives strongly,
- **dispatcher + minimal admission contract** survives as a compromise,
- **richer lifecycle kernel** survives for deployments that want kernel-visible
  semantic gating.

Recommended conclusion:

- The strict microkernel design is coherent if the kernel/handler conversation is
  primarily about resource use, budgets, throttling, and optional compensation.
- In that design, `inspect` and `verify` should be treated as handler-private
  phases unless there is a strong reason to expose them at the kernel boundary.
- The kernel should not need a market to do this; throttling is sufficient, and
  compensation is optional and plural.
- The richer `002.10` model should therefore be treated as one possible boundary
  design, not as a mandatory universal kernel shape.

## Exact DF question that remains

Before kernel/handler APIs are locked, one DF choice should be answered:

- is v1's kernel/handler boundary:
  - **resource-only / microkernel-style**, with inspect and verify private to the
    handler, or
  - **kernel-visible lifecycle**, where inspect and verify are explicit boundary
    phases?

This TE recommends a bias toward the microkernel/resource-first model unless a
specific deployment truly needs kernel-visible semantic admission.

## Implications for open TODOs and DIs

- `002.10` should now be read as describing one candidate boundary model, not a
  settled universal architecture.
- `002.15` remains relevant either way, because the kernel still chooses handlers.
- `001.4` and `001.5` should not prematurely bake in kernel-visible inspect and
  verify unless the DF question above is resolved in that direction.
