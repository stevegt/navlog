## Thought Experiments (Design Validation)

This TODO tracks the remaining thought experiments we need to run before locking
the navlog-001 wire/storage decisions for 001.4 (grid codec) and 001.5 (CAS
store, refs, ledger, journal).

Reference thought experiments already written:
- `docs/thought-experiments/hashing.md`
- `docs/thought-experiments/promises.md`

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
- [ ] 002.10 Handler ABI thought experiment: if signatures are payload-only,
  define a stable kernel<->handler report schema and resource-metering contract:
  inspect vs verify phases, budgets, timeouts, partial parsing, replay claims.
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
  thought experiment into a corresponding file under `docs/thought-experiments/`
  using descriptive filenames (no numeric prefixes). Do not create placeholder
  docs; only create a doc once the thought experiment text exists.

