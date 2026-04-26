### Long-form thought experiment: what should the *message CID* mean?

We’re deciding what bytes define the identity of a “grid message” for CAS purposes.

There are three candidates:

A) Message CID = hash(canonical bytes of the *tagged* CBOR item)  
`CID( bytes( grid([pCID, payload]) ) )`

B) Message CID = hash(canonical bytes of the *untagged* CBOR array)  
`CID( bytes( [pCID, payload] ) )` and `grid(...)` is just a wrapper

C) No message CID (only payload-level CIDs exist)

To reason about this over decades, treat the message CID as a *promise*: “this CID names the same message” across contexts.

---

## Scenario 1: Multiple transports, different framing conventions

Imagine in 2040 there are many transports:

- HTTP body carries a CBOR item directly.
- WebSocket frames carry CBOR bytes.
- A log-structured file format stores CBOR in a sequence.
- A bundle format wraps messages in a container (like CAR files, tar-like bundles, or “message packs”).
- Some transports explicitly keep the `grid` tag for self-description.
- Some transports omit the tag because the channel already says “these are grid messages” (e.g., the topic name or port implies it).
- Some transports compress/encrypt at a layer that is not semantically relevant to the message.

Question: do we want the *same semantic message* to have **one CID** across these environments?

If yes, then hashing “whatever bytes happened to be on the wire” is a trap: wire bytes differ for reasons that aren’t “message meaning.” This pushes toward a canonical, transport-independent byte definition.

Now focus on the `grid` tag. The tag’s job is “this is in the PromiseGrid family.” But many transports can already convey that out-of-band. If the message identity should survive across those transports, it’s undesirable for the identity to depend on whether some hop preserved the tag.

That argues for **B** (hash the untagged array), or even **C** (only payload is content-addressed).

---

## Scenario 2: The “grid family marker” evolves

Suppose PromiseGrid lasts 80 years. Inevitably, someone decides:

- tags are inconvenient in some environments,
- or the ecosystem wants a different tag number,
- or a variant family appears (`grid2`, `g`, `promisegrid`, etc.),
- or different communities build compatible “envelope families” that are semantically equivalent but differently tagged.

If message identity includes the tag (option A), then the same `[pCID,payload]` “meaning” becomes a different message CID depending on which family wrapper was used.

That’s fine if you want the CID to mean “exactly these bytes,” but it’s not great if you want the CID to mean “the same promise/message content” independent of wrapper choices.

So option A bakes a particular envelope family marker into the content identity. Over decades, that’s a form of lock-in: not to DAG-CBOR, but to the exact `'grid'` tag choice as part of the identity.

Option B makes the identity about the *envelope fields* and leaves the tag as a convenience/compatibility wrapper.

---

## Scenario 3: Store-and-forward nodes and CAS deduplication economics

At global scale, the economic value of CAS is deduplication.

- If 10 million nodes store the same clearance message or airport/runway note update, you’d like identical messages to collapse to one object.
- If some nodes stored tagged and others untagged, option A yields two distinct CIDs, doubling storage and destroying dedupe across communities/hops.
- Option B yields one CID for the canonical message array, maximizing dedupe.

So B is better for storage economics and “one object per message semantics.”

---

## Scenario 4: “What exactly is a message” vs “what is a payload”

Now separate two identities:

- **Message identity**: `pCID` + payload bytes, as carried in the grid envelope.
- **Payload identity**: the protocol’s own content objects (which might be DAG-CBOR nodes, JSON, protobuf, etc.).

If your long-horizon stance is: “pCID defines payload format,” then payload identity may vary radically between protocols. Some payloads may already be content-addressed by the protocol; others may not.

The message CID is only useful if it adds value across protocols:
- caching,
- dedupe,
- audit references (“I saw message CID X”),
- stable references in logs.

If we define a message CID at all, it should ideally be *format-agnostic* and stable across transports. That again argues for B: the message CID is the CID of the canonical envelope array, independent of wrappers.

If we don’t need cross-protocol message CIDs, then C becomes attractive: “only the protocol defines what gets hashed.” In that world the envelope is pure routing/family framing, not content-addressed.

---

## Scenario 5: Security & ambiguity over decades

A common failure mode in long-lived systems is “two encodings that look equivalent but aren’t.”

If we allow both tagged and untagged as canonical, you get ambiguity: are these the same message or different messages?

So whichever approach you choose, you need a *rule*:

- If you choose A, you should still define that decoders accept untagged for convenience, but the canonical bytes for hashing/storage include the tag.
- If you choose B, you should define that the canonical bytes for hashing/storage are the untagged array, but wire encoders may wrap it with the `grid` tag.

Option B is clean: canonical bytes are stable; wrappers are orthogonal.

---

## Scenario 6: Promise theory / trust boundaries

Under promise theory, the semantics of a message are defined by the promises and the protocol. The `grid` tag is essentially a promise like: “this object is from the PromiseGrid family; route me accordingly.”

But a transport (or a local store) can make that same promise out-of-band (“this channel is PromiseGrid”), in which case repeating the tag is redundant.

So if the *meaning* is “pCID + payload,” then the tag is a contextual hint, not part of meaning. Meaning-based identity should not depend on whether a redundant hint was included.

Again: B.

---

## The key question: what do you want a message CID to *promise*?

1) If you want message CID to mean **“these exact bytes”**, choose A.  
Pros: simplest story; CAS stores exactly what you transmit.  
Cons: loses dedupe and stable identity across transports/wrappers.

2) If you want message CID to mean **“this semantic envelope tuple (pCID,payload,…) regardless of wrapper”**, choose B.  
Pros: stable across transports; better dedupe; tag can evolve.  
Cons: requires you to define canonical encoding rules for the untagged array, and to explain that the tag is not part of identity.

3) If you want to avoid defining message identity entirely and let each protocol decide what is addressable, choose C.  
Pros: minimalism; no envelope-level lock-in.  
Cons: fewer cross-protocol caching/dedupe/audit handles; kernels/stores need other indexing.

---

## Where this leaves us (without “locking” yet)

If the grid tag and pCID are fundamentally for **routing**, and you want longevity across transports and future envelope family variants, then the most durable definition is:

- **Canonical message bytes**: deterministic CBOR encoding of the untagged array `[pCID, payload]` (and possibly extras).
- **Wire wrapper**: `grid(...)` tag is optional framing.
- **Message CID** (if used): computed from the canonical untagged bytes.

That gives you a stable identity that’s independent of how the message was carried or wrapped, which is typically what you want from a CAS identifier intended to live for decades.

If you want, next I can do the same style of thought experiment for *envelope extensibility* (strict `len==2` vs `len>=2` with extras) under the same long-horizon assumptions.

---

## Security / cryptographic implications (B vs C)

- **Integrity handle:** B gives a stable, content-addressed identifier for the
  whole `(pCID, payload)` tuple, which is useful for tamper-evident logs, dedupe,
  and replay detection. C does not provide an envelope-level integrity handle;
  you must rely on protocol-specific IDs/hashes (or re-hash ad hoc).
- **Authenticity:** Neither B nor C authenticates anything by itself; a CID is
  just a hash. Signatures/capabilities are still required to bind "who promised
  this" and to enforce trust/policy.
- **Privacy / correlation:** B's deterministic message CID can enable
  correlation ("these two parties saw the same message") if CIDs are shared or
  logged. C makes it easier for protocols to avoid stable global identifiers (or
  to use randomized encodings) when that matters.
- **Canonicalization risk:** B requires deterministic canonical encoding for
  `[pCID, payload]`. Divergence between implementations can yield different CIDs
  for the "same" semantic message, which can be exploited for cache-miss/DoS
  patterns (not a cryptographic break, but a security/reliability concern). C
  avoids envelope-level canonicalization obligations.
- **Agility:** B standardizes one place to evolve hash/signing inputs at the
  envelope/message level; C pushes that evolution burden to each pCID protocol.
