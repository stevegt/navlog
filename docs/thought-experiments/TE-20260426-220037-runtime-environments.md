# Runtime Environments Thought Experiment

TE ID: `TE-20260426-220037`  
TODO: `002.24`

## Decision under test

PromiseGrid does not target one runtime. It wants to run in native binaries,
containers, virtual machines, microVM or microVM-like sandboxed hosts,
browsers, serverless hosts, standalone WASM and WASI runtimes,
microcontrollers, mobile apps, game engines, and server-side applications.

That matters because `TE-20260426-204445-kernel-vs-handler-ingress.md` and
`TE-20260426-204444-pcid-signed-material.md` both risk sounding more universal
than they really are.

This TE asks:

- which runtime owns or mediates transport ingress,
- which runtime can safely hold signing keys,
- when "kernel-first" vs "handler-first" is a real architectural choice versus
  a host-imposed fact,
- when author-signed `[pCID, payload]` is realistic,
- when sender-signed `['grid' tag, pCID, payload]` is more natural,
- and when neither is enough without a separate user-controlled signer.

## Axes that matter across runtimes

Across all runtimes, the same five questions keep recurring.

1. Who owns transport primitives?
   - raw sockets, HTTP listeners, browser fetch/WebSocket, push channels,
     radio stacks, engine event loops, or serverless request handlers.
2. Who sees the message first?
   - the host platform, PromiseGrid kernel code, a handler, or some fused
     component.
3. Where can secrets live?
   - local keystore, secure enclave, browser WebCrypto key store, TPM, secure
     element, HSM, remote signer, or nowhere durable at all.
4. What persistence and background execution exist?
   - always-on process, ephemeral worker, suspended mobile app, browser tab, or
     firmware loop.
5. What actor can honestly sign what?
   - the author, the current transport sender, the local device, the operator,
     or a delegated remote signer.

The earlier TEs stay useful, but they have to be read as role models rather
than process models. "Kernel" and "handler" may be logical roles that collapse
or are host-constrained in some runtimes.

## Runtime-by-runtime analysis

### 1) Native binaries on Linux, Windows, and macOS

This is the most permissive environment.

Ingress:

- kernel-first, handler-first, and hybrid models are all genuinely possible,
- raw sockets, listeners, local IPC, and background daemons are available,
- `TE-20260426-204445-kernel-vs-handler-ingress.md` applies most directly here.

Signing:

- author-signed `[pCID, payload]` is realistic if the user's keys live in OS
  keystores, secure enclaves, TPM-backed stores, smartcards, or companion
  agents,
- sender-signed `['grid' tag, pCID, payload]` also makes sense for relays,
  gateways, or operator-controlled ingress services,
- layered signatures are easy to justify because the runtime can support both
  long-lived author keys and transport-layer service keys.

This runtime is the least constraining and therefore the worst default to
universalize from.

### 2) Containers

Containers share a host kernel while giving the process a more packaged and
isolated deployment model.

Ingress:

- containerized PromiseGrid code can often still own guest-visible listeners and
  sockets,
- kernel-first, handler-first, and hybrid models usually remain real choices
  inside the container,
- but the orchestrator, service mesh, sidecar, CNI layer, reverse proxy, or
  host firewall may already mediate some traffic before PromiseGrid sees it.

Signing:

- authorial signing is plausible if the container can safely reach OS keystores,
  HSM clients, TPM proxies, remote signers, or companion agents,
- sender-signed envelopes are natural for sidecars, relays, ingress proxies, or
  operator-owned service boundaries,
- containers are often the first place where host-managed secrets and app-level
  authorial keys diverge sharply.

Containers therefore look similar to native execution for internal structure,
but more obviously depend on outer platform layers for networking, secret
distribution, and policy.

### 3) Virtual machines

Virtual machines sit closer to native execution than containers do because the
guest usually has its own kernel and network stack.

Ingress:

- guest-visible kernel-first, handler-first, and hybrid models are all again
  real choices,
- but the hypervisor, virtual NICs, host firewall, or cloud control plane may
  still mediate the real machine-edge boundary above the guest,
- the useful distinction is often between machine-edge ingress and
  guest-visible ingress.

Signing:

- authorial signing is plausible when the guest can use virtual TPMs, secure
  enclaves, host-mediated HSM access, or remote signers,
- sender-signed envelopes are natural for VM-hosted gateways or relays,
- layered models remain strong because VMs can host durable services while still
  sitting under an operator or cloud host boundary.

VMs therefore preserve many native-style choices while making the outer host
layer more explicit.

### 4) MicroVM or microVM-like sandboxed hosts such as Firecracker or gVisor

These environments are narrower and more host-shaped than full VMs, but less
ambient than ordinary processes.

Ingress:

- guest-visible ingress may still exist, but the host strongly shapes it through
  jailer policies, virtual devices, sandboxed syscalls, proxy layers, or
  platform-owned request delivery,
- for Firecracker-like environments, the microVM may still feel VM-like inside,
  while for gVisor-like environments the app may feel process-like but with a
  much stronger host mediation layer,
- PromiseGrid therefore has to ask both who sees ingress first inside the guest
  and who already mediated it outside.

Signing:

- authorial signing is possible only if the host exposes some trustworthy key
  path such as vTPM-like services, HSM proxies, remote signers, or delegated
  user-signing flows,
- sender-signed envelopes are often attractive because these environments are
  commonly used as policy edges, gateways, or hardened service boundaries,
- the runtime strongly encourages a distinction between guest-local promises and
  higher-layer host or operator promises.

These hosts are especially useful for stressing the difference between
PromiseGrid-visible boundaries and true outer-host boundaries.

### 5) WASM in a browser tab

The browser owns the real transport substrate.

Ingress:

- raw sockets are not available in the normal sense,
- the tab uses browser-mediated APIs such as `fetch`, WebSocket, WebRTC, or
  service-worker-mediated flows,
- host-first ingress is therefore a fact of life even if the app builds an
  internal PromiseGrid kernel abstraction.

Implication for `002.19`:

- "kernel-first" can only mean first inside the app's own logic, not first on
  the machine or network,
- handler-first is also constrained because every handler lives inside the same
  browser security and event-loop model,
- the browser itself is an unavoidable super-host above any PromiseGrid kernel.

Signing:

- authorial signing is still plausible through WebCrypto, passkeys, WebAuthn,
  or browser-managed non-exportable keys,
- sender-signed whole envelopes also make sense because the browser tab is the
  actual transport sender for outbound messages,
- if the user must authorize signing interactively, browser UX may make
  author-primary signing easier than in headless server environments.

The browser runtime therefore favors a layered model: browser/tab as sender,
user key material as author, and host APIs mediating both.

### 6) WASM in a serverless environment

A serverless host owns ingress, scheduling, and often most of the surrounding
network stack.

Ingress:

- platform-first ingress is the default,
- the worker/function is invoked after the host already accepted the request,
- "kernel-first" inside the function is therefore only a post-host boundary.

Implication for `002.19`:

- the true first receiver is the serverless platform,
- PromiseGrid code can still have internal kernel and handler roles, but not in
  a way that ignores the platform's admission, throttling, and lifetime model,
- sender-signed ingress claims may be especially attractive because the worker
  is often acting as a relay, gateway, or policy edge.

Signing:

- durable user private keys generally should not live in ephemeral serverless
  instances,
- operator or service keys are easier to justify than user-author keys,
- if the function must help produce a user-authorized signature, it probably
  needs a remote signer, delegated authorization flow, WebAuthn/passkey round
  trip, threshold-signing service, or user-held device outside the worker.

This runtime strongly pressures the design toward sender-local or service-local
signatures unless an explicit user-signing architecture exists.

### 7) WASM in a standalone runtime such as Wasmtime or Wasmer

This looks more like native execution with explicit host capability boundaries.

Ingress:

- network, filesystem, clock, and IPC access depend on what the host embeds or
  permits,
- if the host exposes sockets or listeners, kernel-first or handler-first are
  again real choices,
- if the host wraps all ingress in callbacks, then the runtime starts to look
  more like serverless.

Signing:

- authorial signing is plausible if the host provides access to secure storage,
  HSM bindings, remote signers, or OS keystores,
- sender-signed envelopes fit well when the embedding host itself is the active
  transport participant,
- the real question is not WASM versus native but what capabilities the host is
  willing to delegate.

This runtime reinforces a broader lesson: runtime labels matter less than host
capability shape.

### 8) WASI in a standalone runtime such as Wasmtime or Wasmer

WASI adds a more explicit capability-oriented contract, but the same basic point
holds.

Ingress:

- there is no universal answer; the embedding host decides what network and I/O
  capabilities exist,
- PromiseGrid roles may map cleanly onto capability-granted components,
- kernel-first may be attractive when one WASI component owns shared admission
  and metering capabilities.

Signing:

- authorial signing depends on whether the host can expose secure key services
  without simply dumping private material into the guest,
- sender-signed envelopes may fit capability-oriented service edges well,
- layered models remain natural because WASI hosts can split duties across
  components or services.

WASI strengthens the idea that ingress and signing are capability-distribution
questions as much as they are process-boundary questions.

### 9) WASI in a serverless-style environment

Some hosts may expose WASI-like components inside a serverless or edge-managed
lifecycle.

Ingress:

- platform-first is still the real first boundary,
- host admission, lifetime, and quotas dominate,
- internal PromiseGrid kernel/handler structure becomes a second-order design.

Signing:

- same pressure as serverless WASM: service keys are easy, user-author keys are
  not,
- user-authorized signing needs an out-of-band signer or delegation protocol,
- sender-signed ingress claims may be the most natural first signature in the
  path.

This is operationally closer to serverless WASM than to standalone WASI.

### 10) Microcontroller firmware such as Arduino or ESP32

Here the runtime may collapse almost everything into one firmware image.

Ingress:

- transport handling may be direct over Wi-Fi, BLE, serial, CAN, LoRa, or other
  device-specific stacks,
- there may be no meaningful local distinction between kernel and handler as
  separate processes,
- the useful question becomes whether the firmware internally separates shared
  admission/resource logic from protocol-specific logic.

Signing:

- device-bound signing is realistic if there is a secure element, TPM-like
  component, or protected flash strategy,
- human-author signing is usually not realistic unless the device is paired with
  a user-held signer such as a phone or browser,
- sender-signed envelopes may be natural because the device often is the actual
  sender and author for telemetry or automation promises.

This runtime favors fused or layered roles, not desktop-style process splits.

### 11) Mobile apps on Android and iOS

The OS strongly mediates lifecycle, networking, background work, and secure key
storage.

Ingress:

- the app does not own the platform's push, wakeup, or network scheduling
  model,
- some traffic may arrive only when the OS resumes or notifies the app,
- an internal PromiseGrid kernel is possible, but the real first receiver is
  still often the OS or platform service.

Signing:

- authorial signing is realistic thanks to Secure Enclave, Keychain, Keystore,
  StrongBox, biometric-gated keys, and passkey support,
- sender-signed envelopes are also plausible because the mobile app is the live
  transport sender for outbound activity,
- layered author-plus-sender signatures look especially credible here.

Mobile therefore looks like browser plus secure hardware: host-mediated ingress,
but strong possibilities for user/device-bound signing.

### 12) Embedded in a game engine such as Unity or Unreal

The engine owns the world loop, event model, and often networking abstractions.

Ingress:

- PromiseGrid code usually runs as a subsystem inside a larger host,
- engine-first ingress is therefore common in practice,
- the architectural question becomes whether PromiseGrid gets a logical kernel
  boundary inside the engine or is simply one handler among many subsystems.

Signing:

- keys may live in platform services, engine plugins, or external companions,
- authorial signing for actual human users is plausible only if the engine can
  delegate to OS keystores or user-controlled signers,
- sender-signed envelopes fit well for game-session relays, servers, or engine-
  hosted agents.

This runtime again argues that the host often sits above any PromiseGrid kernel.

### 13) Server-side applications that need user-authorized signatures without user private keys

This case deserves special treatment because it tests the boundary between
service assistance and false authorship.

Ingress:

- the server is often both a transport endpoint and an application host,
- kernel-first versus handler-first may still matter internally,
- but the signing problem dominates because the server must not simply claim to
  be the user.

Signing:

- if the server does not have the user's private key, it cannot honestly produce
  a plain authorial signature as if it were the user,
- the real options are delegated or mediated designs:
  - remote signer controlled by the user,
  - passkey/WebAuthn assertion from the user's device,
  - threshold or MPC signer where the server alone is insufficient,
  - explicit user-issued capability token allowing the server to make a narrower
    promise on the user's behalf,
  - server-signed envelope plus separately user-signed payload or policy grant.

This runtime is the clearest case where author-signed and sender-signed roles
must be separated. Otherwise the system confuses service delegation with user
meaning.

## Cross-runtime patterns

### 1) The true first receiver is often the host, not PromiseGrid code

Browsers, serverless platforms, mobile operating systems, and game engines all
mediate ingress before any PromiseGrid kernel logic runs. Containers, VMs, and
microVM-like hosts often preserve more guest-visible choice, but they still add
an outer host boundary that may mediate the real machine edge.

Implication:

- `TE-20260426-204445-kernel-vs-handler-ingress.md` should be read as a choice
  inside the runtime's usable boundary, not as a claim that PromiseGrid always
  owns the machine's first listener.

### 2) Key custody decides which signing model is realistic

If the runtime can safely hold or access user-bound keys, authorial signing is
credible. If it cannot, sender-signed envelopes or delegated signing become more
natural.

Implication:

- `TE-20260426-204444-pcid-signed-material.md` should not assume one signer role
  across all runtimes.

### 3) Sender-signed envelopes are strongest at ingress or gateway boundaries

When a runtime is a relay, edge worker, gateway, or host-mediated transport
participant, sender-signed `['grid' tag, pCID, payload]` envelopes become more
plausible as the first verifiable promise.

But that does not eliminate the need for authorial signatures when end-to-end
meaning matters.

### 4) Author-primary signing is strongest where user-bound key hardware or APIs exist

Native desktops, mobile devices, and browsers with passkeys/WebCrypto all make
user-authorized signing more realistic than ephemeral serverless hosts do.

### 5) "Kernel" and "handler" are logical roles, not guaranteed processes

On microcontrollers the roles may collapse.
In browser tabs and engines they may be nested under a larger host.
In serverless hosts they may both sit downstream of a platform-owned ingress.
In containers, VMs, and microVM-like hosts they may be cleanly separated inside
the guest while still sitting beneath an outer host policy layer.

That does not make the TEs wrong. It changes how literally their boundaries can
be realized in each runtime.

## What this does to TE-20260426-204445-kernel-vs-handler-ingress.md

The ingress TE still stands, but it needs one runtime-aware reading:

- native and standalone hosts allow the richest direct choice,
- browser, mobile, game-engine, and serverless hosts impose a higher-level host
  boundary first,
- so the real design question is often "where is the first PromiseGrid-visible
  admission boundary inside this host?" rather than "who owns the socket at the
  machine edge?"

## What this does to TE-20260426-204444-pcid-signed-material.md

The signed-material TE also still stands, but it needs one runtime-aware
reading:

- author-signed `[pCID, payload]` is the strongest end-to-end meaning claim,
- sender-signed `['grid' tag, pCID, payload]` is often the strongest ingress-
  local or gateway-local claim,
- many real deployments will need both,
- and some runtimes require delegated or remote-signing architectures if user
  meaning must be preserved without exposing user keys.

## What survives

Across runtimes, three conclusions survive.

1. There is no single ingress architecture that fits every host.
2. There is no single signer role that fits every host.
3. PromiseGrid needs role-level abstractions that survive host differences:
   author, sender, ingress boundary, handler, verifier, delegated signer,
   discovery layer, and operator.

## Follow-on questions

- Which runtime families are first-class for the MVP, and which are later?
- Does the architecture need an explicit delegated-signing protocol for hosts
  that cannot safely hold user private keys?
- Should the ingress TE be revised to distinguish machine-edge ingress from
  PromiseGrid-visible ingress inside a host?
- Should the signed-material TE explicitly separate authorial signatures,
  sender-local envelope signatures, and delegated-signing flows as three
  separate roles rather than two?
