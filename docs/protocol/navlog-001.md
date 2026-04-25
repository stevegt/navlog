# navlog-001: Event Protocol (grid envelope)

This document specifies the MVP event protocol for the navlog/flightpad app.

## 1. Envelope

All events are encoded as a CBOR item tagged with the PromiseGrid "grid" tag.

The tagged value is a CBOR array with three elements:

```cbor
grid([
  pCID,
  payload,
  signature
])
```

- `pCID`: protocol CID; for this protocol it is the CID of the canonical bytes of
  this specification document (Section 5).
- `payload`: protocol-defined event payload (Section 2).
- `signature`: reserved for future signing. MVP uses `null`.

## 2. Payload

The payload is a CBOR map with string keys:

- `id` (string): ULID
- `ts` (string): RFC3339 timestamp
- `actor` (string): authenticated user identity (e.g., email)
- `entity` (string): entity key (e.g., `airport:KPAE`)
- `type` (string): event type (e.g., `AirportNotesSet`)
- `data` (map): event-type-specific data

The payload map is stored in deterministic DAG-CBOR so its bytes are stable for
hashing and storage.

## 3. Content IDs

Events and protocol specs are referenced by content identifiers (CIDs). For MVP:

- CID version: v1
- Multicodec:
  - events: `dag-cbor`
  - spec docs: `raw` (the canonical bytes of this Markdown file)
- Multihash: `sha2-256`

## 4. Deterministic Encoding Rules

All CBOR encoding used for CID hashing MUST be deterministic.

For CBOR structures (events), use deterministic encoding compatible with DAG-CBOR
constraints (notably: map keys are sorted by their encoded bytes).

## 5. Canonicalization of This Spec (for pCID)

To compute this protocol's `pCID`, canonicalize the bytes of this Markdown file
as follows:

1. Encode as UTF-8.
2. Use LF line endings.
3. Ensure the file ends with exactly one trailing newline.
4. Do not trim internal whitespace.

Then compute `pCID = CIDv1(raw, sha2-256(canonical_bytes))`.

## 6. Forward Compatibility

Future protocol revisions MUST use a new `pCID` (a new spec blob CID) and MUST
retain the outer envelope shape `grid([pCID, payload, signature])`.

