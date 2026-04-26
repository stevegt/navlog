# Promise-Based Thought Experiments (Payload Signatures + Trusted Handlers/Peers)

Assume this world:

- Envelope is just `grid([pCID, payload])` (or `grid([pCID,payload, ...])`), and
  **all signatures live inside payload**.
- Kernel is intentionally dumb: it parses `pCID`, then calls a **local handler**
  for that `pCID`.
- Handler returns to kernel: "here's the signer identity, verification result,
  cost, expiry, replay info, etc." so the kernel can do spam/resource policy.

Now add the assumptions:

- A kernel only runs handlers that it trusts.
- A kernel only accepts transport (connections) from peers that it trusts.
- The entire system is based on Mark Burgess's work on promise theory.
- A security capability token is a form of promise that a provider makes to a
  (later) consumer.
- A security capability token is a promise that the provider will provide a
  resource to the consumer when later presented with a request containing the
  token.
- Tokens can be rejected (revoked) by the provider, breaking the promise.
- Trust is built on the basis of promises that are kept or broken.

Under this model, the design space changes substantially: we've moved from an
"open Internet adversary" model to a **federated / relationship-based grid**
where the kernel's own security posture is itself a set of promises:

- "I only talk to peers I trust."
- "I only run handlers I trust."
- "Capabilities/tokens are promises with revocation."
- "Trust is empirical: promises kept/broken over time."

Below are thought experiments in that mature PromiseGrid world (billions of
people, millions of apps, thousands of pCIDs) evaluating the consequences of
**signatures only in payload** and **kernel routing to trusted pCID handlers**.

## 0) Baseline: what the kernel needs signatures for

In this world, signatures are less about proving identity to strangers and more
about:

- preventing accidental/malicious misattribution inside a trusted federation,
- enabling accountability/audit of promises,
- enabling capability presentation and replay protection.

Because peers and handlers are already trust-filtered, the kernel doesn't need a
universal, protocol-agnostic signature parser just to survive.

## 1) Unknown pCID flood

Old model: attacker spams random pCIDs.

New assumptions:

- Only trusted peers can connect.
- Trusted peers have incentives to keep promises ("don't spam me") or lose trust
  or capability.
- Kernel only routes to trusted handlers; if no handler exists, message is
  meaningless locally.

Result:

- Unknown pCID flood becomes a **relationship failure**, not a global DoS.
- The correct response is not "parse signatures in kernel," but "reduce peer
  budget / revoke capabilities / downgrade trust" for the peer that violated its
  promise.

Implication: payload-only signatures are fine; the kernel's spam controls are
**capability budgets** and **trust scoring**, not envelope triage.

Mitigation patterns (promise-aligned):

- global "admission control": require a peer to earn budget (PoW, fees, or
  reputation tokens) before the kernel stores/relays unknown pCIDs
- protocol registry / allowlist by policy domain
- "cold storage" bucket for unknown pCIDs with harsh quotas

## 2) Handler invocation as a DoS amplifier

Old model: malicious payload forces expensive verification.

New assumptions:

- Only trusted handlers run, so "handler lying" is less of a concern.
- Only trusted peers send messages, but peers can still break promises (buggy or
  compromised).

Result:

- DoS shifts from "anonymous adversary" to "trusted peer broke promise."
- Kernel should still meter resources, but enforcement is promise-based:

  - "I grant you capability X with budget Y (CPU, storage, rate)."
  - If you exceed budget, I revoke.

Implication: you still want a **resource-metered handler API**, but not because
you can't trust handlers - because you can't assume peers always keep promises.

## 3) Handler trust / plugin ecosystem risk

Old model: handlers are untrusted, dangerous.

New assumptions:

- Kernel only runs handlers it trusts (by provenance, policy, attestation, social
  contract, etc.).

Result:

- This becomes a governance problem, not a protocol-structure problem.
- Kernel can still sandbox for defense-in-depth, but it's optional.

Implication: payload-only signatures plus handler callbacks become much more
viable, because the kernel can treat handler outputs as authoritative within the
trust boundary.

## 4) Store-and-forward relays without handlers

Old model: relays can't do signature-aware filtering if signatures are payload-only.

New assumptions:

- Relays only accept from trusted peers.
- Store/forward itself is a promise with explicit capability/budget.

Result:

- Relays don't need to understand signatures to decide resource allocation; they
  enforce capability terms:

  - size limits,
  - retention promises,
  - forwarding promises,
  - quotas.

- If a relay needs semantic filtering, it can choose to run the handler (trusted)
  or refuse that service.

Implication: envelope signatures are not necessary for generic relays; capability
governance replaces protocol-agnostic triage.

## 5) Nested messages and composability

Old model: lack of universal signature location complicates tooling.

New assumptions:

- Handlers are trusted and are the correct place to define semantics.
- Kernel is intentionally minimal; it's a router + capability enforcer.

Result:

- Nested-message verification can be delegated to handlers that understand the
  semantics of promise bundles (pilot promise + airport promise + ATC promise).
- Kernel can require handlers to return a bounded "promise summary":

  - principals involved (IDs),
  - whether signatures/attestations were verified under that protocol,
  - expiry/replay constraints,
  - estimated resource cost,
  - dependencies (e.g., "requires provider X online to check revocation").

Implication: payload-only signatures fit promise theory: the meaning of a promise
is contextual; handlers interpret, kernel enforces budgets.

## 6) Upgrades over decades (ABI stability)

Old model: kernel/handler ABI becomes the long-horizon problem.

New assumptions:

- Kernels only run trusted handlers; trusted ecosystems can coordinate upgrades.
- Peers are trusted; federations can do staged rollouts.

Result:

- ABI evolution is manageable by governance, not global backwards compatibility.
- Still: you want a stable "contract" for handler summary reports so kernels
  remain small.

Implication: define a **small, stable handler-report schema** that can itself be
versioned via a pCID-like mechanism (meta-pCID), so old kernels can treat
"unknown report versions" conservatively.

## 7) Capability tokens as promises (revocable)

This is where signatures interact most strongly with architecture.

If tokens are promises:

- A token is meaningful only relative to the provider who will later honor it.
- Revocation ("breaking the promise") is a first-class reality.

Key consequences:

- Offline verification of tokens is limited-value unless you can check revocation
  status; you often need to consult the provider or a revocation log.
- That reduces the importance of kernel-level signature verification for tokens;
  what matters is:

  - does the provider still honor this token?
  - does my peer still have budget?
  - has the peer kept promises historically?

Implication: again, payload-only signatures are fine; kernel decisions can be
based on capability state and trust history, not cryptographic universalism.

## What becomes "best" in this promise-theory world?

### Best fit: signatures in payload + trusted handler summaries

Because:

- Semantics of promises live in protocols (pCID-defined), not in the kernel.
- Trust boundaries (peers, handlers) make generic adversarial triage less
  necessary.
- Capabilities are the enforcement mechanism; cryptographic signatures are
  supporting evidence, not the core gating factor.

### Kernel responsibilities (minimal, promise-aligned)

- Maintain trusted peer set (and its own promise about accepting connections).
- Maintain trusted handler set (and promise about executing them correctly).
- Enforce capability budgets per peer/protocol.
- Ask handler for a bounded "promise summary":

  - principals involved (IDs),
  - whether signatures/attestations were verified under that protocol,
  - expiry/replay constraints,
  - estimated resource cost.

Kernel should treat handler-provided "trust hints" as advisory; kernel computes
trust from observed history of promises kept/broken.

### Handler responsibilities (promise semantics)

- Parse payload (including internal signatures/attestations).
- Verify according to its pCID's rules.
- Produce a structured summary for kernel decisions.

## Remaining failure modes even with trust assumptions

Even trusted peers/handlers can break promises (bugs, compromise). So you still
want:

- resource metering,
- timeouts,
- deterministic parsing limits,
- audit logs (immutable ledger helps),
- revocation and trust-downgrade workflows.

