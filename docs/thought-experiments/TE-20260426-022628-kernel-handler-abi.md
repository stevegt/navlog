# Kernel/Handler ABI Thought Experiment for Payload-Only Signatures

TE ID: `TE-20260426-022628`  
TODO: `002.10`

## Decision under test

Pretend the kernel receives `grid([pCID, payload])`.

The kernel can always do trivial local checks first: parse the envelope, confirm
that `pCID` is known locally, enforce coarse size limits, and reject obviously
bad transport input. That part is not the question.

The real question starts after that: once `pCID` identifies a trusted local
handler family, what phases should exist before the message is fully admitted
into handler-controlled processing?

The original binary framing is:

- discard it
- process it via the handler specified by the `pCID`

That framing is too coarse once signatures move into payload. If the kernel
needs payload-specific facts in order to decide whether full processing is safe,
then it needs a read-only way to consult the handler before it commits to
execution.

So this TE asks:

- what is `inspect`?
- what is `verify`?
- what is `execute`?
- why are `inspect` and `verify` not the same thing?
- should the generic lifecycle be `inspect -> verify -> execute`, or can it be
  `verify -> execute`, or sometimes `inspect -> execute`?

## Grounding from earlier thought experiments

This TE builds on two earlier conclusions.

From `TE-20260425-162242-promises.md`:

- signatures can live inside payload,
- kernels are intentionally small,
- handlers are trusted but should not make promises on behalf of providers or
  the kernel's local policy,
- provider promises and revocable capabilities often require more than bare
  cryptographic validity.

From `TE-20260425-173644-hashing.md`:

- the envelope is primarily for routing,
- `pCID` identifies payload semantics,
- the kernel should not assume that envelope-level structure answers payload-level
  trust or execution questions.

Taken together, those two TEs imply that the kernel can route on `pCID`, but it
cannot safely jump straight from routing to execution for every protocol.

## Assumptions

- Signatures and attestations live inside payload.
- The kernel only runs handlers that it trusts, but trusted handlers can still
  be buggy, stale, or compromised.
- Mixed federations are normal. Some peers are more trusted than others.
- The kernel is intentionally small. It owns admission control, budgeting,
  scheduling, replay policy, and local trust policy.
- Handlers own payload semantics. They know how to parse, inspect, verify, and
  execute protocol-specific messages.
- Mark Burgess's Promise Theory rule applies: a handler may report facts about
  what it observed or verified, but it must not make promises on behalf of other
  agents or providers.
- A capability token is a provider promise. Revocation is real. "Signature
  verifies" is not the same as "provider will currently honor this."
- The ABI should be transport-neutral so the same conceptual contract can work
  in-process, out-of-process, or in a sandbox.

## Phase definitions

### Inspect

`inspect` is a read-only, handler-provided triage step. Its job is to tell the
kernel whether deeper work is justified.

`inspect` is intentionally cheap and bounded. It answers questions like:

- does this payload have the shape of something this protocol understands?
- what kind of operation does it claim to be?
- does it appear to carry signatures, capabilities, or other claims that will
  require verification before execution?
- what rough resource cost will verification or execution have?
- is there an obvious reason to discard this message now?

`inspect` is not for proving strong claims. It is for fast triage.

### Verify

`verify` is a stronger pre-execution gate. It exists because payload-contained
signatures and capabilities often require protocol-specific checking before the
kernel should admit execution.

`verify` is usually more expensive than `inspect`. Depending on the protocol, it
may be purely local, or it may involve protocol-specific dependency checks. This
TE does not lock one universal verification mechanism. It only treats `verify`
as a distinct phase whose purpose is to produce stronger admissibility facts than
`inspect` can provide cheaply.

In this pass, `verify` is still a pre-process gate: the kernel may discard after
`verify`.

### Execute

`execute` is any post-admission handler work. It is what happens after the
kernel decides the message should actually be processed by the handler.

`execute` is intentionally broader than "side effects." It may include read-only
post-admission work, queueing, state transitions, writes, or provider-facing
actions. The key point is that `execute` is no longer just evidence gathering.
It is the handler carrying out the protocol's actual work.

## Why `inspect` and `verify` are not the same

They differ in purpose.

- `inspect` answers: "is this message even worth spending more on?"
- `verify` answers: "have I gathered strong enough evidence to admit execution?"

If the kernel skips `inspect`, then every dubious message pays the cost of
verification just to discover it was obviously discardable. That weakens the
kernel's resource-control position.

If the kernel skips `verify`, then moving signatures into payload loses much of
its value. The kernel has no strong protocol-specific gate before execution, and
payload-contained claims become too easy to treat as mere syntax.

So `inspect` and `verify` are not duplicates. One is cheap triage. The other is
stronger pre-execution checking.

## Why `inspect` and `verify` are not the only phases

They are not the only phases. `execute` is distinct and should stay distinct.

The old TE overemphasized the read-only boundary and under-described what the
kernel is actually deciding. The kernel is not merely deciding whether to spend
more budget on introspection. It is deciding whether to admit the message into
handler-controlled processing.

That means a lifecycle model with only `inspect` and `verify` is incomplete.
Once the kernel has enough evidence, something different happens: the handler is
allowed to do actual work. That is `execute`.

## Lifecycle alternatives

### A. `inspect -> verify -> execute`

The kernel first asks for cheap triage, then asks for stronger checks if needed,
then admits execution.

### B. `verify -> execute`

The kernel skips cheap triage and uses verification as the first meaningful
handler consultation.

### C. `inspect -> execute`

The kernel performs cheap triage and then admits execution without a distinct
verification phase.

### D. single-call `process`

The kernel gives the handler the message once and the handler internally decides
how much to inspect, verify, and execute before returning.

## Scenario analysis

## 1) Obvious garbage or low-value spam

The payload is malformed, oversized for the protocol, or syntactically wrong in
a way the handler can spot cheaply.

Alternative A works well. `inspect` can reject early without spending verification
budget.

Alternative B is wasteful. The kernel must pay for a heavier path even though
the message was obviously not worth it.

Alternative C also works for early rejection, but only because `inspect` can
discard before execution. It says nothing about cases that need stronger checks.

Alternative D hides too much from the kernel. The kernel loses visibility into
whether it paid a cheap reject cost or a heavy verification cost.

Observation: a cheap inspect phase is valuable even if many messages later need
verification.

## 2) Payload carries signatures that matter to admissibility

The message's meaningful claims live inside payload. The kernel cannot evaluate
them from the envelope alone.

Alternative A fits this case directly. `inspect` can tell the kernel that
verification-bearing claims are present, and `verify` can then decide whether
execution should be admitted.

Alternative B can still work, but it makes every such message pay verification
cost immediately, even when a cheap inspect could have rejected malformed or
irrelevant inputs.

Alternative C is too weak as the generic model. If signatures moved into
payload precisely so that protocol-specific verification can happen there, then
`inspect -> execute` throws away that stronger gate.

Alternative D again hides the kernel's cost and gating logic inside a single
opaque call.

Observation: payload-contained signatures strongly argue for a distinct verify
phase before generic execution.

## 3) Same-federation routine traffic

The sender is transport-trusted and the message is routine. The kernel wants to
stay cheap and fast.

Alternative A still behaves well. Inspect can quickly confirm that the message
looks routine and can report whether verify is actually needed for this protocol
operation.

Alternative B may be acceptable for a protocol that has no meaningful cheap
inspect path. But as the generic model it is too eager to spend verification
budget.

Alternative C is tempting here. For low-risk operations with no verification-
bearing claims, `inspect -> execute` may be acceptable as a constrained special
case. But that is not enough to make it the generic cross-protocol model.

Observation: same-federation routine traffic argues for allowing some protocols
to make verify unnecessary, but not for removing inspect.

## 4) Revocable provider promises and capability tokens

The message presents a capability token or other provider promise whose current
honor status may differ from its offline cryptographic validity.

Alternative A gives the kernel the most control. Inspect can cheaply identify
that provider-dependent claims are present. Verify can then return stronger
statuses such as "locally valid," "dependency unresolved," or "provider state
required" before execution is admitted.

Alternative B can work, but only by making verify the first expensive step for
every such message.

Alternative C is too weak in the generic case. Inspect alone should not be
treated as a substitute for provider- or signature-related checks.

Observation: provider promises are a strong argument against `inspect -> execute`
as the default lifecycle.

## 5) Relay or archival node

Some nodes want minimal semantic cost. They may need to reject junk, bucket
messages, or delay stronger work.

Alternative A is flexible. The node can stop after inspect when all it wants is
triage, queueing, or coarse policy.

Alternative B is less attractive because it forces a stronger check even when
the node only needed to decide whether the message was worth keeping around.

Alternative C can work only for protocols where inspect already provides enough
for that node's local policy and no stronger admissibility claim is needed.

Observation: relay and archival behavior strengthens the case for a distinct
inspect phase that stands on its own.

## 6) Mixed-version evolution and handler diversity

Different handlers will have different abilities. Some may support a meaningful
cheap inspect phase; others may only know how to do a heavier verify.

Alternative A is still the best generic target because it exposes the desirable
split clearly.

Alternative B should survive as a fallback for protocols that genuinely have no
useful inspect path.

Alternative C should survive only as an explicit protocol-local special case:
no verification-bearing claims, and local policy agrees that inspect is enough.

Alternative D remains too opaque for a small kernel.

Observation: the kernel should prefer `inspect -> verify -> execute`, but it may
need a fallback for verify-only handlers.

## What `inspect` should return

`inspect` should return cheap triage facts, not policy decisions.

At minimum it should report:

- parse status (`complete`, `partial`, `invalid`, `unsupported`, `truncated`),
- claimed operation kind,
- claimed principals / issuers / token references seen syntactically,
- whether the payload appears to contain verification-bearing claims,
- whether the protocol believes verify is unnecessary, advisable, or required
  before generic execution,
- obvious discard reasons detected cheaply,
- rough cost estimate for verify and execute,
- replay or freshness hints that are cheap to observe,
- optional handler-local detail blob for protocol-specific diagnostics.

This is enough for the kernel to ask: discard now, stop after inspect, or spend
more on verify?

## What `verify` should return

`verify` should return stronger pre-execution facts.

At minimum it should report:

- verification result per relevant claim or token,
- whether execution preconditions are satisfied, unresolved, or failed from the
  protocol's point of view,
- whether any provider-dependent claim remains conditional,
- actual resources spent,
- dependencies consulted, if any,
- replay-relevant identifiers or freshness outcomes that are stronger than the
  cheap inspect hints,
- optional handler-local detail blob for protocol-specific diagnostics.

`verify` still should not tell the kernel to "admit" or "trust" a message. It
should report protocol facts. The kernel owns policy.

## What `execute` means for the ABI

`execute` is not just another introspection phase. It is the point where the
handler is allowed to carry out the protocol's actual work.

For this TE, that means:

- `execute` should not be conflated with `verify`,
- the kernel should know whether it is still gathering evidence or has crossed
  into admitted processing,
- and the generic kernel/handler contract should keep the read-only gates
  (`inspect`, `verify`) conceptually separate from `execute`.

## Conclusions

Rejected as the generic model:

- **single-call `process`**: too opaque for a small kernel that wants to meter
  cost and reason about discard vs admit decisions.
- **`inspect -> execute` as the generic model**: too weak once signatures and
  capability-bearing claims move into payload.

Surviving alternatives:

- **`inspect -> verify -> execute`** survives as the best generic model.
- **`verify -> execute`** survives as a fallback for protocols that do not have a
  meaningful cheap inspect path.
- **`inspect -> execute`** survives only as a constrained protocol-local special
  case where no stronger verification-bearing claims exist and local policy
  explicitly accepts that tradeoff.

Recommended conclusion:

- The generic kernel lifecycle should be `inspect -> verify -> execute`.
- `inspect` and `verify` are different because they answer different kernel
  questions: "is deeper work justified?" vs "is execution admissible?"
- `execute` must remain explicit because it is the admission boundary where the
  handler stops merely describing the message and starts doing the protocol's
  work.
- The kernel should prefer handlers that can provide cheap inspect data, but it
  may need to support verify-first handlers as a fallback.

## Exact DF question that remains

Before any code or Go interfaces are locked, one DF choice still remains:

- Must every handler implement a distinct `inspect` phase, or may a handler
  declare that it has no meaningful cheap inspect path and therefore uses
  `verify -> execute`?

This TE recommends treating `inspect -> verify -> execute` as the target model,
while allowing `verify -> execute` only as an explicit fallback if needed.

## Implications for open TODOs and DIs

- `002.9` should compare signature placement using this same lifecycle framing:
  moving signatures into payload increases the need for a handler-visible verify
  phase before generic execution.
- `002.11` should treat handler report/version compatibility separately for
  inspect, verify, and execute rather than assuming one opaque process call.
- `001.4` and `001.5` should avoid baking in a single-call handler API shape.
- The earlier DF question about deferred-status-only versus resumable
  continuation becomes secondary and should be revisited only after the
  inspect/verify/execute split is locked.
