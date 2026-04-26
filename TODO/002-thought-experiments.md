## Decision Intent Log

ID: DI-002-20260426-022628
Date: 2026-04-26 02:26:28 UTC
Status: active
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
- [x] 002.10 Handler ABI thought experiment: if signatures are payload-only,
  define a stable kernel<->handler report schema and resource-metering contract:
  inspect vs verify phases, budgets, timeouts, partial parsing, replay claims.
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
