# Commons Governance Resynthesis Thought Experiment

TE ID: `TE-20260426-204449`  
TODO: `002.23`

## Decision under test

Revisit the full TE corpus again, now that the signature-binding, ingress,
history-survey, early synthesis, and governance-vs-force work all exist.

This is the resynthesis step. Its job is not to freeze design criteria yet.
Its job is to see what the whole corpus now says, taken together, about how a
decentralized system avoids tragedy of the commons without collapsing into
enclosure.

## What changed since the earlier synthesis

The earlier synthesis already suggested that the system would have to juggle:

- local plural governance,
- utility-like shared infrastructure,
- anti-enclosure pressure,
- anti-collapse pressure.

The new TEs sharpened four things:

- `TE-20260426-204444-pcid-signed-material.md` clarified that accountable
  authorship requires binding `pCID + payload` when `pCID` is part of meaning,
  and it now explicitly compares that author-primary model against a
  transport-sender-signed `['grid' tag, pCID, payload]` envelope model,
- `TE-20260426-204445-kernel-vs-handler-ingress.md` clarified that ingress is
  also a governance-placement question,
- `TE-20260426-204446-commons-history-survey.md` widened the comparison set and
  clarified that many PromiseGrid naming functions are mathematical or
  self-certifying rather than centrally registry-driven,
- `TE-20260426-204448-governance-vs-force.md` clarified that governance and
  coercion cannot be discussed as if they are unrelated.

## Revisit the major themes across the full corpus

### 1) The commons is multi-layered

The system has at least five overlapping commons:

- machine resources,
- local operator attention,
- protocol identity, mathematical naming, discoverability, and legitimacy,
- handler and app maintenance labor,
- the broader inter-operator network and user trust ecology.

Earlier TEs sometimes focused on one layer at a time. The resynthesis says no
single governance mechanism will cover all five cleanly.

### 2) Meaning, attribution, and governance are linked

The pCID-signing TE makes a new point unavoidable:

- accountable meaning is a governance primitive.

If authorship is weak, then cost attribution, moderation, routing trust, and
historical accountability all weaken. Stable signed meaning is not the whole
governance story, but it is part of the commons bookkeeping layer.

The updated signing TE also makes a second point unavoidable:

- governance has to decide whether the primary protocol-shape promise is an
  end-to-end authorial claim, a sender-local ingress claim, or a layered
  combination of both.

### 3) Mathematical naming changes governance, not the need for governance

The updated survey and synthesis now make a sharper claim:

- PromiseGrid can use mathematical or self-certifying identifiers such as
  hash-derived `pCID`s, so protocol identity does not have to depend on one
  central naming authority,
- but that does not remove commons pressure around discovery, curation, trust,
  migration, and handler-claim selection,
- which means the real governance question is not "who runs the registry?" but
  "which institutions around the hashes become socially authoritative?".

### 4) Boundary placement is governance placement

The ingress TE and the kernel-lifecycle TE now look like part of the same
larger question:

- where in the system do observation, refusal, admission, and accounting
  happen?

Kernel-first, handler-first, and hybrid designs are therefore not only
modularity choices. They distribute governance power differently.

### 5) Minimal kernels are attractive but not self-justifying

The microkernel TE still contributes an important caution:

- do not overload the kernel with semantics or social judgment.

But the newer commons work adds an equally important caution:

- do not strip the kernel so far down that no institution can defend shared
  resources coherently.

Minimality survives as a design pressure, not as an automatic winner.

### 6) Pluralism and utility pressure must coexist

The routing TE, the survey, and the synthesis all point the same way:

- plurality of handlers, operators, and local policy is valuable,
- some shared layers still need utility-like durability, maintenance,
  accounting, and discovery/curation around self-certifying identifiers.

The resynthesis does not choose exactly which layers are which, but it makes
the split itself unavoidable.

### 6) Governance without a hard edge is naive; force without legitimacy is
enclosure

The force TE blocks two bad fantasies:

- the fantasy that pure promises and goodwill can defend a large commons with
  no refusal or exclusion powers,
- the fantasy that strong centralized control is just efficient governance
  without political cost.

The system will need some hard boundaries. The harder question is how to keep
those boundaries local, explicit, reviewable, and resistant to capture.

## Candidate architectural families, revisited without choosing one

### Family A: local-plural federation

Features:

- handler pluralism,
- strong local operator policy,
- thin shared substrate,
- high reliance on promise history and local trust.

Commons upside:

- high exit and anti-enclosure value,
- low risk of one operator setting reality for everyone.

Commons risk:

- fragmented abuse response,
- weak maintenance funding for shared layers,
- high operator burden.

### Family B: utility-core federation

Features:

- local pluralism at the edges,
- stronger shared accounting, maintenance, routing, ingress substrate, or
  discovery/curation layers around self-certifying identifiers,
- explicit support for durable shared institutions.

Commons upside:

- better defense against collapse from under-maintenance,
- clearer common observability and capacity planning,
- stronger response to free-riding and attack.

Commons risk:

- drift toward capture,
- temptation to turn utility layers into platform sovereignty,
- harder political fights over the shared core.

### Family C: managed platform

Features:

- a small number of operators own most hard problems,
- the commons is replaced by tenancy under managed services.

Commons upside:

- operational simplicity,
- easy funding and abuse response.

Commons risk:

- enclosure by design,
- weak exit,
- reproduction of the platform patterns this project is trying to avoid.

This family remains useful mainly as a foil and stress test.

## What now looks most important to settle later

Without yet locking criteria, the full corpus now makes four later decision
clusters look central.

### Cluster 1: accountable message meaning

- Do we require authorial signatures to bind `[pCID, payload]` universally?
- Is the primary meaning claim author-signed `[pCID, payload]`,
  sender-signed `['grid' tag, pCID, payload]`, or a layered combination?
- Do we sign tuple bytes or a tuple-derived CID?
- How do secondary signatures avoid obscuring the primary authorial claim?

### Cluster 2: ingress and admission governance

- Is there a kernel-visible normalized handoff for every incoming message?
- Which ingress powers are universal primitives, and which belong to handlers?
- How are resource budgets enforced across transports?

### Cluster 3: shared-resource accounting and maintenance

- Which shared layers need utility-style funding and observability?
- Which maintenance burdens can stay local, and which need stronger shared
  institutions?
- How are free-riding and invisible labor surfaced?
- Which discovery, curation, and migration layers around self-certifying
  identifiers become commons institutions in their own right?

### Cluster 4: governance and coercion boundaries

- What is the ladder from soft governance to hard exclusion?
- Which actors may invoke which hard powers?
- How do we prevent emergency and anti-abuse powers from becoming enclosure
  tools?

## What the resynthesis does not do

It still does not choose:

- kernel-first vs handler-first,
- payload vs envelope signature placement,
- one handler discovery/curation or routing-economy model around
  self-certifying identifiers,
- one institutional shape for the shared core.

That is deliberate. The corpus is now rich enough that forcing a premature
lock would mostly disguise unresolved governance questions as engineering
defaults.

## Final carry-forward questions

- Which parts of the architecture are commons-defense mechanisms, and which
  are enclosure risks in disguise?
- Which local freedoms are essential, and which shared institutions are worth
  strengthening even at some cost to autonomy?
- How do we preserve plural governance while still making maintenance,
  accountability, and emergency response real?
- How do we keep mathematical naming from quietly hardening into social
  authority without accountability around discovery and curation?
- What should the next DF round try to lock first: authorship/signing,
  ingress/governance boundary, or shared-resource accounting?
