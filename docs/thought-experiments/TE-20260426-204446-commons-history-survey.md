# Commons History Survey Thought Experiment

TE ID: `TE-20260426-204446`  
TODO: `002.20`

## Decision under test

Re-examine the entire design space with a primary goal: avoid tragedy of the
commons.

Here the commons is not only one machine's CPU or one kernel's memory. The
commons includes the whole decentralized ecology of nodes, kernels, handlers,
apps, authors, users, operators, owners, protocol maintainers, and shared
expectations that make the network usable.

This TE is a full survey, not yet a design-synthesis document. Its job is to
surface patterns, analogies, and warnings from earlier eras and adjacent
systems.

## Why a history survey matters here

A system can fail as a commons in several distinct ways:

- overuse of a scarce resource,
- under-maintenance of shared infrastructure,
- enclosure by a dominant actor,
- subsidy patterns that hide true costs until too late,
- externalization of moderation, security, or care labor onto an exhausted
  minority,
- governance arrangements that collapse under scale, speed, or strategic
  abuse.

PromiseGrid has to think about all of these, not just classic congestion.

## Domain 1: pre-electronic commons

Consider pasture, fisheries, forests, and irrigation systems.

Common patterns:

- the resource is shared but not infinite,
- local knowledge matters,
- monitoring is costly but necessary,
- maintenance is as important as consumption,
- legitimacy matters because participants must keep cooperating after each
  dispute.

What worked in successful cases:

- clear boundaries around who participates,
- locally legible rules,
- metering or at least rough observability of use,
- graduated sanctions rather than one giant punishment,
- nested governance: village, canal, district, region,
- the ability to adapt rules when seasons, populations, or technologies
  change.

What failed:

- open access with no real membership boundary,
- elites capturing the shared resource while claiming to govern it,
- maintenance obligations being treated as somebody else's problem,
- outside power breaking local feedback loops.

Relevance to PromiseGrid:

- local operators need room to govern locally,
- participation probably cannot be pure open access with zero accountability,
- maintenance labor and abuse-handling are part of the commons, not external
  details.

## Domain 2: public infrastructure and utility-style commons

Consider roads, canals, ports, water systems, power grids, telephony, and
other public-utility style infrastructures.

These systems differ from village commons in one major way: heavy capital and
reliability requirements dominate the design.

Common patterns:

- large fixed costs,
- continuous maintenance obligations,
- strong incentives toward monopoly or quasi-monopoly,
- pressure for professional operators, regulators, and formal service
  guarantees,
- metering and billing becoming central governance tools.

What utilities teach:

- some shared systems need enough centralization to fund maintenance and
  safety,
- metering is not just accounting; it is part of governance,
- service guarantees change incentives because operators can no longer claim
  the commons is merely informal.

What goes wrong:

- underinvestment while extracting rents,
- political capture,
- universal service promises made without sustainable funding,
- monopoly lock-in that reduces exit and local autonomy.

Relevance to PromiseGrid:

- kernels, handler ecosystems, and shared protocol registries may behave more
  like utilities than like a village pasture,
- the system may need explicit maintenance and funding models rather than
  relying on volunteer surplus,
- avoiding tragedy may require preventing both overuse and underfunded core
  infrastructure.

## Domain 3: the open and early internet, plus protocol commons

Consider email, Usenet, DNS, BGP, the Web, open-source protocol stacks, and
shared standards work.

The early internet looked like an enormous commons of interoperation:

- open protocols,
- low entry barriers,
- local autonomy at the edges,
- shared dependence on rough consensus and volunteer or institutionally
  subsidized maintenance.

What worked:

- interoperability created compounding value,
- local experimentation flourished,
- open specifications lowered dependency on any one vendor,
- shared protocols supported many communities at once.

What broke:

- spam and abuse turned email and Usenet into governance stress tests,
- security externalities piled up because no one actor owned the full cost,
- naming, routing, and trust systems became soft spots,
- maintainers of critical protocols and libraries often became invisible,
  overdrawn common-pool labor.

Relevance to PromiseGrid:

- open protocols alone do not solve abuse,
- decentralized architecture can still produce commons failures around trust,
  moderation, naming, maintenance, and funding,
- protocol commons need governance institutions, not just open specs.

## Domain 4: centralized platforms, cloud, devops, and investment patterns

The last 20-30 years produced a different answer to internet governance:
centralize the difficult parts.

Social media platforms, app stores, cloud providers, and heavily managed
platform stacks solved some commons problems by enclosure.

What they improved:

- faster operational coordination,
- stronger centralized abuse response,
- easier monetization and subsidized growth,
- easier developer onboarding when one provider owns the paved road.

What they changed politically and economically:

- dependency shifted from open protocol participation to platform tenancy,
- moderation and infrastructure policy became private governance,
- venture and growth financing rewarded enclosure, data extraction, and
  lock-in,
- infrastructure-as-code and devops discipline professionalized operations
  but also concentrated operational power inside large organizations and
  managed providers.

Resulting information and social effects:

- public discourse became dependent on a few operators,
- users got convenience but lost bargaining power,
- operators gained the ability to set default reality for identity,
  discoverability, and acceptable speech,
- the commons problem did not disappear; it moved inside corporations and
  supply chains.

Relevance to PromiseGrid:

- avoiding tragedy of the commons cannot just mean "centralize everything and
  call it solved,"
- but ignoring why centralization won in practice would be naive,
- the system has to answer the real operational problems that platforms solved
  while resisting the enclosure patterns they normalized.

## Domain 5: PromiseGrid itself as a commons

PromiseGrid is not only a message format or a kernel design. It is an ecology
of shared resources and shared institutions.

Shared resources include:

- CPU, memory, storage, and bandwidth,
- handler author attention and maintenance labor,
- protocol namespace and legitimacy,
- routing reputations and trust histories,
- operator goodwill,
- user attention and willingness to keep participating,
- the credibility of capability promises and signed claims.

Shared institutions include:

- local sysadmin policy,
- protocol specs,
- handler registries or routing claims,
- social expectations about fair use,
- dispute resolution and exclusion practices,
- funding and maintenance arrangements.

If any of those are overdrawn or captured, the whole system can suffer a
tragedy even if the raw message transport still works.

## Cross-domain patterns

A few patterns recur across all four historical domains.

### 1) Boundaries matter

Every durable commons distinguishes some combination of:

- members and non-members,
- contributors and extractors,
- legitimate use and abuse,
- local authority and outside authority.

A boundary does not have to be a wall, but it has to be legible.

### 2) Maintenance is part of the commons

Commons fail not only from overconsumption, but from deferred maintenance.

For PromiseGrid, maintenance includes:

- handler upkeep,
- protocol updates,
- abuse response,
- security work,
- operator tooling,
- documentation and training,
- conflict resolution.

### 3) Metering and observability are governance tools

Durable commons need enough observability to answer:

- who is using what,
- who is paying what,
- who is exhausting which shared capacity,
- who is doing the maintenance work,
- who is free-riding.

### 4) Enclosure solves some problems while creating others

Centralization can reduce coordination cost and abuse overhead, but usually at
the price of local autonomy, exit, plural governance, and political balance.

### 5) Legitimacy matters as much as efficiency

A governance system that is efficient but regarded as illegitimate invites
evasion, defection, or revolt. A system that is legitimate but operationally
weak invites capture or collapse. Durable commons need both.

## What this survey does not yet settle

This survey does not yet answer:

- which resources the kernel should meter directly,
- whether ingress should be kernel-first or handler-first,
- exactly how signatures should be structured,
- exactly how routing and handler selection should be governed.

It does say those choices cannot be made as purely local engineering tricks.
They are all governance choices inside a commons.

## Questions to carry into later TEs

- Which shared resources in PromiseGrid are actually scarce, and which are
  only scarce because of current funding or governance arrangements?
- Which institutions need to remain local and plural, and which need more
  utility-like stability?
- How do we prevent enclosure while still solving the real operational
  problems that centralized platforms solved?
- Who pays for maintenance, moderation, and abuse handling if the system is
  to remain decentralized?
- What kind of boundaries, metering, and accountability are legitimate in a
  promise-oriented system?
