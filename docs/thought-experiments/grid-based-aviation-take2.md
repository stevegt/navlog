# Grid-based Aviation Thought Experiment -- Take 2

## Thesis

This note sharpens a stronger PromiseGrid reading than the first aviation draft
did.

The point is not merely that aviation could be decentralized. The stronger
claim is that PromiseGrid's common message format, hash-derived protocol
identifiers, portable executable checks, and capability-style promises could
make a large part of aviation *universally legible* and *more deterministic*
without requiring central registries, one sovereign authority, one grid
currency, or voting-heavy governance.

That does **not** mean "everything becomes a blockchain", "all institutions
disappear", or "WASM replaces the world". It means:

- protocol identity can be self-certifying,
- message meaning can be portable across nodes,
- many standards can be expressed as executable predicates,
- coordination can become less arbitrary where the problem is computable,
- and trust can form by kept promises, curation, and adoption rather than by
  mandatory hierarchy.

## Grounding from earlier TEs

This note now assumes the current repo direction:

- `pCID` is a hash-derived identifier, not a centrally assigned registry token,
- common message shapes such as `grid([pCID, payload, signature])` improve graph
  legibility,
- authorial meaning and transport-local meaning may both matter,
- runtime shape matters because ingress, key custody, and sandboxing differ by
  host,
- utility-like services, stewards, labs, fields, insurers, and training
  organizations still exist even in a decentralized world.

So the positive PromiseGrid claim is:

- universal graph legibility does **not** imply universal hierarchy,
- executable standards do **not** imply institutional erasure,
- deterministic coordination does **not** imply the end of operators or service
  providers.

## What universal graph legibility actually means

Universal graph legibility means that an artifact can be understood, linked, and
evaluated across many nodes because its identity and structure are stable:

- a protocol spec can be named by hashing its own spec document,
- messages can declare their protocol family with `pCID`,
- the same artifact can be cached, referenced, and re-checked across many
  participants,
- related claims can be assembled into graphs of provenance, inspection,
  maintenance, training, or access rights.

This is strongly anti-registry. The identifier does not need to be assigned by
a central office.

But graph legibility still leaves open:

- who chooses to trust a given artifact,
- who gets to publish curated indexes or discovery views,
- who is allowed to issue a capability token worth caring about,
- who pays to maintain shared infrastructure and conflict-resolution services.

So legibility is foundational, but it does not by itself settle governance.

## Executable standards without a WASM monoculture

The useful claim is not "standards as WASM modules, not committees". The useful
claim is:

- many standards can be represented as content-addressed executable checks,
- those checks can have more than one runtime realization,
- and the executable layer complements rather than replaces institutional
  judgment.

Examples of standards that fit executable treatment well:

- message schema validation,
- unit conversions and field normalization,
- geometry and obstacle-surface checks,
- weight-and-balance calculations,
- route conflict detection,
- surveillance or transponder capability classification,
- maintenance interval predicates,
- field capability matching,
- conformance test vectors for radios, navigation profiles, or chart bundles.

Examples that remain partly social or judgment-laden even if supported by code:

- whether a training organization is competent,
- whether an accident board is independent enough,
- whether a field's local legitimacy is strong enough,
- whether an operator's safety culture is trustworthy,
- whether a carrier's passenger obligations are acceptable.

So executable standards are real and important, but they are not the whole
institutional stack.

## Runtime families for executable standards and sandboxing

WASM and WASI are important because they make portable, sandbox-friendly checks
easier. But PromiseGrid's runtime TE already established that the ecosystem
wants to run across a much larger host set. The right model is therefore
multi-runtime, not WASM-only.

### 1) Native binaries and bare metal

- useful for avionics, field services, gateways, and heavy local tooling,
- allow direct device access and low-latency execution,
- can host rich executable checks when the operator controls the machine,
- are the easiest place to overfit from, so they should not be treated as the
  only host model.

### 2) Containers

- good for field services, weather pipelines, coordination gateways, and
  operator infrastructure,
- package executable standards with controlled dependencies,
- still sit under orchestrators, sidecars, or service meshes that may shape
  ingress and secret access.

### 3) Virtual machines

- useful for durable operator or service-provider stacks,
- preserve strong execution environments while making host boundaries explicit,
- are a good fit for auditable coordination, charting, surveillance, or insurer
  infrastructure.

### 4) MicroVM and microVM-like hosts

- useful where stronger isolation is needed for risky or tenant-separated
  workloads,
- fit edge coordination, high-assurance gateways, or narrow-purpose services,
- reinforce that executable standards may run under host-shaped ingress and
  resource boundaries.

### 5) Browser-hosted WASM

- useful for pilot tools, mechanic tools, training tools, dispatch consoles, and
  public-facing aviation apps,
- great for portable validation and interactive checks,
- live under browser-controlled ingress and key management.

### 6) Serverless WASM

- useful for bursty validation, translation, and policy checks,
- good for service-local signatures or ingress-local enforcement,
- poor for durable author-held keys and poor as the universal mental model.

### 7) Standalone WASM runtimes

- useful where portability matters but browser constraints do not,
- can host reproducible check modules on desktops, servers, and appliances,
- are one portable substrate among several, not the one true one.

### 8) Standalone WASI runtimes

- make capability boundaries more explicit,
- fit environments where executable checks need narrower host contracts,
- are attractive for portable validation or policy modules in controlled hosts.

### 9) Serverless WASI-style hosts

- good for constrained, provider-shaped execution,
- reinforce the split between portable check logic and host-owned ingress or key
  custody.

### 10) Microcontrollers and firmware

- matter for onboard avionics, sensors, beacons, and constrained field
  equipment,
- usually need compact, specialized implementations rather than heavyweight
  portable runtimes,
- still benefit from content-addressed specs and test vectors even when the
  runtime is minimal.

### 11) Mobile apps

- matter for pilots, mechanics, dispatchers, and passengers,
- often combine strong secure-hardware key custody with host-mediated ingress,
- are a natural place for user-authorized signing and portable standards checks.

### 12) Game-engine embeddings

- useful for training, simulation, incident replay, and shared airspace
  rehearsal,
- remind us that PromiseGrid nodes can exist inside larger hosts and event
  loops,
- are valuable for executable training and procedure standards.

### 13) Server-side delegated-signing hosts

- matter when a service must act on behalf of a user without holding the user's
  private key,
- encourage remote-signing, passkey, or companion-device models,
- make it clear that executable standards, signing, and ingress may belong to
  different actors even within one workflow.

## What this means for aviation

Once the runtime story is widened, the aviation picture becomes stronger, not
weaker.

### Aircraft, parts, and maintenance

- airframes, engines, avionics, and parts can be described through graph-shaped
  attestations, manifests, and histories,
- executable checks can validate conformance, interval compliance, component
  compatibility, and known service-bulletin applicability,
- some checks run onboard, some in shops, some at fields, some in insurer or
  manufacturer infrastructure.

So the airframe can be *represented* as a promise graph without claiming that
all human-facing recordkeeping disappears.

### Airfields and ground operations

- field manifests, runway specs, fuel or charging capability, obstacle maps,
  maintenance status, and local procedures can all be graph-legible artifacts,
- executable checks can verify that a field bundle is internally coherent and
  compatible with a given aircraft or operation class,
- different operators can still publish competing curation views over the same
  underlying artifacts.

### Surveillance, traffic, and coordination

- deterministic conflict checks, route compatibility checks, and procedure
  validation can be executable and widely shared,
- those checks can run on aircraft, field systems, corridor services, or mobile
  clients,
- dense or high-stakes traffic still likely produces stewards, sequencing
  services, and utility-like coordinators.

So deterministic coordination reduces arbitrariness where the problem is
computable, but it does not eliminate service providers.

### Training and certification

- curricula, endorsements, checkride envelopes, and maintenance authorization
  profiles can become portable, signed, graph-legible artifacts,
- simulations and training tools can execute the same published standards across
  browsers, game engines, mobile devices, and native or cloud tooling,
- trust in instructors, schools, labs, and examiners still depends on observed
  promise-keeping and institutional reputation.

## Capability tokens, not currencies

The economic and governance core remains the same:

- no single grid currency,
- no DAO treasury,
- no default voting layer,
- no assumption that consensus means one chain or one sovereign tally.

Instead:

- a capability token is a promise by a specific issuer,
- access and priority come from who issued the promise, under what terms, and
  how often they keep it,
- standards adoption happens because fields, operators, insurers, schools,
  manufacturers, and service providers decide that certain artifacts are worth
  relying on.

That is closer to plural institutional economics than to crypto-style monetary
systems.

## What still requires institutions

PromiseGrid makes more of the aviation world legible, reproducible, and
portable. It does not remove the need for:

- insurers and underwriters,
- field operators,
- corridor stewards,
- test labs,
- accident investigators,
- training organizations,
- local governments and courts,
- discovery and curation services.

The right way to say this is:

- executable standards shrink discretionary ambiguity where code can honestly
  help,
- graph legibility makes provenance and coordination more portable,
- institutions remain necessary wherever incentives, liability, legitimacy, and
  interpretation still matter.

## Strongest surviving claim

The strongest version of this note is:

- PromiseGrid can make aviation artifacts universally *legible* without making
  them centrally *owned*.
- It can make many standards executable without collapsing all governance into
  code.
- It can make coordination more deterministic without pretending dense airspace
  will run forever with no stewards, no operators, and no utilities.
- WASM and WASI are useful tools for portable checks, but they are only part of
  a broader multi-runtime ecosystem that includes bare metal, native binaries,
  containers, VMs, microVM-like hosts, browsers, mobile, firmware, and
  delegated-signing services.

That is a much better fit for the rest of the TE corpus than the earlier
WASM-only framing.

## Open tensions

- Which standards should be specified as portable executable predicates first,
  and which should remain human-reviewed for longer?
- When should the same standard have more than one executable realization across
  runtimes?
- Which aviation functions should converge toward utility-style federations, and
  which should stay plural and locally curated?
- How much graph legibility is operationally necessary before privacy,
  competition, or safety concerns push back?
