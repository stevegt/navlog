# Kernel/Handler ABI Thought Experiment for Payload-Only Signatures

TE ID: `TE-20260426-022628`  
TODO: `002.10`

## Decision under test

If signatures live only in payload, what kernel/handler ABI best supports
resource allocation, spam filtering, capability checks, and long-horizon
evolution while keeping the kernel small?

The boundary under test is not a Go interface or a wire format yet. It is the
semantic contract between a microkernel-like PromiseGrid kernel and trusted
protocol handlers.

## Assumptions

- The outer envelope is used for routing to a local pCID handler; signatures and
  attestations live inside payload.
- A kernel only runs handlers that it trusts, but trusted handlers can still be
  buggy, stale, or compromised.
- A kernel can trust some peers and distrust others; mixed federations are the
  normal case, not an edge case.
- The kernel is intentionally small. It owns admission control, budget
  allocation, scheduling, storage decisions, and local trust policy.
- Handlers own payload semantics. They parse payloads, understand signature
  schemes, and understand protocol-specific capability/token structures.
- Mark Burgess's Promise Theory rule applies: an agent must not make promises on
  behalf of another agent. A handler may report what it observed or verified,
  but it must not claim that a remote provider will honor a token unless the
  provider itself has just made that promise to the kernel.
- Capability tokens are provider promises. Revocation is real. Offline
  cryptographic validity is not the same as current provider willingness.
- The ABI should be transport-neutral so the same contract can be used for
  in-process calls, subprocesses, message passing, or sandboxed runtimes.

## Alternatives

### A. Single-call verdict ABI

The kernel gives the handler a message and a budget. The handler parses,
verifies, and returns one final summary.

### B. Two-phase inspect/verify ABI

The kernel first asks for a cheap bounded inspection result. It then chooses
whether to spend more budget on deeper verification.

### C. Continuation/dependency ABI

The handler may return "needs provider/dependency/freshness check" together with
state needed to resume later.

### D. Policy-delegating ABI

The handler returns not only observations but also routing, quota, trust, or
admission decisions for the kernel to enforce.

## Scenario analysis

## 1) Same-federation routine traffic

A pilot, airport, or FBO sends a normal message within one federation. The
sender is already transport-trusted. The kernel mostly wants cheap scheduling
and logging decisions.

Alternative A works, but it spends verification effort too early. A store-only
node or low-priority relay still pays full parse/verify cost just to learn the
message is probably fine.

Alternative B fits the microkernel goal better. Inspect can cheaply identify the
protocol, claimed principals, replay tokens, rough resource demand, and whether
full verification is even useful for this node.

Alternative C adds value only when the message depends on external freshness or
slow checks. For routine same-federation traffic it is unnecessary overhead as a
primary model.

Alternative D is unnecessary here and expands trust in handlers too far. The
kernel already owns local budget and routing policy.

Observation: routine traffic argues for a cheap first phase, not a mandatory
full-verification path.

## 2) Cross-federation edge with partial trust

A message arrives from a federation that is transport-trusted enough to accept
the connection, but not trusted enough to grant local resources purely on
transport identity.

Alternative A can verify deep claims, but it collapses all useful distinctions
into one expensive call. The kernel learns too late whether the handler needed
remote dependencies, whether identity is merely claimed, and how much cost was
spent to reach the verdict.

Alternative B lets the kernel ask first: who is claimed, what is locally
verifiable, what is externally dependent, and what is the likely cost of deeper
checks? That lets the kernel decide whether this federation gets more CPU, more
queue depth, or only cold storage.

Alternative C matters here because cross-federation capability checks often
depend on provider freshness, revocation, or local federation policy. The kernel
needs an explicit way to distinguish "cryptographically intact" from
"currently honored by provider."

Alternative D is wrong for the Promise Theory constraint. A handler may say
"provider X issued token Y and signature Z verified" or "provider freshness is
unknown," but it must not say "admit this message because provider X will honor
it." That would be the handler making a promise on behalf of provider X and on
behalf of the kernel's local policy.

Observation: mixed federations require the handler to return conditional facts
and dependency states, not final policy.

## 3) Replay or spam using expensive verification paths

An attacker, or a peer that broke a promise, sends traffic designed to trigger
expensive crypto, large partial parses, or repeated revocation lookups.

Alternative A is weakest here. The handler either burns budget to reach a final
answer or returns a shallow answer that mixes "not yet verified" with "invalid."
The kernel cannot easily meter verification separately from initial triage.

Alternative B is strongest. Inspect can be bounded tightly: limit bytes parsed,
nesting depth, signatures examined, and CPU time. The inspect report can say
what was observed, whether parsing was truncated, and the projected cost of
verification. The kernel can then decide whether the sender or federation has
earned that spend.

Alternative C remains useful as a result state. If verification requires online
provider checks, the handler can say so without consuming unbounded time.

Alternative D again fails because it blurs observation with policy. The kernel
needs facts and costs so it can enforce its own anti-spam promises.

Observation: resource triage strongly favors a split between cheap observation
and expensive verification.

## 4) Revocable provider promises and offline dependencies

A capability token claims access to a runway note, airport dataset, ATC slot, or
other provider-controlled resource. The token verifies cryptographically, but
the provider may have revoked it or may be offline.

Alternative A encourages overclaiming. A single final verdict tends to compress
"signature valid, provider offline" and "provider confirmed" into one status
unless the ABI is already rich enough to express dependency states.

Alternative B helps, but only if verify results can report conditional outcomes.
Inspect may notice token issuer, token id, expiry hints, and the fact that
online freshness is required. Verify may then either confirm with the provider
or return a dependency status.

Alternative C is essential here, but not as an entirely separate architecture.
It is better modeled as a verify outcome: "provider-unreachable," "revocation
unknown," "deferred," or "requires provider X query."

Alternative D is the most dangerous in this scenario. If the handler returns a
policy verdict such as "authorized," it risks speaking for the provider. That
violates the Promise Theory constraint unless the provider itself was queried and
the result is explicitly time-bounded and attributable.

Observation: provider-dependent truths must stay conditional in the ABI. The
kernel needs to see the condition, not just a flattened authorization verdict.

## 5) Mixed-version evolution over decades

Kernels and handlers will evolve at different speeds. Some handlers will know
new signature schemes or new token structures before others.

Alternative A makes version skew harder to reason about because there is only
one coarse call result. Unknown fields, partial support, and degraded operation
become ambiguous.

Alternative B provides a cleaner compatibility story. Older kernels can still
use inspect results and avoid verify if the handler reports unsupported or
unknown verification features. Newer handlers can expose more detail without
forcing older kernels to understand every verification path.

Alternative C helps with evolution if continuation/dependency results are
represented explicitly and conservatively. A kernel that does not understand a
future dependency type can still treat it as unresolved and limit resources.

Alternative D is brittle under version skew because policy meanings change over
time and are hard to transport safely across kernel versions.

Observation: a long-lived ABI should version fact-reporting, not delegate policy
semantics.

## 6) Buggy or compromised trusted handler

The kernel trusts installed handlers, but trust can be broken by bugs,
compromise, or stale logic.

Alternative A gives too much leverage to one opaque result. If the handler says
"accept" or "verified," the kernel has little structured context to sanity-check
the claim.

Alternative B limits damage because the kernel sees more structured intermediate
facts: parse coverage, claimed principals, verification status per claim, and
actual resource use. That still does not make the handler untrusted, but it does
reduce blind authority.

Alternative C is acceptable only if dependency and continuation results remain
descriptive, not imperative.

Alternative D should be rejected outright in this world. A small kernel should
not outsource local policy decisions to protocol handlers.

Observation: even with trusted handlers, the ABI should minimize the amount of
kernel policy authority delegated across the boundary.

## 7) Relay or archival node with minimal semantic work

Some nodes want to forward, queue, or archive messages with minimal semantic
cost. They still need spam resistance and some replay/freshness hints.

Alternative A forces them to pay for full verification or accept a weak,
all-or-nothing summary.

Alternative B gives them exactly what they need: bounded inspection plus the
option to stop there. They can record claimed principals, replay keys, and rough
cost without spending deep verification budget.

Alternative C is useful as a no-op path: the handler can report that stronger
claims require provider interaction, and the relay can decline to do that work.

Alternative D again overreaches because relays need local policy control.

Observation: relay and archival nodes strengthen the case for a cheap inspect
phase that stands on its own.

## Derived report contract

The scenario analysis rejects policy delegation and rejects a mandatory
single-call verdict as the only model. The surviving base model is:

- a transport-neutral two-phase ABI,
- with inspect and verify phases,
- where verify may return conditional dependency states instead of pretending
  provider-dependent truth is settled,
- and where the kernel remains the only component that makes local policy
  decisions.

The minimal report contract should therefore carry facts, costs, and conditions.

### Inspect report

Inspect must be cheap and tightly bounded. It should report:

- report schema/version identifier,
- pCID / handler identity / handler version,
- parse status (`complete`, `partial`, `truncated`, `unsupported`, `invalid`),
- bytes examined and any explicit truncation reason,
- claimed principals and token issuers observed so far,
- replay/freshness hints observed cheaply (nonce, sequence, token id, expiry
  hint, message-local correlation key),
- estimated cost of deeper verification,
- dependencies required for stronger claims (provider lookup, key fetch,
  federation mapping, revocation query),
- optional opaque handler detail for protocol-local use.

Inspect should not claim that a principal is verified unless that verification is
part of the inspect budget and is labeled as such.

### Verify report

Verify consumes an explicit kernel-granted budget and should report:

- all inspect facts needed for correlation,
- verification result per claim or token, not just one global verdict,
- statuses such as `verified`, `unverified`, `unsupported`, `deferred`,
  `revoked`, `provider-unreachable`, `dependency-missing`, and `expired`,
- actual resources spent (time, bytes, external queries, signatures checked),
- which dependencies were consulted and with what outcome,
- whether the result is locally final or conditional on provider freshness,
- optional replay disposition inputs (for example, a stable replay key and the
  confidence level that it is safe to use).

Verify should still avoid policy words such as "admit", "route", "trust", or
"allocate". Those are kernel decisions. The handler may return evidence that the
kernel can use to make those decisions.

### Optional continuation state

The ABI does not need a third mandatory phase, but it should allow verify to
return resumable state when provider interaction or long-running checks are
deferred. That state should be descriptive and bounded:

- what is still needed,
- what was already verified,
- what deadline or freshness window applies,
- what budget was already consumed.

That keeps continuation as an extension of verify rather than a separate,
always-present architecture.

## Kernel and handler responsibilities

### Kernel responsibilities

- Route payloads to handlers by pCID.
- Set inspect and verify budgets separately.
- Decide whether to stop after inspect or spend more on verify.
- Own local admission, scheduling, quota, cache, replay, and trust policy.
- Treat provider-dependent claims as conditional unless the report says the
  provider was actually consulted and the freshness bounds are explicit.

### Handler responsibilities

- Parse payload according to protocol rules.
- Report observed and verified facts within the granted budget.
- Surface conditions, dependencies, and uncertainty explicitly.
- Never make promises on behalf of providers, peers, or the kernel's local
  policy engine.

## Conclusions

Rejected alternatives:

- **D. Policy-delegating ABI** is rejected. It violates the Promise Theory
  constraint and makes the kernel too large in the wrong direction by turning
  protocol handlers into policy authorities.
- **A. Single-call verdict ABI as the only contract** is rejected. It hides cost,
  collapses conditional truths, and prevents the kernel from making fine-grained
  resource decisions.

Surviving alternatives:

- **B. Two-phase inspect/verify ABI** survives as the base model.
- **C. Continuation/dependency results** survive as an extension of verify, not
  as a separate primary architecture.

Recommended conclusion:

- The kernel/handler boundary should be a small, transport-neutral, fact-reporting
  ABI with separate inspect and verify phases.
- The handler should report observations, verification status per claim, budget
  use, and unresolved dependencies.
- The kernel should keep all resource allocation, admission, and trust policy
  decisions local.

## Exact DF question that remains

Before any code or Go interfaces are locked, one DF choice still remains:

- Should the first implementation treat dependency handling as:
  - a verify result with inline deferred statuses only, or
  - a resumable verify flow with an explicit continuation token/state object?

The TE supports both, but recommends starting with inline deferred statuses
unless concrete use cases require resumable long-running checks immediately.

## Implications for open TODOs and DIs

- `002.9` should compare signature placement using the same rule: handlers may
  surface facts to the kernel, but they should not own kernel policy.
- `002.11` should treat the report schema/version as the long-horizon
  compatibility boundary, not any one runtime binding.
- `001.4` and `001.5` should not lock handler APIs or storage shapes that assume
  a single-call verdict model.
- A future DI should lock the post-TE DF choice for deferred-status-only versus
  resumable continuation.
