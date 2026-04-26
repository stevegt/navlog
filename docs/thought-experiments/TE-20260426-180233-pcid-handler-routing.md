# pCID Handler Routing Thought Experiment

TE ID: `TE-20260426-180233`  
TODO: `002.15`

## Decision under test

Pretend the kernel receives `grid([pCID, payload])`.

The earlier lifecycle TE answered a later question: once the kernel has chosen a
handler for a given `pCID`, what is the right pre-execution flow? That TE gave a
generic answer: prefer `inspect -> verify -> execute`, with `verify -> execute`
as a fallback.

This TE asks an earlier question:

- where does the kernel get the `pCID -> handler` routing entry?
- may more than one handler claim the same `pCID`?
- if so, how does the kernel choose which handler to use?
- should promise-keeping history, capability promises, or plural currencies
  influence that choice?
- if an exchange or market exists, should the kernel itself be that exchange?

## Grounding from earlier thought experiments

From `TE-20260425-173644-hashing.md`:

- `pCID` is part of routing and protocol selection, not a complete trust answer.
- the envelope can tell the kernel what protocol family is claimed, but not by
  itself which local handler should be entrusted to run it.

From `TE-20260425-162242-promises.md`:

- the kernel is intentionally small,
- handlers are trusted, but trust is built and revised through promises kept and
  broken,
- capability tokens are provider promises, not magic truth,
- local policy and trust history matter more than cryptographic universalism.

From `TE-20260426-022628-kernel-handler-abi.md`:

- once a handler is selected, the kernel still wants explicit lifecycle phases,
- execution is a real admission boundary,
- the kernel should avoid opaque single-call dispatch.

Taken together, those TEs imply that `pCID` should not directly select one
handler by fiat. The kernel needs a local model for which handlers are eligible
to serve a `pCID`, and how one gets picked.

## Assumptions

- A kernel runs only handlers it is willing to trust locally.
- A handler may promise that it can serve one or more `pCID`s, but that promise
  is not self-authenticating.
- Trust can grow or shrink based on kept/broken promise history.
- The kernel remains small and policy-owning even if it consults richer data.
- Cross-federation import of claims is allowed in principle.
- Any currency/economy model in this TE is plural and promise-based, not a
  single universal coin.

## What a routing entry means

This TE treats a routing entry as more than "pointer from `pCID` to executable."

At minimum, a useful routing entry needs to answer:

- which handler is claiming capability for this `pCID`?
- who said so: the handler itself, the operator, or an imported registry?
- what local trust or promise history backs that claim?
- what costs, constraints, or capability terms apply if this handler is chosen?
- is this one candidate among several, or the only accepted claimant?

So the real design problem is not "which table format?" It is "what kind of
claim-set does the kernel maintain per `pCID`, and how sovereign is the kernel
in deciding among those claims?"

## Alternatives

### A. Kernel-curated routing table

Only the kernel or operator may create `pCID -> handler` entries. Handlers do
not self-register. There is one authoritative local mapping.

### B. Self-registration with single accepted winner

Handlers may register themselves for a `pCID`, but the kernel accepts only one
active handler per `pCID`.

### C. Self-registration with multiple accepted claimants

Handlers may register themselves for a `pCID`, and the kernel may keep more than
one accepted claimant. Selection happens at dispatch time or during periodic
evaluation.

### D. Imported or federated routing claims

The kernel may accept routing claims from trusted registries, bundles, peers, or
other federated sources rather than relying only on local handler assertions.

### E. Plural-currency / promise-economy selection

Handlers may advertise not just "I can handle `pCID`" but also terms expressed
in promises, capabilities, quotas, or personal currencies. The kernel, or some
adjacent matcher, uses those terms plus promise history to pick a handler.

## Kernel roles to compare

### 1. Kernel as policy owner

The kernel is the authoritative local chooser. It may consider claims,
registries, history, or offers, but it owns the final selection logic.

### 2. Kernel as minimal registrar

The kernel stores which handlers claimed which `pCID`s, but selection is mostly
outside the kernel or hardcoded elsewhere.

### 3. Kernel as exchange or matcher

The kernel actively matches handlers, currencies, and promises, potentially
clearing trades among competing offers.

## Non-alternative: single universal coin

This TE does not treat "one blockchain coin decides everything" as a serious
design direction.

That model is rejected as a default simplification because:

- it destroys the local, plural, promise-based nature of the system,
- it pretends incomparable promises can be priced in one scalar,
- it encourages external consensus machinery where local kernel sovereignty is
  the real issue,
- and it turns the kernel into finance theater instead of a policy-owning
  dispatcher.

## Scenario analysis

## 1) Bootstrap: first handler for a new `pCID`

A new handler arrives and claims it can serve `pCID-X`.

Alternative A is simple: the operator or kernel must manually install the
mapping. This is conservative, but slow and rigid.

Alternative B is more dynamic: the handler can offer the claim itself, but the
kernel still picks a single accepted winner. That gives some automation without
opening immediate multi-claim competition.

Alternative C also works, but its full value only appears once there are
multiple claimants.

Alternative D helps if trusted registries or bundles already exist, but bootstrap
still requires the kernel to decide whether to trust that external source.

Alternative E is weak at bootstrap if there is no existing market context or
promise history. The kernel still needs some base trust decision.

Observation: bootstrap requires kernel sovereignty. No market or remote registry
removes the need for a local acceptance decision.

## 2) Two handlers claim the same `pCID`

Now there is direct competition or redundancy.

Alternative A avoids the problem by construction, but at the cost of flexibility.
Whichever mapping the operator curates wins, and the kernel loses the ability to
compare live alternatives.

Alternative B also avoids ambiguity at runtime, but it forces an early collapse
to one winner. That makes rollback, fallback, and comparative trust growth more
difficult.

Alternative C is the first model that treats this as a normal situation. The
kernel can keep multiple accepted claimants and choose among them locally.

Alternative D can enlarge the candidate pool, but also raises the risk that the
kernel inherits ambiguous or conflicting claims from outside.

Alternative E is interesting here: competing handlers can offer different terms,
budgets, or reciprocal promises. But that only helps if the kernel can interpret
those terms without surrendering policy.

Observation: if competition, redundancy, or specialization matter, the kernel
should support multiple accepted claimants per `pCID`.

## 3) One handler is more trusted, another is cheaper

Handler A has a better promise-keeping record. Handler B is faster, cheaper, or
asks for fewer resources.

Alternative A can express this only through operator curation. It is stable, but
not very adaptive.

Alternative B forces the kernel to collapse these tradeoffs into one accepted
winner up front. That may be too early if context matters.

Alternative C allows the kernel to keep both and choose based on local policy:
trust-first, cost-first, safety-first, or different policies per environment.

Alternative D is not enough by itself; imported claims do not solve local
tradeoffs.

Alternative E makes this scenario richer. Different handlers can offer distinct
terms, including currencies or capability promises, but now the kernel must ask
whether these offers are commensurable at all.

Observation: multi-claim routing plus local policy is more robust than a forced
single winner. Economy-like terms may help, but they do not remove the need for
policy.

## 4) A handler breaks promises or goes stale

A previously trusted handler misbehaves, fails verification, exhausts resources,
or stops tracking the protocol correctly.

Alternative A can recover only if the operator changes the routing table. This is
safe but slow.

Alternative B lets the kernel revoke one winner and maybe replace it, but only
if another registered claimant is available or can be activated quickly.

Alternative C is strongest here. The kernel can downgrade one claimant while
keeping others available.

Alternative D helps only if imported claims are still subject to local trust
revision.

Alternative E may incorporate reputation, debt, or broken-promise penalties, but
that still depends on the kernel treating those signals as advisory inputs to its
own choice.

Observation: promise history matters most when the kernel has multiple candidate
handlers to compare or fail over to.

## 5) Scarce local resources force prioritization

CPU, memory, storage, or scheduling budget is tight. The kernel cannot give every
claimed handler equal opportunity.

Alternative A can prioritize through static operator policy, but it has little
adaptivity.

Alternative B can encode one chosen winner and avoid scheduling complexity, but
it loses flexibility under changing conditions.

Alternative C lets the kernel choose different handlers for different loads or
contexts, if that complexity is acceptable.

Alternative E becomes relevant here. A plural-currency or capability-offer model
can express "I will spend my own budget," "I offer reciprocal service," or "I am
willing to accept lower priority." But these are not scalar truths. They are
terms that the kernel may or may not value.

Observation: local scarcity strengthens the case for kernel-owned policy over a
minimal registrar. Some form of comparative evaluation is necessary.

## 6) Cross-federation import of routing claims

A trusted federation or registry says that handler H can serve `pCID-X`.

Alternative A rejects this unless the operator manually imports it.

Alternative B can accept it by turning the imported claim into a single winner,
but that is brittle if local experience later diverges.

Alternative C works better: imported claims can become candidates rather than
authoritative winners.

Alternative D is the model that normalizes this source type, but it only works
well if imported claims remain claims, not commands.

Alternative E can add terms or reciprocal promises across federations, but that
increases complexity even further.

Observation: imported routing claims should enter the kernel as candidate
evidence, not as forced dispatch commands.

## 7) Plural-currency offers are not directly comparable

Handler A offers one kind of promise token. Handler B offers another. There is no
universal exchange rate. The kernel must decide what to do.

Alternative A avoids the problem by not having an economy at all.

Alternative B and C can still function if the kernel ignores currencies entirely
and uses local trust and policy.

Alternative E is the stress test. If the kernel is the exchange, then it must
maintain rules for valuing incomparable promises, or admit that some offers
cannot be ordered mechanically.

The clean promise-theory answer is not "invent one universal number." It is:

- local policy may define partial orderings,
- some offers may be incommensurable,
- some currencies may be accepted only from certain peers,
- and the kernel may refuse to compare offers it does not understand.

Observation: plural-currency matching is possible, but only if the kernel keeps
the right to say "these offers are not comparable under my current policy."

## 8) Operator override and local sovereignty

The operator wants to pin one handler regardless of history, imported claims, or
market offers.

Alternative A handles this directly.

Alternative B and C can also handle it if kernel policy is clearly above
registration and history.

Alternative D fails if imported registries are treated as stronger than local
policy.

Alternative E fails if exchange logic is allowed to overrule operator intent.

Observation: the kernel must remain policy-sovereign even if it uses registries,
history, or exchange-like mechanisms.

## What survives

The scenario analysis rejects two extremes:

- a purely operator-curated table as the only model, because it is too rigid for
  redundancy, competition, and adaptive trust;
- and a kernel-core exchange that tries to clear all handler choice through one
  market abstraction, because it makes the kernel too large and falsely assumes
  comparability.

The strongest surviving model is:

- the kernel owns an authoritative local routing table,
- but the table stores a set of accepted candidate claims per `pCID`, not just
  one hardcoded winner,
- handlers may self-register claims,
- imported or federated claims may also become candidates,
- local promise history and policy shape which candidates remain acceptable,
- and selection may optionally consider plural-currency or capability offers as
  one input, not as the only deciding metric.

## Recommended kernel role

The best default role is **kernel as policy owner**.

Why not minimal registrar?

- because the kernel must still decide which claims it accepts,
- because resource scarcity and promise history require local judgment,
- and because dispatch affects execution safety.

Why not exchange/matcher as the kernel core?

- because pricing incomparable promises is not the kernel's foundational job,
- because it grows the kernel in the wrong dimension,
- and because it risks turning local sovereignty into market theater.

So the recommended split is:

- the kernel is policy owner and authoritative selector,
- registration is a claim-ingest function,
- optional exchange/matching logic is adjacent or subordinate,
- and any market-like mechanism remains advisory unless the kernel explicitly
  decides otherwise.

## What the routing table should conceptually contain

Not a final schema, but the TE implies the kernel should think in terms of
accepted candidate claims, each carrying facts such as:

- handler identity,
- `pCID` claim,
- source of the claim (`self`, `operator`, `imported`),
- local trust state / promise-keeping history,
- capability or resource constraints,
- optional economic terms or offered currencies,
- local policy tags or pinning state,
- current eligibility status.

This is more expressive than a simple one-row routing map, and it matches the
promise-theory framing better.

## Conclusions

Rejected as the generic model:

- **single authoritative winner fixed by curation alone** as the only routing
  model,
- **kernel-core exchange/matcher** as the primary routing authority,
- **single universal currency** as a serious simplification.

Surviving alternatives:

- **self-registration with kernel acceptance** survives,
- **multiple accepted claimants per `pCID`** survives,
- **imported/federated claims as candidate inputs** survives,
- **plural-currency or capability-offer matching** survives only as an optional
  advisory mechanism under kernel policy.

Recommended conclusion:

- The kernel should own the authoritative local routing table.
- That table should allow more than one accepted claimant per `pCID`.
- Handlers may register claims, and external registries may contribute claims,
  but the kernel decides what becomes locally eligible.
- Promise-keeping history should matter directly.
- Plural-currency exchange may exist, but as a subordinate advisory layer, not
  as the kernel's core identity.

## Exact DF question that remains

Before any routing implementation is locked, one DF choice should be answered:

- Does v1 need first-class support for multiple accepted claimants per `pCID`,
  or may v1 keep one active winner per `pCID` while still storing competing
  claims and their provenance for future expansion?

This TE recommends first-class multiple-claim support as the cleaner long-term
model, but a one-active-winner v1 may be acceptable if claim provenance and
future multi-claim expansion are preserved.

## Implications for open TODOs and DIs

- `002.10` assumes a handler has already been chosen; `002.15` now defines where
  that choice should come from.
- `002.11` should consider versioning not only of handler ABI reports but also of
  routing claims and imported registry evidence.
- `001.4` and `001.5` should avoid hardcoding the assumption that a `pCID` maps
  to exactly one handler forever.
- Any future registry or exchange implementation should separate:
  claim ingestion,
  local policy,
  and optional economic comparison.
