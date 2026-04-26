# System Promise Map Thought Experiment

TE ID: `TE-20260426-184245`  
TODO: `002.16`

## Decision under test

Before deciding how much the kernel should know, decide, or enforce, we need a
map of the promises in the system.

This TE asks:

- what promises are being made among the major actors?
- what incentive does each actor have to keep those promises?
- what disincentive or penalty exists for breaking them?
- which promises can the kernel actually observe or enforce?
- which promises are social, economic, or reputational rather than technical?

## Grounding from earlier thought experiments

From `TE-20260425-162242-promises.md`:

- trust grows from promises kept and shrinks from promises broken,
- capability tokens are provider promises,
- the kernel is not the universal source of truth about other actors.

From `TE-20260426-022628-kernel-handler-abi.md`:

- the kernel wants a clear admission boundary,
- handlers do semantic work,
- resource and trust decisions remain local.

From `TE-20260426-180233-pcid-handler-routing.md`:

- the kernel remains the policy owner for local handler selection,
- but its inputs include claims, histories, and optional market-like terms.

Taken together, these TEs imply that the system is held together by many
different promises, only some of which are directly technical.

## Actor set

This TE considers at least these actors:

- machine owner,
- sysadmin,
- physical machine,
- operating system,
- PromiseGrid kernel,
- handlers,
- kernel author,
- handler author,
- message author / sender,
- provider / peer / federation.

Strictly speaking, some of these are not autonomous in the same way. For
example, a physical machine or a message does not "intend" in the human sense.
But in promise-theory terms, they still participate as loci of behavior and
expectation.

## Directed promise sketches

## 1) Owner <-> sysadmin

### Owner -> sysadmin

The owner promises:

- authority to operate the system,
- payment, budget, or other support,
- legitimacy to make operational choices on the owner's behalf.

Incentive to keep that promise:

- the owner wants a working system,
- wants reduced operational burden,
- and wants reliable service or mission outcomes.

Disincentive for breaking it:

- the sysadmin leaves,
- service quality collapses,
- trust decays and replacements become expensive.

### Sysadmin -> owner

The sysadmin promises:

- to keep the machine, OS, kernel, and handlers operating acceptably,
- to protect the owner's assets and priorities,
- to make local policy choices that serve the owner's interests.

Incentive to keep that promise:

- compensation,
- reputation,
- professional identity,
- desire for stable operations.

Disincentive for breaking it:

- job loss,
- blame,
- legal or contractual consequences,
- loss of future trust.

## 2) Owner <-> machine

### Owner -> machine

The owner promises:

- purchase, power, housing, maintenance budget, and continued care.

### Machine -> owner

The machine "promises":

- compute, storage, network connectivity, and physical durability within its
  design limits.

Incentive/disincentive note:

- the machine has no human-style incentive; its "kept promise" is simply proper
  functioning under maintained conditions.
- the owner's incentive is obvious utility: the machine exists to do work the
  owner values.

## 3) Sysadmin <-> machine

### Sysadmin -> machine

The sysadmin promises:

- proper installation,
- maintenance,
- updates,
- monitoring,
- replacement when degraded.

### Machine -> sysadmin

The machine promises:

- bounded, observable behavior,
- stable resource availability when healthy,
- faults that can be diagnosed and reacted to.

The sysadmin's incentive is to keep the substrate predictable; the machine's
"disincentive" is simply breakdown, which destroys its usefulness.

## 4) Operating system <-> sysadmin

### Operating system -> sysadmin

The OS promises:

- process model,
- filesystems,
- device access,
- isolation,
- logging,
- administrative control surfaces.

### Sysadmin -> operating system

The sysadmin promises:

- installation,
- configuration,
- updates,
- patching,
- sane policy,
- resource provisioning.

Incentives:

- the sysadmin wants leverage and predictability,
- the OS "wants" continued use in the sense that usable systems persist in
  operator preference.

## 5) Operating system <-> PromiseGrid kernel

### Operating system -> kernel

The OS promises:

- execution environment,
- memory/process isolation,
- scheduling,
- IPC/network primitives,
- files and persistence APIs,
- local security boundaries.

### Kernel -> operating system

The PromiseGrid kernel promises:

- to behave as a well-formed workload,
- to stay inside granted resources,
- to use OS interfaces correctly,
- to not force the OS into undefined or abusive behavior.

The OS author's incentive is adoption by sysadmins; the kernel's incentive is
continued viability on real machines. Breaking these promises leads to crashes,
resource exhaustion, rejection, and eventual non-adoption.

## 6) Kernel <-> sysadmin

### Kernel -> sysadmin

The kernel promises:

- local policy enforcement for handler selection and execution,
- resource accounting and throttling,
- auditable routing and dispatch decisions,
- a stable enough operational model that a sysadmin can reason about it.

### Sysadmin -> kernel

The sysadmin promises:

- installation,
- configuration,
- trust boundaries,
- allowed handlers,
- operational budgets.

The sysadmin's incentive is a manageable system. The kernel author's incentive
is adoption by sysadmins and operators. If the kernel breaks promises, it is
removed, replaced, or never deployed again.

## 7) Kernel author <-> sysadmin

### Kernel author -> sysadmin

The kernel author promises:

- that the kernel is worth deploying,
- that its behavior is explainable,
- that it respects local sovereignty,
- that it does not require ideological baggage (for example a fake universal
  market or chain) to function.

### Sysadmin -> kernel author

The sysadmin promises, indirectly:

- adoption,
- bug reports,
- trust,
- operational feedback,
- perhaps payment or reputation if the kernel proves useful.

The kernel author's incentive is adoption, credibility, and impact. The
sysadmin's incentive is a tool that reduces complexity without stealing policy.

## 8) Handler author <-> sysadmin

### Handler author -> sysadmin

The handler author promises:

- competence for one or more `pCID`s,
- acceptable operational behavior,
- updates when the protocol evolves,
- compatibility with local policy and budgets.

### Sysadmin -> handler author

The sysadmin promises, indirectly:

- acceptance into the local handler set,
- operational trust,
- continued deployment if promises are kept.

The handler author's incentive is acceptance, use, reputation, and possibly
payment or reciprocal access. The sysadmin's incentive is useful protocol
coverage without unsafe or unmaintained code.

## 9) Handler <-> kernel

### Handler -> kernel

The handler promises:

- "I can serve this `pCID`",
- bounded behavior under local budgets,
- honest reporting about capabilities, costs, and failures,
- protocol-competent inspect/verify/execute behavior if that model survives.

### Kernel -> handler

The kernel promises:

- fair or policy-driven access to dispatch opportunities,
- resources within granted budgets,
- stable local selection rules,
- revocation or throttling based on known policy rather than arbitrary chaos.

The handler's incentive is continued selection and resource access. The kernel's
disincentive for tolerating a bad handler is obvious: wasted resources and bad
dispatch decisions.

## 10) Provider / peer / federation <-> kernel or handler

Providers promise:

- to honor or reject their own capability promises,
- to publish routing claims, identities, or verification material,
- to behave consistently enough that local trust can form.

Kernels and handlers promise:

- not to project more trust onto providers than local evidence justifies,
- to treat imported claims as evidence rather than commands.

The provider's incentive is reciprocal interoperability, trust, and utility. The
local system's incentive is safe cooperation without surrendering sovereignty.

## 11) Message author -> handler / kernel

This edge needs care.

A message is not an independent moral agent. The real promiser is the author or
sender who uses the message as the vehicle of a claim.

So, strictly speaking:

- the **message author** promises things **through** the message,
- the **message** carries those promises in claim form,
- the **handler** interprets and tests them,
- the **kernel** mostly cares whether processing the message is worth local
  resources and trust.

What the message author is promising through the message may include:

- "this payload has the shape I claim,"
- "these identity claims are mine to make,"
- "this request is worth your resources,"
- "these tokens or capabilities should be honored,"
- "I am willing to bear the consequences if these claims are false."

The message author's incentive is to get work done, gain access, coordinate
action, or receive service. The disincentive for lying is throttling, rejection,
reputation loss, revoked capabilities, and eventually economic or social loss.

## 12) What does a message promise the handler?

Colloquially, the message "promises" at least:

- parseable structure,
- claimed protocol meaning,
- claimed identity or capability-bearing content,
- hints about expected resource cost.

But these are only claimed promises until the handler inspects or verifies them.
The handler should never confuse "message says so" with "author has kept the
promise."

## Incentive families

The promises above are sustained by a few broad incentive families:

- **economic**: payment, budget, reciprocal service, continued access,
- **operational**: keeping the system running, avoiding outages, avoiding manual
  firefighting,
- **reputational**: being seen as trustworthy by owners, sysadmins, peers, and
  downstream users,
- **policy / coercive**: revocation, throttling, removal, non-selection,
- **mission-oriented**: accomplishing the real-world purpose that motivated the
  system in the first place.

## What the kernel can actually enforce

The kernel can directly enforce only a subset of the promise graph.

It can reasonably enforce or observe:

- local resource budgets,
- dispatch eligibility,
- handler acceptance or revocation,
- routing preference,
- local throttling,
- local history of kept/broken operational promises.

It cannot directly enforce:

- owner payment to sysadmin,
- provider truth in the abstract,
- author sincerity,
- social or reputational consequences outside the local domain.

So the kernel should not pretend to be the sovereign of the whole promise graph.
It is one actor inside it, with strong local powers and weak global powers.

## Conclusions

The promise map suggests:

- many promises in the system are not purely technical,
- the kernel sits in the middle of several operational promise edges,
- but it does not own every promise in the system,
- and messages are only carriers of authored promises, not independent truth
  engines.

The strongest kernel-relevant promises are:

- handler -> kernel: capability, honesty, boundedness,
- kernel -> sysadmin: policy and resource enforcement,
- sysadmin -> owner: operational reliability,
- message author -> handler: claim bundle to be interpreted and tested.

## Exact DF question that remains

Before implementation starts using promise history more broadly, one DF choice
should be answered:

- which promise edges need first-class state in v1:
  - only handler/kernel operational promises,
  - or also richer cross-actor history such as peer/provider/message-author
    reputation?

This TE recommends starting with first-class local operational promises and
treating broader social/economic promises as inputs that may later influence
policy, not as immediate kernel state.

## Implications for open TODOs and DIs

- `002.17` should use this promise map to avoid giving the kernel responsibility
  for promises it cannot realistically enforce.
- `002.15` should treat handler claims as one promise edge among many, not as a
  complete model of the system.
- `001.4` and `001.5` should avoid turning every promise in the ecosystem into a
  first-class kernel storage primitive.
