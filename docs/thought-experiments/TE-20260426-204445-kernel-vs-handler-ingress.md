# Kernel-vs-Handler Ingress Thought Experiment

TE ID: `TE-20260426-204445`  
TODO: `002.19`

## Decision under test

When a message enters the machine, which component sees it first?

- the kernel as transport listener and first admission gate,
- a handler that owns one or more transports and passes admitted messages to
  the kernel later,
- or some hybrid arrangement.

This is not only a socket-placement question. It determines:

- who pays the first parse and verification cost,
- who enforces resource limits before semantic work begins,
- who owns transport adaptation and protocol evolution,
- who has the first opportunity to reject junk,
- who gets a unified audit/accounting view across transports.

## Grounding from earlier thought experiments

From `TE-20260426-022628-kernel-handler-abi.md`:

- the richer lifecycle model distinguishes `inspect`, `verify`, and
  `execute`,
- the kernel may want facts before admission,
- the boundary matters because verification may be expensive and protocol-
  specific.

From `TE-20260426-184246-microkernel-dispatcher.md`:

- there is pressure to keep the kernel close to a microkernel-style
  dispatcher,
- there is also pressure to let handlers own more semantic work,
- the strictest model would limit kernel/handler conversation mostly to
  resource usage and dispatch.

From `TE-20260426-180233-pcid-handler-routing.md`:

- the kernel is the local policy owner for handler selection,
- more than one handler may claim the same `pCID`,
- routing, trust history, and resource policy may all affect dispatch.

From `TE-20260426-204444-pcid-signed-material.md`:

- if `pCID` is part of the author's claimed meaning, then verification has to
  bind `pCID + payload`,
- that verification may still happen in payload-aware handlers,
- but ingress placement affects who gets first access to the signed claim.

## Boundary models under test

### 1) Kernel-first ingress

The kernel owns transport listeners, accepts connections or datagrams,
performs the first envelope parse, and only then routes to handlers.

Pros:

- one place for cross-transport resource accounting,
- one place for coarse filtering, quotas, and audit,
- easier to guarantee that every incoming byte crosses the same admission
  and metering boundary,
- simpler to correlate multiple transports carrying the same message family.

Cons:

- pressures the kernel to know too much about transports and their failure
  modes,
- may bloat the kernel into a transport hub rather than a small dispatcher,
- can slow protocol experimentation because new transports now need kernel
  support or kernel mediation.

### 2) Handler-first ingress

A handler owns zero or more transports directly. It listens, parses,
verifies, and only passes admitted messages onward to the kernel or local
runtime.

Pros:

- strongest alignment with microkernel instincts: policy and protocol logic
  stay out of the kernel,
- new transports can be added by handler authors without changing the kernel,
- transport-specific verification can happen close to the code that actually
  understands the payload and signature rules.

Cons:

- resource governance is weaker unless handlers operate under tight kernel-
  issued budgets or contracts,
- the machine loses a single first-line accounting point,
- two handlers listening on different transports may duplicate admission
  logic and accounting,
- buggy or greedy handlers can absorb too many resources before the kernel
  can react.

### 3) Hybrid ingress

Some transports terminate at the kernel, some at handlers, or the kernel owns
minimal listener and budget primitives while handlers own protocol-specific
verification and adaptation.

Pros:

- preserves a small shared admission and accounting core,
- still lets handlers evolve transports and semantic verification,
- supports cases where one transport is generic infrastructure while another
  is handler-owned niche infrastructure,
- is closer to how many real systems evolve over time.

Cons:

- can become muddled if the split is not principled,
- makes reasoning about trust and failure more complex,
- risks duplicated logic between kernel and handlers unless the contract is
  very sharp.

## Compare against common microkernels

Common microkernels are a useful analogy, but not a direct template.

`Mach`, `QNX`, `MINIX3`, and `seL4` all keep privileged mechanism small and
push a large amount of policy and service logic into user space. Drivers,
filesystems, network stacks, or servers often live outside the kernel.

But these systems still give the kernel a real job:

- scheduling,
- address-space control,
- IPC primitives,
- interrupts,
- capability mediation or equivalent low-level authority.

The lesson is not "services should bypass the kernel." The lesson is
narrower: keep the kernel's job universal, low-level, and hard to fake.

Applied here:

- a kernel-first design says transport ingress is one of those universal
  primitives,
- a handler-first design says transport ingress is service-specific policy,
- a hybrid says the kernel owns the universal resource and dispatch
  primitives, while handlers may still own some transport endpoints.

## Scenarios

### Scenario 1: adding a new transport quickly

Suppose a handler author wants to add a new transport optimized for satellite
links or cockpit-local peer exchange.

- kernel-first slows this down because the kernel must learn the new
  listener or framing path,
- handler-first makes this easy,
- hybrid works if the kernel contract is small enough that the new handler
  can still plug into common accounting.

This scenario favors handler-first or hybrid.

### Scenario 2: expensive signature verification on arrival

Suppose verification is costly because the message carries layered signatures,
revocation checks, or provider-dependent capability promises.

- kernel-first can refuse work early but risks dragging expensive semantics
  into the kernel boundary,
- handler-first keeps semantic cost with the handler but may let handlers
  burn resources too early,
- hybrid can force handlers to spend from budgets while keeping the kernel
  out of semantic verification details.

This scenario favors hybrid if the kernel can meter handler-owned ingress.

### Scenario 3: junk or adversarial traffic flood

Suppose one transport is flooded with garbage.

- kernel-first gives the cleanest universal choke point,
- handler-first only works if handlers can be throttled or cut off before the
  machine is starved,
- hybrid works if the kernel owns the first hard resource limits even when a
  handler owns the transport logic.

This scenario strongly pressures the design toward at least some kernel-
visible resource boundary.

### Scenario 4: one semantic message, many transports

The same `grid([pCID, payload])` object may arrive by radio relay, queue,
local sync, or direct peer exchange.

- kernel-first makes dedupe and audit easier because all transports converge
  in one place,
- handler-first can still dedupe later, but each handler may have its own
  ingress view,
- hybrid depends on whether the kernel sees a normalized message object after
  handler-side transport adaptation.

This scenario pressures handler-first designs to define a clear handoff into
a kernel-visible normalized object.

### Scenario 5: multiple handlers claim the same pCID

Earlier TEs already allowed more than one handler to claim a `pCID`.

- kernel-first lets the kernel route after ingress using one local policy
  table,
- handler-first risks each ingress-owning handler becoming its own hidden
  routing domain,
- hybrid can preserve kernel routing authority if handlers must hand off to a
  common dispatch step after transport adaptation.

This scenario favors kernel-visible routing, even if not necessarily kernel-
owned transport listeners.

### Scenario 6: buggy or compromised handler

A trusted handler turns out to be buggy, stale, or compromised.

- kernel-first narrows the damage because the handler receives only messages
  the kernel has already admitted and budgeted,
- handler-first gives the handler more direct control over transport state,
- hybrid can contain damage if handler-owned listeners are still subordinate
  to kernel-issued budgets, revocation, and accounting.

This scenario again pressures handler-first designs to prove a strong
resource-governance story.

## What common microkernel practice suggests

Common microkernels do not eliminate central mediation. They choose carefully
which mediation remains central.

That suggests two weak conclusions:

- a pure handler-first design is only credible if the kernel still controls a
  small set of non-bypassable resource primitives,
- a pure kernel-first design is only credible if ingress really is one of the
  non-bypassable primitives rather than just a convenience.

## What survives

Three families survive, but not equally well.

- Pure kernel-first survives because it gives the cleanest shared accounting
  and routing boundary.
- Pure handler-first survives only if handler-owned listeners operate under
  strict kernel resource contracts and a common normalized handoff.
- Hybrid survives most comfortably because it can preserve a small kernel
  boundary while letting handlers own protocol-specific transport logic.

The current pressure therefore points toward a hybrid or constrained handler-
first model rather than a simplistic all-kernel or no-kernel answer.

## Candidate implications without locking them

- The kernel may need to own a universal budget, admission, and routing layer
  even if it does not own every listener.
- Handlers may need to prove resource consumption to the kernel regardless of
  who owns the socket.
- Common microkernel analogies argue for small privileged primitives, not for
  pretending that transport and admission governance do not exist.

## Follow-on questions

- Must every incoming message cross a kernel-visible normalized handoff,
  even when the transport listener is handler-owned?
- Which transport functions, if any, are non-bypassable kernel primitives?
- Can handler-owned ingress exist without giving up cross-transport auditing,
  quotas, and routing authority?
- Is the right long-term model "kernel owns ingress," "handlers own ingress
  under kernel budgets," or a deliberately mixed architecture?
