## Decision Intent Log

ID: DI-002-20260515-165527
Date: 2026-05-15 16:55:27 UTC
Status: active
Decision:
- Write `docs/promisegrid-handoff.md` as a comprehensive cross-repo handoff of
  the PromiseGrid-relevant design work in this repo for the `~/lab/wire-lab`
  team.
Intent:
- Give wire-lab a simulation-ready source map that distinguishes navlog-local
  implementation locks from broader PromiseGrid hypotheses, strong conclusions,
  and still-open design forks.
- Reduce clarification churn by making the current design posture, specimen
  candidates, and recommended simulation backlog explicit in one document.
Constraints:
- Do not present navlog-local implementation choices as global PromiseGrid law.
- Keep the handoff grounded in the existing README, protocol doc, DI logs, and
  thought experiments in this repo, while explicitly aligning with wire-lab's
  apparatus-versus-specimen discipline.
- Include recommended simulation work items, but keep the document descriptive
  rather than prescriptive about final PromiseGrid architecture.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/promisegrid-handoff.md`

ID: DI-002-20260428-235845
Date: 2026-04-28 23:58:45 UTC
Status: active
Decision:
- Normalize `docs/thought-experiments/grid-based-aviation-take2.md` in place as a
  repo-style thought-experiment note.
- Expand its executable-standards and sandboxing discussion beyond WASM to the
  same runtime families covered by `TE-20260426-220037-runtime-environments.md`.
Intent:
- Keep the note aligned with the current PromiseGrid philosophy: universal graph
  legibility, hash-derived identifiers, portable executable checks, and
  deterministic coordination do not imply central registries or hierarchy.
- Replace the note's current prompt-transcript form and WASM monoculture with a
  cleaner, runtime-aware statement that remains anti-DAO, anti-single-currency,
  and pro-capability-token.
Constraints:
- Keep `grid-based-aviation-take2.md` as a special-case note at its current
  path; do not promote it to a numbered TE artifact.
- Mirror the runtime coverage from `002.24`: native, containers, VMs,
  microVM-like hosts, browser/serverless/standalone WASM, standalone/serverless
  WASI, microcontrollers, mobile, game engines, and delegated-signing servers.
- Treat executable standards as portable, content-addressed predicates that may
  have more than one runtime realization.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/grid-based-aviation-take2.md`

ID: DI-002-20260426-232002
Date: 2026-04-26 23:20:02 UTC
Status: active
Decision:
- Rename the TE `002.25` artifact path from
  `docs/thought-experiments/TE-20260426-230939-aviation-without-regulators.md`
  to `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`.
Intent:
- Keep the TE artifact name aligned with the new positive title and avoid
  carrying the old negative framing in the filename.
Constraints:
- Keep the TE ID and document content unchanged apart from the file path.
- Update the live corpus and task references to the new path.
- Preserve older DI references as historical records rather than rewriting them.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`
Supersedes: `DI-002-20260426-231610`

ID: DI-002-20260426-231610
Date: 2026-04-26 23:16:10 UTC
Status: active
Decision:
- Rename TE `002.25` from "Aviation Without Regulators Thought Experiment" to
  "Grid-based Aviation Thought Experiment" while keeping the existing file path.
Intent:
- Make the TE title state the positive architectural frame rather than defining
  the scenario only by the absence of legacy regulators.
Constraints:
- Keep the TE content, TE ID, and file path unchanged.
- Update the task label in `TODO/002-thought-experiments.md` to match the new
  title while preserving the existing scenario description.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-230939-aviation-without-regulators.md`

ID: DI-002-20260426-230939
Date: 2026-04-26 23:09:39 UTC
Status: active
Decision:
- Add TE `002.25` as a long-form world-building exercise about how an aviation
  industry could emerge after the internet, without legacy regulators or
  standards bodies, using PromiseGrid-style protocols and institutions instead.
Intent:
- Stress-test the full TE corpus against a concrete, safety-critical industry in
  order to see how funding, certification, training, communications, airfield
  operations, traffic coordination, and shared infrastructure could actually be
  bootstrapped and governed without defaulting to central registries,
  blockchains, DAOs, or other hype-driven abstractions.
Constraints:
- Keep the TE practical and institutionally serious.
- Explicitly avoid cryptocurrency, blockchain, DAOs, and similar toxic or
  overhyped framing.
- Cover airfields, communications, navigation, aircraft design and inspection,
  pilot and mechanic training, shared traffic systems, funding, maintenance,
  and evolutionary stages of the industry.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-230939-aviation-without-regulators.md`

ID: DI-002-20260426-220850
Date: 2026-04-26 22:08:50 UTC
Status: active
Decision:
- Refine TE `002.24` to add containers, virtual machines, and microVM or
  microVM-like sandboxed hosts as explicit runtime families.
Intent:
- Keep the runtime TE honest about host isolation models that sit between native
  processes and higher-level sandboxes, since they materially affect ingress
  ownership, key custody, and host-versus-guest boundaries.
Constraints:
- Preserve the existing runtime analysis and add the new host models as
  first-class runtime types rather than leaving them implicit under native or
  serverless execution.
- Keep the TE connected to `002.18` and `002.19`.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`

ID: DI-002-20260426-220037
Date: 2026-04-26 22:00:37 UTC
Status: active
Decision:
- Add TE `002.24` to examine how supported runtime environments change the
  ingress boundary question from `002.19` and the signed-material question from
  `002.18`.
- The TE must cover at least these runtime families:
  native general-purpose binaries, browser-hosted WASM, serverless WASM,
  standalone WASM runtimes, standalone WASI runtimes, serverless WASI-style
  runtimes, microcontroller firmware, mobile apps, game-engine embeddings, and
  server-side applications that must trigger user-authorized signatures without
  holding user private keys.
Intent:
- Keep the ingress and signing analysis honest by testing it against the actual
  runtime diversity PromiseGrid wants to support rather than assuming one host
  model.
- Surface where "kernel-first" vs "handler-first" and "author-signed" vs
  "sender-signed" are runtime-dependent rather than universal.
Constraints:
- Treat the TE as analysis only; do not lock one universal runtime architecture.
- Make the TE explicitly connect back to
  `TE-20260426-204444-pcid-signed-material.md` and
  `TE-20260426-204445-kernel-vs-handler-ingress.md`.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`

ID: DI-002-20260426-214859
Date: 2026-04-26 21:48:59 UTC
Status: active
Decision:
- Refine TE `002.23` so it reflects the recent `002.20`, `002.21`, and
  `002.18` updates:
  - PromiseGrid often uses mathematical or self-certifying identifiers rather
    than central registries for protocol naming,
  - governance pressure therefore shifts toward discovery, curation, trust,
    migration, and handler-claim selection around those identifiers,
  - and the signed-material discussion now includes the sender-signed
    `['grid' tag, pCID, payload]` envelope as a first-class alternative.
Intent:
- Keep the resynthesis aligned with the recent survey and synthesis corrections
  and ensure it does not fall back to central-registry assumptions or overlook
  the choice between author-primary and sender-primary first protocol-shape
  promises.
Constraints:
- Preserve the existing anti-enclosure, anti-collapse framing and candidate
  architectural families.
- Keep this as a TE refinement only; do not lock architecture.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`

ID: DI-002-20260426-214013
Date: 2026-04-26 21:40:13 UTC
Status: active
Decision:
- Refine TE `002.21` so it inherits the revised `002.20` view that PromiseGrid
  often uses mathematical or self-certifying identifiers rather than central
  registries for protocol naming.
Intent:
- Keep the commons-governance synthesis aligned with the survey's updated claim
  that governance pressure around naming shifts toward discovery, curation,
  trust, maintenance, migration, and handler-claim selection around hash-based
  identifiers.
Constraints:
- Preserve the synthesis's existing governance shapes and anti-enclosure frame.
- Remove wording that implies a central registry is the main unresolved
  institutional question for routing or protocol identity.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`

ID: DI-002-20260426-213159
Date: 2026-04-26 21:31:59 UTC
Status: active
Decision:
- Refine TE `002.20` so it does not imply that PromiseGrid depends mainly on
  central registries for protocols or other core naming functions.
- The TE must explicitly recognize mathematical or self-certifying
  "registries", especially hash-derived identifiers such as `pCID`s derived
  from spec documents.
Intent:
- Keep the commons-history survey aligned with the actual PromiseGrid design,
  where cryptographic hashes often play the role that institutional registries
  play in other ecosystems.
- Clarify that governance still exists, but it shifts toward discoverability,
  curation, trust, maintenance, migration, and handler-claim selection rather
  than requiring one central naming authority.
Constraints:
- Preserve the survey's argument that governance institutions are still needed.
- Reframe "registry" language so mathematical naming and local/shared indexes
  are distinguished from central registries.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`

ID: DI-002-20260426-212511
Date: 2026-04-26 21:25:11 UTC
Status: active
Decision:
- Refine TE `002.18` so the transport-sender-signed
  `['grid' tag, pCID, payload]` model is integrated into the main alternatives
  and scenario analysis rather than left only as a follow-on question.
Intent:
- Analyze the sender-signed whole-envelope model as a first-class alternative,
  including how it shifts the promise from authorial meaning to sender-introduced
  envelope meaning and how that interacts with ingress placement.
Constraints:
- Preserve the existing author-primary analysis and keep the sender-signed
  envelope model explicit rather than letting it silently replace end-to-end
  authorship.
- Make the interaction with `TE-20260426-204445-kernel-vs-handler-ingress.md`
  part of the main argument.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`
Supersedes: DI-002-20260426-211925

ID: DI-002-20260426-211925
Date: 2026-04-26 21:19:25 UTC
Status: active
Decision:
- Refine TE `002.18` with a follow-on question asking whether the signed object
  should instead be the whole `['grid' tag, pCID, payload]` envelope signed by
  the transport sender, rather than author-signed `[pCID, payload]`.
Intent:
- Keep the signed-material TE open to a sender-asserted envelope model in which
  the transport sender promises both that the payload conforms to `pCID` and
  that the `pCID + payload` tuple conforms to the `grid` family marker.
- Make the interaction with ingress placement explicit, since a sender-signed
  envelope model changes who is making the first protocol-shape promise.
Constraints:
- This is a TE refinement only; it does not lock signer role, envelope shape,
  or ingress architecture.
- Preserve the existing author-primary analysis and add this as an explicit
  follow-on question.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`

ID: DI-002-20260426-211406
Date: 2026-04-26 21:14:06 UTC
Status: active
Decision:
- Refine TE `002.20` so the centralized-platform section states PromiseGrid's
  positive decentralization thesis directly instead of framing it mainly as a
  rejection of centralization.
Intent:
- Make the survey's PromiseGrid relevance section say plainly that PromiseGrid
  aims to provide decentralized incentives, accountability, local/community
  control, and stewardship of shared resources.
- Keep centralized platforms as a challenge case rather than implying they are
  candidate institutional answers for PromiseGrid.
Constraints:
- Preserve the historical comparison to centralized platforms, cloud, devops,
  and investment patterns.
- Do not weaken the survey's claim that PromiseGrid still has to solve the real
  coordination, maintenance, and abuse-response problems those platforms solved.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`

ID: DI-002-20260426-205931
Date: 2026-04-26 20:59:31 UTC
Status: active
Decision:
- TE `002.18` gains a follow-on question asking whether earlier TEs may have
  erred by treating the envelope as `[pCID, payload]` instead of
  `[pCID, payload, signature]`.
Intent:
- Keep the pCID signed-material TE open to the possibility that prior envelope
  framing was too narrow once authorial signatures are required to bind meaning.
Constraints:
- This is a TE refinement only; it does not lock envelope shape.
- Preserve the existing conclusions and add the new question as an explicit
  follow-on item.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`

ID: DI-002-20260426-204443
Date: 2026-04-26 20:44:43 UTC
Status: active
Decision:
- Add six new TE artifacts in this exact order: `002.18` pCID signed material,
  `002.19` kernel vs handler ingress, `002.20` commons history survey,
  `002.21` commons governance synthesis, `002.22` governance vs force, and
  `002.23` commons governance resynthesis.
- Use these exact TE doc suffixes:
  `pcid-signed-material`, `kernel-vs-handler-ingress`,
  `commons-history-survey`, `commons-governance-synthesis`,
  `governance-vs-force`, and `commons-governance-resynthesis`.
- Split the commons/governance work into a full survey plus two separate
  synthesis artifacts, with the force TE standing as a parallel analysis rather
  than a minor subsection.
- Treat the six TEs as informative and comparative. They may surface candidate
  designs and later DF questions, but they must not prematurely lock design
  criteria.
Intent:
- Extend the TE corpus so authorship/signature binding, ingress placement,
  commons history, governance, and force are explored before the next round of
  DF questions.
- Put the decentralized ecology of nodes, kernels, handlers, apps, owners,
  operators, developers, and users into explicit commons/governance context.
Constraints:
- `002.18` is author-primary and examines how the author's signature can bind
  `pCID + payload`.
- `002.19` uses the full boundary model: transport ownership, signature
  verification, admission, routing, and resource control, with common
  microkernels as comparators.
- `002.20` must treat pre-electronic commons, public infrastructure and
  utility-style commons, the open/early internet and protocol commons, and the
  last 20-30 years of centralized platforms/cloud/devops/investment patterns as
  first-class domains.
- `002.22` treats governance and force as parallel analyses with a combined
  human-and-system lens.
- `002.23` is a separate final TE artifact, not a revision of `002.21`.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`
- `docs/thought-experiments/TE-20260426-204445-kernel-vs-handler-ingress.md`
- `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`
- `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`
- `docs/thought-experiments/TE-20260426-204448-governance-vs-force.md`
- `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`
- `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`
- `docs/thought-experiments/TE-20260426-230939-aviation-without-regulators.md`

ID: DI-002-20260426-184244
Date: 2026-04-26 18:42:44 UTC
Status: active
Decision:
- TE `002.16` maps the promises, incentives, and disincentives among the major
  actors in the system: owner, sysadmin, machine, operating system, kernel,
  handlers, authors, and message senders.
- TE `002.17` tests a stricter microkernel model in which the kernel is only a
  message dispatcher and the kernel/handler conversation is limited to resource
  usage, compensation, and throttling.
- TE `002.17` must explicitly compare itself against the current handler-lifecycle
  framing from `002.10`.
- The resource-compensation angle in `002.17` must remain plural/promise-based
  and must not collapse into a single-currency or blockchain framing.
Intent:
- Clarify which promises are actually being made in the system before overloading
  the kernel with social, economic, or semantic responsibilities.
- Stress-test whether the richer lifecycle framing from `002.10` is really a
  kernel concern or should instead be pushed behind a more minimal dispatch
  boundary.
Constraints:
- Treat both TEs as analysis artifacts only; do not lock APIs or storage
  layouts.
- Keep the microkernel TE explicit about what would survive, narrow, or be
  superseded from earlier handler-lifecycle work.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-184245-system-promises.md`
- `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`

ID: DI-002-20260426-180233
Date: 2026-04-26 18:02:33 UTC
Status: active
Decision:
- TE `002.15` covers where `pCID -> handler` routing entries come from and how
  the kernel chooses among competing handlers.
- The TE must compare kernel-curated routing, handler self-registration,
  multiple-handler claims per `pCID`, imported/federated routing claims, and a
  plural-currency/promise-economy alternative.
- The TE must compare kernel roles as `policy owner`, `minimal registrar`, and
  `exchange/matcher`.
- The TE must explicitly reject single-currency/blockchain framing as a default
  design assumption.
Intent:
- Extend the handler-lifecycle work so the kernel's dispatch decision is grounded
  in an explicit model of where handler claims come from and how local selection
  works.
- Force the routing/selection discussion to take plural promises and kept/broken
  promise history seriously without assuming one universal coin or market.
Constraints:
- Treat the economy/exchange idea as one core alternative, not as the presumed
  winner.
- Do not lock runtime APIs or storage layouts in this DI entry.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-180233-pcid-handler-routing.md`

ID: DI-002-20260426-030507
Date: 2026-04-26 03:05:07 UTC
Status: active
Decision:
- TE `002.10` is reframed around the kernel lifecycle for a received
  `grid([pCID, payload])` message rather than only a generic report-schema
  boundary.
- The revised TE distinguishes three phases:
  `inspect`, `verify`, and `execute`.
- `inspect` is primarily a handler-provided, read-only payload triage step after
  pCID routing.
- `verify` remains a pre-process gate in this pass; the kernel may still discard
  after inspect and/or verify.
- `execute` means post-admission handler work, not only side effects.
- The revised TE explicitly compares `inspect -> verify -> execute` and
  `verify -> execute`, and evaluates whether `inspect -> execute` survives only
  as a constrained special case.
Intent:
- Reframe the TE from the kernel's actual decision point: whether a received
  message should be discarded or admitted into handler-controlled execution.
- Make the inspect/verify distinction defensible now that signatures are moving
  into payload and the kernel needs handler help before execution.
Constraints:
- Keep the existing TE artifact path and revise it in place.
- Do not lock a Go interface, wire format, or runtime binding in this DI entry.
- Keep the TE aligned with the earlier promise-based and hashing thought
  experiments.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`
Supersedes: DI-002-20260426-022628

ID: DI-002-20260426-022628
Date: 2026-04-26 02:26:28 UTC
Status: superseded
Decision:
- TE `002.10` evaluates the kernel/handler ABI primarily in a mixed-federation
  world where signatures are payload-only and handlers are trusted but not
  policy-authoritative.
- The ABI under test must be transport-neutral and compatible with a small,
  microkernel-like kernel boundary.
- The TE proceeds in two stages: policy/trust analysis first, then the derived
  report fields and resource-metering contract.
- The TE must explicitly apply the Promise Theory rule that agents do not make
  promises on behalf of other agents or providers.
Intent:
- Narrow the kernel/handler ABI design space before any DF questions or code/API
  decisions are locked.
- Force the TE to distinguish handler observations from kernel policy decisions
  and provider-dependent promises.
Constraints:
- Do not lock a Go interface, wire format, or runtime-specific binding in this
  DI entry.
- Keep the TE focused on the boundary contract and kernel decision inputs:
  resource triage, capability checks, and trust/reputation inputs.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`

ID: DI-002-20260426-020018
Date: 2026-04-26 02:00:18 UTC
Status: active
Decision:
- The pre-TE-ID legacy thought experiment docs are renamed into TE-ID-prefixed
  filenames using their filesystem mtimes as the TE timestamps.
- The hashing thought experiment uses `TE-20260425-173644`.
- The promises thought experiment uses `TE-20260425-162242`.
Intent:
- Bring the existing thought experiment corpus into compliance with the TE ID
  filename rule without inventing synthetic timestamps unrelated to the docs'
  actual creation history.
Constraints:
- Preserve the existing thought experiment content verbatim.
- Keep the descriptive suffixes aligned with the original doc topics.
Affects:
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/TE-20260425-173644-hashing.md`
- `docs/thought-experiments/TE-20260425-162242-promises.md`

ID: DI-002-20260426-015740
Date: 2026-04-26 01:57:40 UTC
Status: active
Decision:
- Required thought experiments (TEs) happen before final DF questions when
  multiple plausible designs remain for a non-trivial decision.
- Each TE uses a unique ID in the format `TE-YYYYMMDD-HHMMSS`.
- Each completed TE is written verbatim to `docs/thought-experiments/` in a file
  whose name begins with the TE ID and then uses a descriptive suffix.
Intent:
- Make DF questions narrower and more defensible by forcing scenario analysis
  before final decision locking.
- Keep TE artifacts auditable and unambiguous as the number of design bakeoffs
  grows.
Constraints:
- Decision-first still means decision before coding.
- TEs are analysis artifacts; DIs remain the mechanism for locking choices.
Affects:
- `AGENTS.md`
- `TODO/002-thought-experiments.md`
- `docs/thought-experiments/`

## Thought Experiments (Design Validation)

This TODO tracks the remaining thought experiments we need to run before locking
the navlog-001 wire/storage decisions for 001.4 (grid codec) and 001.5 (CAS
store, refs, ledger, journal).

Thought experiments already written:
- `docs/thought-experiments/TE-20260425-173644-hashing.md`
- `docs/thought-experiments/TE-20260425-162242-promises.md`
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

## Tasks

### Message Identity / Hashing

- [ ] 002.1 Message CID usage: enumerate all places a message CID would be used
  (dedupe, audit logs, replay detection, caching, indexing, signatures, refs).
  For each use, decide whether it requires message-level hashing or can be
  payload-only.
- [ ] 002.2 Message CID vs transport wrappers: evaluate cross-transport stability
  requirements (tagged vs untagged, future envelope families, bundling formats).
- [ ] 002.3 Message CID vs privacy/correlation: model how stable message CIDs
  enable correlation across parties and whether that is acceptable for
  PromiseGrid-at-scale.

### Envelope Shape / Extensibility

- [ ] 002.4 Strict `len==2` vs `len>=2` envelope arrays: evaluate long-horizon
  extensibility (routing hints, resource hints, receipts, compression markers)
  without breaking old kernels.
- [ ] 002.5 "Extras" semantics: if we allow extras, decide whether extras are
  hashed/signed, ignored, preserved, or forbidden by default.

### CID Encoding Inside Payload / Storage

- [ ] 002.6 CID encoding for pCID: confirm `pCID` should be CBOR `bstr` of
  `cid.Bytes()` and never tag-42.
- [ ] 002.7 CID encoding for payload fields: compare:
  - (A) always `bstr(cid.Bytes())` (format agility)
  - (B) tag-42 CID links (IPLD friendliness)
  - (C) decoder accepts both; canonical form is one
  Consider: longevity, tooling, signature binding, canonicalization risks.
- [ ] 002.8 Canonicalization failure modes: model divergent implementations
  producing different hashes for "same" semantics; decide mitigation strategy
  (strict test vectors, canonicalization profiles, conformance tests).

### Signature Location / Kernel Resource Decisions

- [ ] 002.9 Signature in envelope vs payload vs both:
  - open-adversary world
  - promise-theory / trusted-peers world
  - mixed federations interconnecting
  For each: what can kernels do without handlers? what requires handlers?
- [x] 002.10 Kernel/handler lifecycle thought experiment: for a received
  `grid([pCID, payload])`, define how the kernel chooses `discard` vs
  `process via handler`, and what `inspect`, `verify`, and `execute` mean,
  including whether the generic model is `inspect -> verify -> execute` or
  `verify -> execute`.
  See `docs/thought-experiments/TE-20260426-022628-kernel-handler-abi.md`.
- [ ] 002.11 Transition/upgrade scenarios: PQ migration, broken algorithms,
  multi-signature transitions, and how old kernels behave safely.

### Journal / Index / Recovery

- [ ] 002.12 Journal entry format: compare:
  - one-object-per-entry
  - segmented CBOR sequences
  - hash-lists / reflog-like structures
  Consider: incomplete writes, concurrent writers, recovery from lost refs,
  listing costs, and low-traffic degeneration.
- [ ] 002.13 Ref loss and recovery: model "refs are authoritative but lost" and
  how journal scanning reconstructs heads with bounded work.

### Outputs

- [ ] 002.14 For each thought experiment above, write a verbatim copy of the
  thought experiment into a corresponding file under `docs/thought-experiments/`.
  The filename must begin with the TE ID (`TE-YYYYMMDD-HHMMSS`) and then use a
  descriptive suffix. Do not create placeholder docs; only create a doc once the
  thought experiment text exists.

### Handler Routing / Registration

- [x] 002.15 pCID handler routing thought experiment: where do `pCID -> handler`
  routing entries come from, may more than one handler claim the same `pCID`,
  how does the kernel choose among competing claims, and what role - if any -
  should plural-currency/promise-economy matching play?
  See `docs/thought-experiments/TE-20260426-180233-pcid-handler-routing.md`.

### System Roles / Kernel Minimality

- [x] 002.16 System promise-map thought experiment: what promises are being made
  among owner, sysadmin, machine, operating system, kernel, handlers, authors,
  and messages, and what incentives or disincentives cause those promises to be
  kept or broken?
  See `docs/thought-experiments/TE-20260426-184245-system-promises.md`.
- [x] 002.17 Microkernel dispatcher thought experiment: what if the kernel is
  only a microkernel-style message dispatcher, and the only kernel/handler
  conversation is about resource usage, compensation, or throttling?
  See `docs/thought-experiments/TE-20260426-184246-microkernel-dispatcher.md`.

### Authorial Meaning / Signature Binding

- [x] 002.18 pCID signed-material thought experiment: if `pCID` is part of the
  author's claimed meaning, how must the author's signature bind `pCID +
  payload`, and what are the viable placement and signing-input options for
  making that happen?
  See `docs/thought-experiments/TE-20260426-204444-pcid-signed-material.md`.

### Ingress / Transport Boundary

- [x] 002.19 Kernel-vs-handler ingress thought experiment: do messages arrive
  at the kernel first or at handlers first, and how do transport ownership,
  signature verification, admission, routing, and resource control differ among
  kernel-first, handler-first, and hybrid designs?
  See `docs/thought-experiments/TE-20260426-204445-kernel-vs-handler-ingress.md`.

### Commons / Governance / Force

- [x] 002.20 Commons history survey: compare pre-electronic commons, public
  infrastructure and utility-style commons, the open/early internet and
  protocol commons, and the last 20-30 years of centralized
  platforms/cloud/devops/investment patterns, while treating the entire
  decentralized PromiseGrid ecology as a commons in its own right.
  See `docs/thought-experiments/TE-20260426-204446-commons-history-survey.md`.
- [x] 002.21 Commons governance synthesis: re-examine the existing TE corpus
  plus the new signing, ingress, and survey work through the primary goal of
  avoiding tragedy of the commons, using anchor cases plus challenge cases.
  See `docs/thought-experiments/TE-20260426-204447-commons-governance-synthesis.md`.
- [x] 002.22 Governance-vs-force thought experiment: analyze the tension
  between promise-based governance and use of force through a combined
  human-and-system lens, using both the full survey and the early synthesis as
  inputs.
  See `docs/thought-experiments/TE-20260426-204448-governance-vs-force.md`.
- [x] 002.23 Commons governance resynthesis: after the force TE, revisit the
  full TE corpus again from the commons/governance perspective without locking
  design criteria prematurely.
  See `docs/thought-experiments/TE-20260426-204449-commons-governance-resynthesis.md`.

### Runtime Environments / Host Constraints

- [x] 002.24 Runtime environments thought experiment: compare the runtime
  families PromiseGrid wants to support and examine how they change the ingress
  boundary question from `002.19` and the signed-material question from
  `002.18`, including who can own transports, who sees messages first, who can
  hold keys, and how user-authorized signing works when the runtime itself
  cannot safely hold user private keys.
  See `docs/thought-experiments/TE-20260426-220037-runtime-environments.md`.

### Applied Domain / Aviation World-Building

- [x] 002.25 Grid-based aviation thought experiment: imagine aircraft
  are invented after the internet and no FAA, FCC, ICAO, Eurocontrol, or
  comparable institutions exist; analyze how PromiseGrid-style protocols and
  institutions could fund and govern airfields, coordinate aircraft, certify
  designs and maintenance, train pilots and mechanics, manage communications and
  navigation, and evolve an aviation industry without relying on blockchain,
  cryptocurrency, or DAO framing.
  See `docs/thought-experiments/TE-20260426-230939-grid-based-aviation.md`.

### Handoffs / Cross-Repo Inputs

- [x] 002.26 PromiseGrid design handoff for wire-lab simulations: write
  `docs/promisegrid-handoff.md` as a simulation-oriented synthesis of this
  repo's PromiseGrid work, including local locks, broader design posture,
  simulation-ready hypotheses, source map, and recommended wire-lab backlog.
