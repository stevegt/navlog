# Navlog / Flightpad Builder

This repo is building a web app that generates an Apple Notes-friendly, stylus-
friendly "flightpad" HTML document for a trip (multiple legs in one long scroll).

## Core Goal

Avoid re-entering the same information across legs and flights by treating
airport/runway/route knowledge as reusable library data, and generating a new
flightpad for each trip by combining:

- Global library notes (airports, runways, routes, waypoints, constraints), and
- Flight-specific notes (today's plan and reminders).

## Storage Model (CAS + Refs + Ledger)

Refs are the authoritative operational state.

- Immutable objects are stored as content-addressed blobs (CAS).
- "Refs" are movable tags that point to current state roots (hybrid: per-entity
  refs plus a per-user `head` ref for the current workspace/project).
- Each change appends an immutable ledger entry (hash-chained via `prev`) and
  advances the relevant refs via compare-and-swap.
- A time-partitioned journal/index entry is also appended for recovery and
  chronological browsing (not required for normal reads).
- The ledger can be replayed for audit/undo/forensics; derived snapshots/indexes
  may be compacted/packed freely because refs define operational state.

The wire envelope for stored events is CBOR tagged as a PromiseGrid-style grid
message:

`grid([ pCID, payload, signature ])`

MVP uses `signature = null` and deterministic DAG-CBOR encoding.

## Status

This is early-stage scaffolding plus prototypes under `x/`.

## Development

Planned local commands (once the server code exists):

- Run server: `go run ./cmd/navlogd`
- Run tests: `go test ./...`
- Vet: `go vet ./...`
- Errcheck (if installed): `errcheck ./...`

## References

- `docs/protocol/navlog-001.md` defines the event protocol and canonicalization.
- `x/` contains early HTML/PDF layout experiments and notes.
