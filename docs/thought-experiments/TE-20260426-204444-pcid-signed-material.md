# pCID Signed Material Thought Experiment

TE ID: `TE-20260426-204444`  
TODO: `002.18`

## Decision under test

Assume `pCID` is not merely local routing metadata. It is part of the
author's claimed meaning: "this payload is a message of protocol shape
`pCID`".

If that premise is true, then the author's signature has to bind `pCID` and
`payload` together. Otherwise an intermediary can keep the same payload,
swap the `pCID`, route the message to a different handler, and still present
a valid signature over payload alone. The author would not actually have
signed the shape claim that caused the message to be routed.

This TE asks:

- what exactly has to be bound by the author's signature?
- where can that signature live?
- should the author sign canonical `[pCID, payload]` bytes directly, or sign
  a stable hash or CID of that tuple?
- how should relay, transport, or notary signatures layer on top of the
  author's claim without replacing it?

## Grounding from earlier thought experiments

From `TE-20260425-173644-hashing.md`:

- the most stable candidate for message identity is a canonical, transport-
  independent encoding of `[pCID, payload]`,
- the outer `grid(...)` wrapper is best treated as transport framing rather
  than the core semantic object,
- if we want a durable message-level integrity handle, it should describe
  the `pCID + payload` tuple rather than wire quirks.

From `TE-20260425-162242-promises.md`:

- signatures may live in payload even when the kernel relies on handlers for
  semantic verification,
- the kernel can stay small so long as it has a defensible notion of what
  the author is actually claiming,
- trusted handlers do not remove the need for authorial accountability.

From `TE-20260426-022628-kernel-handler-abi.md` and
`TE-20260426-184246-microkernel-dispatcher.md`:

- verification may be expensive and may remain handler-mediated,
- where the signature sits matters less than what bytes the signature binds,
- any generic kernel policy becomes weaker if the author's meaning can be
  detached from the routing claim.

## Alternatives under test

### 1) Payload-only signature over payload alone

The signature lives in payload and covers payload bytes, but not `pCID`.

Pros:

- keeps the signature logic entirely inside the protocol payload,
- avoids introducing envelope-level signing rules,
- may look attractive if `pCID` is treated as only a transport hint.

Cons:

- fails immediately if `pCID` is part of the author's claimed meaning,
- allows `pCID` substitution without breaking the signature,
- gives handlers and kernels an authenticated payload but not an
  authenticated routing claim.

This alternative only works if we redefine `pCID` as local receiver metadata
rather than authorial meaning. That is a different model than the one now on
the table.

### 2) Direct author signature over canonical `[pCID, payload]`

The author's signature covers deterministic bytes for the tuple
`[pCID, payload]`. The signature itself may live in payload, in an envelope
slot, or in a detached sidecar, but the signing input is the tuple.

Pros:

- directly binds the author's protocol-shape claim to the payload,
- aligns with the strongest reading of `pCID` as authorial meaning,
- does not require a separate message-CID layer in order to express the
  signing target.

Cons:

- requires a canonical byte definition for `[pCID, payload]`,
- any disagreement about canonicalization breaks verification,
- long-term format evolution must preserve a very stable notion of the tuple
  bytes.

### 3) Author signature over a stable hash or CID of `[pCID, payload]`

The author signs a digest or content ID derived from canonical `[pCID,
payload]`, rather than the tuple bytes directly.

Pros:

- decouples signature container choice from the full tuple bytes,
- can make nested or repeated references cheaper because signers only bind a
  compact identifier,
- aligns well with CAS, logs, and audit references if the tuple already has
  a stable message CID.

Cons:

- still depends on canonicalization one step earlier, because the digest or
  CID has to come from stable tuple bytes,
- adds an extra layer that old tools must compute or fetch before they can
  verify meaning,
- makes the security story slightly more indirect: the author is signing a
  name for the tuple rather than the tuple bytes themselves.

### 4) Envelope-held detached signature over `[pCID, payload]`

The signature is outside payload in a regular envelope position or detached
companion object, but it still signs the authorial tuple.

Pros:

- makes it easy for generic tooling to locate the author's signature,
- avoids payload self-reference issues in protocols that dislike carrying
  their own signatures internally,
- can simplify multi-signature or countersignature packaging.

Cons:

- risks reintroducing a universal envelope signature format that later TEs
  have been skeptical about,
- may tempt the kernel to make stronger generic assumptions than it should,
- does not by itself solve the more important question of what bytes are
  signed.

### 5) Layered signatures: author binds `[pCID, payload]`, others sign around it

The author signs `pCID + payload`. Relays, transports, escrow agents,
notaries, or local operators may add separate signatures about forwarding,
receipt, timestamping, moderation, or local policy.

Pros:

- separates the author's meaning claim from later operational claims,
- fits a promise-oriented world where different agents sign different
  promises rather than pretending one signature does everything,
- supports countersignatures and local attestations without weakening the
  authorial binding rule.

Cons:

- introduces signature layering complexity,
- makes verification policy more context-sensitive,
- requires care so secondary signatures do not obscure which agent actually
  signed the `pCID + payload` meaning claim.

## Scenarios

### Scenario 1: pCID substitution attack

A message author creates payload `P` for protocol `A`. An intermediary swaps
`pCID A` for `pCID B` and forwards the same payload.

- payload-only signing over `P` fails to prevent this,
- direct tuple signing prevents it,
- tuple-hash signing prevents it if the hash or CID is derived from `[A,P]`,
- detached or envelope signatures are fine if they still cover `[A,P]`.

This scenario is the decisive reason that `pCID` has to be in the signed
material if it is part of the author's meaning.

### Scenario 2: multiple transports and wrappers

The same semantic message may move through HTTP, a queue, local storage, or
a relay protocol that keeps or drops the outer `grid(...)` wrapper.

- signing wire bytes is fragile because wrappers vary,
- signing canonical `[pCID, payload]` survives transport differences,
- signing a CID of `[pCID, payload]` also survives if everyone derives the
  same CID.

This favors signing the canonical tuple or its stable name, not transport
wrapper bytes.

### Scenario 3: payload-contained signature without envelope lock-in

Suppose we still want signatures to live in payload so handlers can interpret
them in protocol-specific ways.

That is still possible. The rule becomes:

- the signature is stored in payload,
- but the signing input is canonical `[pCID, payload-with-signature-removed]`
  or an equivalent stable representation.

This is more complex than signing payload alone, but it preserves the user's
desire to keep envelope routing format-agnostic.

### Scenario 4: nested messages

A flight-plan message may embed airport-note messages or ATC promises.

- if each nested author signs only payload, nested routing meaning can be
  rewritten by wrappers,
- if each nested author signs its own `[pCID, payload]`, then the nesting
  structure can change while the embedded claims remain attributable.

This argues for authorial tuple binding at every message boundary.

### Scenario 5: multi-signature or countersignature workflows

A relay, reviewer, or regulator wants to add a signature after the author.

That does not weaken the core rule. It clarifies it:

- the author signs the message meaning,
- later actors sign their own promises about forwarding, validation,
  countersigning, escrow, or acceptance.

The more layered the workflow becomes, the more useful it is to keep the
author's claim sharply separated from later claims.

## What this TE rejects

Under the current premise, this TE rejects one family clearly:

- author signs payload alone while `pCID` remains outside the signed material.

That family only works if we change the semantics and say `pCID` is not part
of the author's claim. If we do not want that semantic change, payload-only
signing is too weak.

## What survives

Two primary families survive:

1. author signs canonical `[pCID, payload]` directly,
2. author signs a stable hash or CID derived from `[pCID, payload]`.

Signature placement is still open:

- in payload,
- in an envelope slot,
- or detached.

The important point is that placement and signing target are separate
questions. Payload-resident signatures remain viable so long as the signing
input still binds `pCID + payload`.

## Candidate implications without locking them

- If the repo keeps a message-CID concept, that CID becomes a strong signing
  target candidate.
- If the repo avoids message CIDs, then direct canonical tuple signing
  becomes the simpler story.
- Relay, notary, and transport signatures should be treated as additional
  promises layered around the author's promise, not as substitutes for it.

## Follow-on questions

- Do we want the canonical signing target to be tuple bytes directly or a
  tuple-derived CID?
- Did we err in previous TEs by concluding that the envelope is `[pCID,
  payload]` rather than `[pCID, payload, signature]`?
- If signatures live in payload, what exact representation of payload is used
  when the signature field itself is present?
- Should every authorial signature be required to bind `[pCID, payload]`, or
  will some protocols be allowed to treat `pCID` as only local metadata?
- How should secondary signatures declare that they are not the primary
  authorial meaning claim?
