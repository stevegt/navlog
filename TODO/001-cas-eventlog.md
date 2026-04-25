## Decision Intent Log

ID: DI-001-20260421-045059
Date: 2026-04-21 04:50:59 UTC
Status: active
Decision:
- Refs are the authoritative operational state ("movable tags"); normal reads
  must not scan the ledger/journal.
- Keep an immutable, append-only ledger chain for audit/undo/forensics; replay is
  supported but not required for routine operation.
- Each ledger entry includes `prev` (prior ledger head CID) to form a chain,
  plus the ref changes it commits (`refKey`, `from`, `to`).
- Also write an append-only time-partitioned journal/index entry on each change
  for recovery and chronological browsing.
- Refs are hybrid: per-entity refs (airport/runway/route/flight/etc.) plus a
  user/dataset `head` ref for the current workspace/project.
- Ledger retention is forever; compaction/packing may add derived
  checkpoint/index artifacts without changing the meaning of refs.
Intent:
- Avoid ScanAll() scaling issues while preserving a trustworthy audit trail and
  practical undo/forensics.
- Reduce edit conflicts under multiple concurrent editors by narrowing ref
  contention to the entity (and optional workspace head) being modified.
Constraints:
- Storage remains CAS-centric and append-only for immutable objects; refs are the
  only mutable pointers.
- Backend storage must stay abstracted (GCS/Firestore/etc. behind adapters).
- No SQL code and no reliance on SQL features (joins, transactions, etc.).
Affects:
- `TODO/001-cas-eventlog.md`
- `README.md`
- `TODO/TODO.md`
- Future: `cas/`, `cmd/navlogd/`, `model/`, `project/`, `render/`, `web/`
Supersedes: DI-001-20260420-180324

ID: DI-001-20260420-180324
Date: 2026-04-20 18:03:24
Status: superseded
Decision:
- The event log is the only source of truth; all state is restorable by replay.
- Storage is append-only immutable blobs; no update/delete of stored events.
- Wire envelope is `grid([pCID, payload, signature])`; MVP uses `signature = null`.
- Encoding is deterministic DAG-CBOR; CID hashing is stable/deterministic.
- `pCID` for the navlog event protocol is derived from the canonical bytes of
  `docs/protocol/navlog-001.md` (and the spec bytes are also stored in CAS).
- Event journal keys are time-partitioned:
  `journal/<dataset>/<YYYY>/<MM>/<DD>/<ULID>.cbor`.
- Backend is abstracted; MVP implements a GCS adapter; Firestore (if used later)
  must be behind the same abstraction.
- Per-user datasets in v1; dataset ID is the raw IAP email, path-escaped (PII in
  object names is acceptable for now).
- Naming policy for v1: use standard Go naming conventions; new functions and
  variables may be introduced as needed, so long as they are descriptive and
  consistent.
Intent:
- Avoid re-entering airport/runway/route notes by capturing reusable knowledge in
  event-sourced libraries and rendering flights from projected state.
- Ensure future migration to PromiseGrid CAS is primarily a data move: copy the
  immutable event log and replay to rebuild derived views.
Constraints:
- No SQL code and no reliance on SQL features (joins, transactions, etc.).
- No mutable canonical records; only derived caches/checkpoints are allowed and
  must be rebuildable by replay.
- Web app runs in the cloud (GCP) behind IAP; local dev may use a safe override.
Affects:
- `TODO/TODO.md`
- `TODO/001-cas-eventlog.md`
- `README.md`
- `docs/protocol/navlog-001.md`
- `go.mod`
- `cmd/navlogd/`
- `cas/`
- `grid/`
- `model/`
- `project/`
- `render/`
- `web/`
- `templates/`

## Tasks

- [x] 001.1 Write `README.md` (repo goal, architecture, dev commands)
- [x] 001.2 Write `docs/protocol/navlog-001.md` (spec + canonicalization rules)
- [x] 001.3 Create Go module (`go.mod`) and package skeleton
- [ ] 001.4 Implement `grid([pCID,payload,signature])` encoding/decoding
- [ ] 001.5 Implement CAS storage + adapters
- [ ] 001.5.1 BlobStore: PutIfAbsent/Get
- [ ] 001.5.2 RefStore: GetRef/CompareAndSwapRef (authoritative operational state)
- [ ] 001.5.3 Ledger: append immutable entries (prev-chain for audit/undo/forensics)
- [ ] 001.5.4 Journal/index: time-partitioned append for recovery/time browsing
- [ ] 001.6 Implement derived projections/indexes (airports/runways/routes/flights)
- [ ] 001.7 Implement minimal web UI + HTML download renderer
- [ ] 001.8 Local build/run sanity check (`go test ./...`, `go vet ./...`)
