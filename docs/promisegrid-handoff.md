# PromiseGrid Design Handoff for Wire Lab

This document hands off the PromiseGrid-relevant design work in this repo to
the `~/lab/wire-lab` team for use in design simulations.

This repo is **not** the canonical PromiseGrid specification. It currently
contains two different kinds of material:

- one concrete app specimen (`navlog` / `flightpad`) that has made some local
  implementation decisions so work can proceed; and
- a substantial thought-experiment corpus that pressure-tests broader
  PromiseGrid design questions.

Wire Lab should treat this document as a **source map plus simulation backlog**.
It should not treat this repo as the authority that settles PromiseGrid
architecture ahead of simulation evidence.

## Purpose and audience

The audience is the Wire Lab team working in `~/lab/wire-lab`.

The goal of this handoff is to make the current design state explicit enough
that Wire Lab can:

- identify which parts of this repo are useful as simulation specimens;
- identify which parts are stronger cross-cutting conclusions;
- identify which decisions remain open and should stay under experiment; and
- start a concrete simulation backlog without a long clarification round.

Wire Lab's current posture matters here. Its harness spec explicitly says the
harness exists to **discover** the design, not validate a predetermined one,
and that message shape is a specimen under test rather than a pre-locked global
envelope. This handoff is written to support that posture rather than override
it.

## What this repo contributes

### 1) App-local specimen work

These files exist to move the navlog app forward, not to settle PromiseGrid for
all runtimes and all domains:

- `README.md`
- `TODO/001-cas-eventlog.md`
- `docs/protocol/navlog-001.md`

They lock a concrete local specimen:

- content-addressed storage with refs + ledger + journal;
- a concrete event envelope shape for navlog;
- deterministic CBOR rules suitable for hashing and storage; and
- app-specific event payload semantics.

These are real decisions for navlog. They are **not automatically global
PromiseGrid decisions**.

### 2) Broader PromiseGrid design analysis

These files are the main handoff payload for Wire Lab:

- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260425-162242-promises.md`
- `docs/thought-experiments/TE-20260425-173644-hashing.md`
- `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`
- `docs/thought-experiments/TE-20260426-180233-pcid-handler-routing.md`
- `docs/thought-experiments/TE-20260426-184245-system-promises.md`
- `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`
- `docs/thought-experiments/TE-20260426-204445-kernel-vs-handler-ingress.md`
- `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`
- `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`
- `docs/thought-experiments/TE-20260426-204448-governance-vs-force.md`
- `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`
- `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`
- `docs/thought-experiments/grid-based-aviation-take2.md`

This corpus is where the repo most directly overlaps Wire Lab's mission.

## Design posture this repo now reflects

Across the TE corpus, the repo has converged on a fairly consistent PromiseGrid
philosophy:

- **Self-certifying identifiers over central registries.** `pCID` is treated as
  the hash-derived identifier of a protocol spec document. Many "registries" in
  PromiseGrid are mathematical rather than institutional.
- **Universal graph legibility without hierarchy.** Common message structure,
  hash-derived IDs, and portable predicates are meant to make artifacts legible
  across communities and runtimes. That does not imply a central authority.
- **Consensus by adoption and curation.** Compatibility emerges through
  publication, adoption, translation, trust, and curation, not through one
  sovereign standards body.
- **Per-promise, per-relationship trust.** Trust is local, typed, contextual,
  and empirical. Global trust scores are treated as a design smell.
- **Capability tokens as promises.** Capability tokens are promises from an
  issuer to later provide a resource or action. They may behave like personal
  currencies or exchangeable instruments, but not like one universal coin.
- **Executable standards where possible.** Schema checks, conformance tests,
  geometry checks, conflict detection, and similar rules can often be
  executable. That does not eliminate the need for institutions, labs,
  training, insurers, stewards, or curation layers.
- **Decentralized governance with incentives and accountability.** PromiseGrid
  aims to make decentralized stewardship practical, not to replace governance
  with central platforms or with fashionable but brittle abstractions such as a
  single blockchain currency or DAO voting monoculture.
- **Runtime diversity is fundamental.** Ingress, signing, key custody, and
  policy boundaries change across native binaries, containers, VMs, microVMs,
  browser/serverless/standalone WASM, WASI, microcontrollers, mobile apps, game
  engines, and delegated-signing server-side systems.

This posture aligns well with Wire Lab's current harness assumptions:

- no central registry;
- protocol forking as normal;
- trust accruing per promise type;
- kernel as a role, not necessarily one process;
- apparatus/specimen separation;
- candidate message shapes and ingress models as variables under test.

## What is locally locked in this repo

Wire Lab should import these as **local locks for navlog**, not as PromiseGrid
law.

### Navlog storage and operational state

`TODO/001-cas-eventlog.md` currently locks:

- refs as authoritative operational state;
- immutable append-only ledger entries for audit and replay;
- append-only time-partitioned journal/index entries for recovery and browsing;
- CAS-centric storage;
- hybrid refs (per-entity refs plus a user/dataset head ref).

This is a concrete app storage model. It is useful as a specimen for Wire Lab,
especially where simulations need durable state, audit chains, and recovery
paths.

### Navlog event envelope

`docs/protocol/navlog-001.md` currently defines the navlog event specimen as:

```cbor
grid([
  pCID,
  payload,
  signature
])
```

with:

- deterministic DAG-CBOR;
- `pCID` as the CID of the canonical bytes of the spec document;
- `signature` reserved and `null` in the MVP.

This is a real local commitment for navlog planning. It is also one useful
input specimen for Wire Lab. It is **not** proof that PromiseGrid-at-large has
settled envelope shape, signature placement, or signing target.

## Strong conclusions from the TE corpus

These are the main carry-forward conclusions for Wire Lab. They are stronger
than raw brainstorming, but most of them are still **simulation candidates**,
not globally locked architecture.

### Message identity and hashing

Source:
- `docs/thought-experiments/TE-20260425-173644-hashing.md`

Working conclusion:
- If PromiseGrid uses a message CID, the strongest current candidate is the
  canonical untagged tuple `[pCID, payload]`, with `grid(...)` treated as a
  framing wrapper rather than part of semantic identity.

Why it matters to Wire Lab:
- this lets the harness compare transport/wrapper choices without silently
  turning wrapper differences into message-identity differences.

Still open:
- whether PromiseGrid needs a message CID at all in the general case;
- how much correlation/privacy cost a stable message CID introduces;
- whether some specimens should omit message-level IDs and rely on payload-level
  identity instead.

### Signed material

Source:
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`

Working conclusion:
- If `pCID` is part of the author's claimed meaning, signatures must bind
  `pCID + payload`, not payload alone.

Surviving families:
- author signs canonical `[pCID, payload]`;
- author signs a stable CID/hash derived from `[pCID, payload]`;
- transport sender signs `['grid' tag, pCID, payload]` as a sender-local
  promise;
- layered combinations of author and sender signatures.

Why it matters to Wire Lab:
- signer role and signing target are not side details. They determine who is
  claiming semantic meaning, who is only introducing an envelope at ingress,
  and what can be substituted without invalidating signatures.

Still open:
- whether author-primary, sender-primary, or layered signing is the dominant
  PromiseGrid mental model;
- whether the primary signature field belongs in the envelope, payload, a
  detached artifact, or some layered mix;
- whether the broader PromiseGrid envelope should really be modeled as
  `[pCID, payload]`, `[pCID, payload, signature]`, or something more flexible.

### Ingress and kernel boundary

Source:
- `docs/thought-experiments/TE-20260426-204445-kernel-vs-handler-ingress.md`
- `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`

Working conclusion:
- There is no single ingress architecture that survives all runtimes and all
  trust boundaries.

Surviving families:
- kernel-first ingress;
- handler-first ingress;
- hybrid ingress;
- richer kernel-visible lifecycle versus resource-only microkernel boundary.

Why it matters to Wire Lab:
- this is exactly the kind of decision the harness should keep open and compare
  across shared scenarios. The repo's strongest conclusion is not a winner; it
  is that the winner is runtime- and role-dependent.

Still open:
- which parts of inspect/verify/execute belong in the apparatus versus inside a
  specimen;
- whether the kernel is mostly a resource governor or a protocol-visible
  participant;
- whether there is a stable wire-level invariance across all ingress models.

### Kernel and handler lifecycle

Source:
- `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`
- `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`

Working conclusion:
- `inspect -> verify -> execute` is the strongest generic lifecycle model from
  the ABI TE.
- The microkernel TE then re-opened the question and argued that a
  resource-first, minimal dispatcher boundary may be the better default bias.

Why it matters to Wire Lab:
- this is an active architectural fork, not a settled design.

Still open:
- whether inspect/verify must be kernel-visible at all;
- whether verify-first or resource-only designs produce better long-term
  apparatus properties.

### pCID to handler routing

Source:
- `docs/thought-experiments/TE-20260426-180233-pcid-handler-routing.md`

Working conclusion:
- a kernel-owned local routing table is favored;
- multiple accepted claimants per `pCID` should be allowed;
- self-registration and imported claims are both valid inputs;
- promise-economy / plural-currency mechanisms are plausible advisory inputs,
  but the kernel is better understood as policy owner than as the exchange
  itself.

Why it matters to Wire Lab:
- routing is a live commons/governance problem, not only a lookup table.

Still open:
- the exact selection function among accepted claimants;
- how much exchange logic belongs in the routing layer versus adjacent services;
- whether some runtime classes need radically simpler routing models.

### Runtime diversity

Source:
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`

Working conclusion:
- host/runtime differences materially affect ingress ownership, key custody,
  signing feasibility, and whether kernel and handler can even be distinct
  processes.

Relevant runtime families:
- native binaries on Linux, macOS, and Windows;
- containers;
- virtual machines;
- microVM or microVM-like hosts;
- browser-hosted WASM;
- serverless WASM;
- standalone WASM runtimes;
- standalone WASI runtimes;
- serverless WASI-style hosts;
- microcontroller firmware;
- mobile apps;
- game-engine embeddings; and
- delegated-signing server-side applications that must act for users without
  holding user private keys.

Why it matters to Wire Lab:
- any candidate PromiseGrid design that only works for one host model is too
  narrow.

Still open:
- which role-level invariants are truly cross-runtime;
- which runtime-specific compromises should be modeled as specimens rather than
  built into apparatus assumptions.

### Commons, governance, and force

Source:
- `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`
- `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`
- `docs/thought-experiments/TE-20260426-204448-governance-vs-force.md`
- `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`

Working conclusion:
- PromiseGrid should be judged in large part by whether it can avoid
  tragedy-of-the-commons failure through decentralized governance,
  accountability, curation, and incentive structures.
- Mathematical naming and self-certifying IDs remove some central registry
  pressure, but they do **not** remove the need for discovery, curation,
  migration, stewardship, or conflict handling.
- Governance and force cannot be collapsed into one dimension. Promise-based
  cooperation remains primary, but sanctions, throttling, refusal, revocation,
  and exclusion are still real system tools.

Why it matters to Wire Lab:
- Wire Lab is the right place to test whether these governance claims survive
  simulation rather than rhetoric.

Still open:
- which governance burdens belong to local communities versus shared
  infrastructure federations;
- where soft governance is enough and where coercive analogs are necessary;
- how discovery and curation markets evolve around self-certifying identifiers.

### Applied domain stress test: aviation

Source:
- `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`
- `docs/thought-experiments/grid-based-aviation-take2.md`

Working conclusion:
- a safety-critical industry can plausibly be bootstrapped in a PromiseGrid-like
  world without legacy regulators, but not without durable institutions.
- airfield stewards, labs, training organizations, insurers, inspection
  networks, coordination providers, and infrastructure federations still
  emerge; they are just not assumed to be one central sovereign hierarchy.

Why it matters to Wire Lab:
- aviation provides a concrete, high-stakes scenario family for testing
  publication, attestation, training, maintenance, coordination, and
  commons-governance questions together.

Still open:
- which aviation institutions should be modeled as general PromiseGrid patterns;
- how much of air-traffic coordination becomes deterministic protocol versus
  service-provider layer;
- which parts of the aviation thought experiment should be turned into reusable
  simulation fixtures.

## What Wire Lab should treat as apparatus versus specimen

This is the most important operational boundary for the handoff.

### Apparatus-level candidates

These are the ideas this repo supports as apparatus-level assumptions or at
least apparatus-level evaluation axes:

- no central registry assumption;
- self-certifying protocol identity as a serious baseline assumption;
- per-promise-type, peer-local trust accounting;
- plural trust domains and protocol forks as normal;
- capability tokens as promises, with room for plural/personal-currency models;
- kernel, handler, issuer, forwarder, steward, and verifier as roles rather
  than always fixed process classes;
- runtime diversity as a first-class simulation dimension;
- commons/governance outcomes as a core acceptance lens.

### Specimen-level candidates

These should remain under experiment unless and until Wire Lab decides
otherwise:

- the exact PromiseGrid envelope shape;
- whether signatures are in the envelope, payload, detached, or layered;
- the exact signing target and signer primacy model;
- whether there is a message CID and what bytes it hashes;
- kernel-first versus handler-first ingress;
- whether inspect/verify are kernel-visible;
- exact routing-table registration and selection mechanics;
- exact market/exchange mechanisms for capability tokens;
- exact domain protocols for things like airfields, training, surveillance, and
  traffic coordination.

Rule of thumb: if this repo shows both a local lock and a later TE reopening the
question, Wire Lab should treat it as a specimen axis, not a settled apparatus
fact.

## Recommended simulation backlog for Wire Lab

The following backlog is ordered to let Wire Lab extract value quickly while
preserving apparatus/specimen discipline.

### 1) Envelope specimen comparison

Compare at least:

- navlog's `grid([pCID, payload, signature])` specimen;
- Wire Lab's current apparatus-neutral candidate message shapes; and
- any textual or group-transport specimens already active in the harness.

Primary question:
- does navlog's concrete envelope give useful leverage as a specimen without
  prematurely constraining the apparatus?

### 2) Signed-material comparison

Compare:

- author-signed canonical `[pCID, payload]`;
- author-signed CID/hash of `[pCID, payload]`;
- sender-signed `['grid' tag, pCID, payload]`;
- layered author + sender combinations.

Primary questions:
- who is making the first semantic promise?
- what substitution attacks remain possible?
- how does the answer change across ingress models and runtimes?

### 3) Ingress boundary comparison

Compare:

- kernel-first;
- handler-first;
- hybrid;
- resource-only microkernel boundary;
- richer kernel-visible inspect/verify/execute boundary.

Primary questions:
- what must the apparatus observe?
- what can remain inside specimens?
- which boundaries survive runtime diversity?

### 4) Routing competition and handler selection

Compare:

- one accepted claimant per `pCID`;
- multiple accepted claimants per `pCID`;
- self-registration versus imported claims;
- local policy ranking;
- advisory capability-market or plural-currency ranking inputs.

Primary questions:
- what routing model remains governable over time?
- what local information does a kernel or router really need?

### 5) Runtime matrix reruns

Rerun the ingress and signing scenarios across the runtime families from
`TE-20260426-220037-runtime-environments.md`.

Primary question:
- does a design survive native, browser, serverless, firmware, mobile, and
  delegated-signing constraints, or is it only attractive in one host model?

### 6) Commons and governance scenarios

Build scenarios that explicitly test:

- resource exhaustion and freeloading;
- discovery/curation quality around self-certifying IDs;
- revocation and exclusion;
- promise-keeping history;
- capability-token exchange and selective refusal;
- governance capture and institution failure.

Primary question:
- does decentralized governance actually remain workable under stress?

### 7) Aviation-derived scenario families

Start with:

- airfield publication and stewardship;
- runway, obstacle, and field manifest publication;
- training endorsements;
- maintenance attestations and inspection chains;
- surveillance/transponder-like disclosures;
- corridor or sequencing coordination.

Primary question:
- can PromiseGrid-style primitives support a safety-critical institutionally
  dense industry without falling back to central registry assumptions?

## Reading order and source map

The shortest useful reading sequence for Wire Lab is:

1. `README.md`
   - repo orientation and current navlog storage/envelope posture.
2. `TODO/001-cas-eventlog.md`
   - the concrete navlog-local storage and operational-state decisions.
3. `docs/protocol/navlog-001.md`
   - the exact navlog event specimen now in play.
4. `TODO/002-thought-experiments.md`
   - index of the design corpus and what is still open.
5. `docs/thought-experiments/TE-20260425-162242-promises.md`
   - trusted-peers/promise-theory baseline.
6. `docs/thought-experiments/TE-20260425-173644-hashing.md`
   - message identity and wrapper/canonicalization reasoning.
7. `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`
   - author-versus-sender signing fork.
8. `docs/thought-experiments/TE-20260426-204445-kernel-vs-handler-ingress.md`
   - ingress boundary fork.
9. `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`
   - inspect/verify/execute framing.
10. `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`
    - resource-only counterpressure against richer kernel visibility.
11. `docs/thought-experiments/TE-20260426-180233-pcid-handler-routing.md`
    - routing-table and handler-claim governance.
12. `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`
    - runtime diversity as a design constraint.
13. `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`
    - the long-horizon commons/governance frame.
14. `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`
    - early synthesis of the commons/governance implications.
15. `docs/thought-experiments/TE-20260426-204448-governance-vs-force.md`
    - the governance/coercion tension.
16. `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`
    - later re-synthesis across the wider corpus.
17. `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`
    - full domain stress test.
18. `docs/thought-experiments/grid-based-aviation-take2.md`
    - supplemental note sharpening graph legibility, executable standards, and
      multi-runtime execution.

Then compare against the current Wire Lab framing:

- `~/lab/wire-lab/README.md`
  - Wire Lab's own charter and its emphasis on discovery over validation.
- `~/lab/wire-lab/protocols/wire-lab.d/specs/harness-spec-draft.md`
  - current apparatus assumptions: no central registry, per-promise trust,
    kernel as role, message shape as specimen, economic coordination as a live
    experimental dimension.

## Open questions Wire Lab should carry forward

The biggest unresolved questions from this repo, viewed as Wire Lab work items,
are:

- Should PromiseGrid standardize an envelope at all, or keep envelope shape
  specimen-local for longer?
- If there is a common envelope family, should its semantic core be
  `[pCID, payload]`, `[pCID, payload, signature]`, or a more extensible form?
- Is the dominant signing model author-primary, sender-primary, or layered?
- Does PromiseGrid want a message CID at the general level, and if so, what
  exactly does it identify?
- Is the kernel mainly a resource governor, a protocol-visible lifecycle
  participant, or a role that dissolves under some runtimes?
- Should multiple accepted handler claimants per `pCID` be first-class from the
  start?
- How much privacy tension does universal graph legibility create in practice?
- What durable discovery and curation institutions are needed around
  self-certifying identifiers?
- Which institutions discovered in the aviation scenario generalize to other
  safety-critical or infrastructure-heavy domains?

## Direct handoff guidance

Use this repo as follows:

- Treat navlog's concrete envelope and storage model as **useful specimens**.
- Treat the TE corpus as **the real handoff payload**.
- Treat contradictions between local locks and later TEs as evidence that the
  relevant dimension belongs under simulation, not as a documentation error to
  flatten away.
- Preserve Wire Lab's apparatus-versus-specimen discipline.
- Prefer simulation campaigns that compare alternatives under the same scenario
  family rather than validating one presumed winner.
- If Wire Lab needs one sentence to remember from this handoff, it is this:
  **import the design posture and the open forks, not just the current navlog
  wire shape.**
