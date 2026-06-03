# INFRASTRUCTURE_RUNTIME.md

## 1. Document Authority

This contract inherits all authority definitions from `SYSTEM_AUTHORITY_CONTRACT.md`.

This document defines the deterministic runtime behavior of the ISP Billing & AAA Infrastructure.

This document does not define implementation, configuration, schema, commands, deployment, or monitoring tooling.

---

# 2. Runtime Doctrine

## 2.1 Runtime Definition

Runtime is the live operational condition where infrastructure components execute their assigned responsibilities while preserving authority boundaries.

Runtime includes:

* component startup
* component shutdown
* dependency readiness
* health evaluation
* synchronization execution
* degraded operation
* failure detection
* recovery sequencing

Runtime does not own business authority, billing authority, Radius policy authority, accounting authority, payment authority, or network enforcement authority.

## 2.2 Runtime Authority

Runtime authority governs only operational execution.

Runtime may determine:

* whether a component is available
* whether a dependency is ready
* whether the system is degraded
* whether recovery is required
* whether synchronization must pause or resume

Runtime may not determine:

* customer status
* billing status
* payment status
* Radius policy truth
* MikroTik enforcement truth
* accounting truth
* provisioning truth

## 2.3 Runtime Ownership

The Backend Orchestrator owns runtime coordination.

PostgreSQL owns durable authoritative state.

FreeRADIUS owns Radius protocol execution.

MikroTik NAS owns network enforcement execution.

Midtrans owns external payment notification delivery.

Telegram owns external notification delivery.

Docker Runtime owns process containment only.

Ubuntu Server owns host execution only.

---

# 3. Runtime Component Model

## 3.1 Backend Orchestrator

Owns:

* runtime coordination
* startup validation
* dependency readiness checks
* synchronization orchestration
* failure classification
* recovery sequencing
* runtime state machine

Forbidden:

* becoming source of truth without PostgreSQL
* silently overriding authority
* creating hidden runtime state
* assuming dependency success without validation

## 3.2 PostgreSQL

Owns:

* durable authoritative records
* persisted system evidence
* billing state
* Radius policy input state
* accounting evidence
* provisioning records
* payment records

Forbidden:

* executing runtime decisions
* enforcing network state
* sending external notifications

## 3.3 FreeRADIUS

Owns:

* Radius authentication execution
* Radius authorization execution
* Radius accounting packet reception
* protocol-level Radius response

Forbidden:

* owning customer truth
* owning billing truth
* owning payment truth
* manually diverging from generated policy

## 3.4 MikroTik NAS

Owns:

* PPPoE session enforcement
* bandwidth enforcement
* disconnect execution
* NAS-level session behavior

Forbidden:

* owning billing state
* owning customer lifecycle
* owning payment state
* becoming independent authority

## 3.5 Midtrans

Owns:

* external payment event delivery
* payment gateway status signals

Forbidden:

* directly changing subscriber service state
* directly changing Radius policy
* directly changing MikroTik state

## 3.6 Telegram

Owns:

* operational notification delivery
* operator command input where explicitly authorized

Forbidden:

* becoming operational authority
* becoming billing authority
* bypassing Backend Orchestrator validation

---

# 4. Startup Authority Model

## 4.1 Startup Doctrine

Startup must be deterministic, dependency-aware, and validation-gated.

A component is not considered usable merely because its process is running.

A component becomes usable only after readiness validation succeeds.

## 4.2 Startup Dependency Graph

Deterministic startup order:

1. Ubuntu Server runtime available
2. Docker Runtime available
3. PostgreSQL process available
4. PostgreSQL readiness validated
5. Backend Orchestrator process available
6. Backend dependency validation executed
7. FreeRADIUS process available
8. FreeRADIUS readiness validated
9. MikroTik NAS reachability validated
10. Midtrans connectivity mode classified
11. Telegram connectivity mode classified
12. Runtime state transitions to READY or DEGRADED

## 4.3 Forbidden Startup Behavior

Forbidden:

* starting dependent services without readiness validation
* treating container start as service readiness
* circular startup dependency
* hidden manual bootstrapping
* silent fallback to local state
* FreeRADIUS becoming authoritative before policy validation
* MikroTik being treated as synchronized without verification

---

# 5. Shutdown Authority Model

## 5.1 Shutdown Doctrine

Shutdown must preserve durable authority and prevent partial runtime corruption.

Shutdown must be ordered, explicit, and state-preserving.

## 5.2 Shutdown Order

Deterministic shutdown order:

1. Stop accepting new runtime orchestration tasks
2. Pause non-critical synchronization
3. Persist pending durable events where possible
4. Stop notification dispatch
5. Stop payment processing workers
6. Stop provisioning workers
7. Stop Backend Orchestrator
8. Stop FreeRADIUS only after accounting risk is classified
9. Stop PostgreSQL last among application dependencies
10. Stop Docker Runtime
11. Stop host runtime if required

## 5.3 Forbidden Shutdown Behavior

Forbidden:

* killing PostgreSQL before dependent services are quiesced
* dropping pending accounting evidence silently
* terminating recovery halfway without marking state
* stopping Backend Orchestrator while workers mutate state
* treating shutdown as failure recovery

---

# 6. Runtime Health Model

## 6.1 Health States

Each component may be classified as:

* `UNKNOWN`
* `STARTING`
* `HEALTHY`
* `DEGRADED`
* `UNAVAILABLE`
* `RECOVERING`
* `FAILED`

## 6.2 Health Ownership

Backend Orchestrator owns health classification.

Each component may emit health evidence, but cannot independently define global runtime state.

## 6.3 Health Consumers

Health state may be consumed by:

* runtime state machine
* synchronization controller
* recovery controller
* notification dispatcher
* operator dashboard
* audit log

Health state must not be consumed as business truth.

---

# 7. Synchronization Runtime Model

## 7.1 Synchronization Doctrine

Synchronization exists to align execution systems with authoritative state.

Synchronization never creates authority.

## 7.2 Synchronization Direction

Allowed synchronization direction:

* PostgreSQL authoritative state → Backend Orchestrator
* Backend Orchestrator → FreeRADIUS policy execution
* Backend Orchestrator → MikroTik enforcement instruction
* Midtrans event → Backend validation → PostgreSQL
* PostgreSQL evidence → notification dispatcher → Telegram

Forbidden synchronization direction:

* MikroTik → billing truth
* FreeRADIUS → customer truth
* Telegram → direct infrastructure mutation
* Midtrans → direct service activation
* runtime cache → authoritative state

## 7.3 Synchronization Lifecycle

Synchronization states:

* `PENDING`
* `VALIDATING`
* `APPLYING`
* `CONFIRMED`
* `FAILED`
* `RETRY_REQUIRED`
* `RECONCILIATION_REQUIRED`

---

# 8. Degraded Operation Model

## 8.1 PostgreSQL Unavailable

System state: `DEGRADED` or `FAILED`.

Allowed:

* preserve existing already-loaded execution where safe
* reject new authoritative mutations
* classify outage
* notify operators if notification path is available

Forbidden:

* creating new billing truth
* creating new customer truth
* creating new payment truth
* generating new policy from stale memory as authority

## 8.2 FreeRADIUS Unavailable

Allowed:

* preserve PostgreSQL authority
* classify Radius execution failure
* queue recovery validation
* notify operators

Forbidden:

* changing customer status because Radius is down
* changing billing state because Radius is down
* treating NAS state as replacement Radius truth

## 8.3 MikroTik Unavailable

Allowed:

* preserve authority state
* mark enforcement synchronization as pending
* retry validation
* classify NAS reachability failure

Forbidden:

* marking enforcement confirmed without NAS validation
* changing billing state due to NAS outage
* assuming disconnect success

## 8.4 Midtrans Unavailable

Allowed:

* preserve existing payment records
* pause external payment verification
* continue internal operations not dependent on new payment events

Forbidden:

* marking payment paid without validated event
* activating service from unverified payment claims

## 8.5 Telegram Unavailable

Allowed:

* continue core infrastructure operation
* persist notification failure evidence
* retry notification later

Forbidden:

* treating notification failure as business failure
* blocking Radius, billing, or accounting authority solely due to Telegram failure

---

# 9. Failure Detection Model

## 9.1 Failure Classes

Failure classes:

* `PROCESS_FAILURE`
* `DEPENDENCY_FAILURE`
* `READINESS_FAILURE`
* `HEALTH_DEGRADATION`
* `SYNCHRONIZATION_FAILURE`
* `AUTHORITY_VIOLATION`
* `PARTIAL_FAILURE`
* `CASCADING_FAILURE`
* `RECOVERY_FAILURE`

## 9.2 Failure Ownership

Backend Orchestrator owns failure classification.

Component-local errors are evidence only.

## 9.3 Escalation Rules

Escalate to `DEGRADED` when at least one dependency is impaired but authority remains preserved.

Escalate to `FAILED` when authority preservation cannot be guaranteed.

Escalate to `RECOVERING` only when deterministic recovery sequence has started.

---

# 10. Recovery Model

## 10.1 Recovery Doctrine

Recovery restores operational alignment.

Recovery must not create new authority.

Recovery must validate state before resuming normal operation.

## 10.2 Recovery Sequence

Deterministic recovery order:

1. Detect failure
2. Classify failure
3. Freeze unsafe synchronization paths
4. Preserve durable authority
5. Validate dependency availability
6. Reconcile pending synchronization
7. Validate execution state
8. Resume controlled operation
9. Emit recovery event
10. Transition runtime state

## 10.3 Recovery Validation

Recovery is valid only when:

* dependency health is confirmed
* authority state remains intact
* pending synchronization is reconciled or explicitly marked
* no hidden runtime state is promoted
* audit evidence exists

---

# 11. Runtime State Machine

## 11.1 States

### STARTING

System is booting and validating dependencies.

May transition to:

* `READY`
* `DEGRADED`
* `FAILED`

### READY

All mandatory dependencies are healthy and authority boundaries are preserved.

May transition to:

* `DEGRADED`
* `FAILED`
* `SHUTTING_DOWN`

### DEGRADED

System can operate partially while authority remains preserved.

May transition to:

* `RECOVERING`
* `FAILED`
* `SHUTTING_DOWN`

### RECOVERING

System is executing deterministic recovery.

May transition to:

* `READY`
* `DEGRADED`
* `FAILED`
* `SHUTTING_DOWN`

### FAILED

System cannot guarantee safe runtime operation.

May transition to:

* `RECOVERING`
* `SHUTTING_DOWN`

### SHUTTING_DOWN

System is terminating in controlled order.

May transition to:

* terminal stopped condition

## 11.2 Transition Authority

Only Backend Orchestrator may transition global runtime state.

No dependency may self-promote global runtime state.

---

# 12. Runtime Event Model

## 12.1 Startup Events

Producer:

* Backend Orchestrator
* component readiness checks

Authority owner:

* Backend Orchestrator

Consumers:

* runtime state machine
* audit log
* operator notification

## 12.2 Shutdown Events

Producer:

* Backend Orchestrator
* operator command
* host runtime signal

Authority owner:

* Backend Orchestrator

Consumers:

* workers
* synchronization controller
* audit log

## 12.3 Health Events

Producer:

* components
* readiness probes
* dependency checks

Authority owner:

* Backend Orchestrator

Consumers:

* runtime state machine
* recovery controller
* notification dispatcher

## 12.4 Synchronization Events

Producer:

* Backend Orchestrator
* provisioning lifecycle
* Radius policy engine
* accounting engine
* payment handler

Authority owner:

* owning domain contract

Consumers:

* synchronization controller
* audit log
* recovery controller

## 12.5 Failure Events

Producer:

* components
* dependency checks
* workers
* reconciliation systems

Authority owner:

* Backend Orchestrator

Consumers:

* runtime state machine
* recovery controller
* operator notification
* audit log

## 12.6 Recovery Events

Producer:

* recovery controller

Authority owner:

* Backend Orchestrator

Consumers:

* runtime state machine
* synchronization controller
* audit log
* operator notification

---

# 13. Operational Invariants

1. Runtime cannot override authority.
2. Dependency failure cannot transfer ownership.
3. Startup ordering must be deterministic.
4. Shutdown ordering must preserve durable state.
5. Recovery cannot create new authority.
6. Health state is not business state.
7. Synchronization failure does not change authority.
8. Process availability is not readiness.
9. Container availability is not application health.
10. Runtime cache is not source of truth.
11. FreeRADIUS execution is not customer authority.
12. MikroTik enforcement is not billing authority.
13. Telegram notification is not operational confirmation.
14. Midtrans signal is not payment truth until validated.
15. PostgreSQL unavailability freezes authoritative mutation.
16. Recovery must validate before resuming.
17. Failed synchronization must be explicit.
18. Hidden state must never be promoted.
19. Partial failure must be classified.
20. Split-brain runtime is forbidden.
21. Manual synchronization cannot bypass authority.
22. Runtime state transitions must be auditable.
23. Degraded operation must preserve authority.
24. Failure escalation must be deterministic.
25. Shutdown must not erase pending evidence.
26. Recovery must not skip reconciliation.
27. Startup must not assume dependency correctness.
28. Runtime cannot invent business facts.
29. External systems cannot mutate internal authority directly.
30. Every runtime decision must have an owning authority.

---

# 14. Forbidden Runtime Patterns

## 14.1 Startup Race Conditions

Forbidden because dependency readiness must be validated before dependent operation begins.

## 14.2 Circular Dependencies

Forbidden because startup, recovery, and shutdown ordering become non-deterministic.

## 14.3 Runtime Authority Migration

Forbidden because authority ownership is fixed by contract and cannot move during failure.

## 14.4 Hidden Runtime State

Forbidden because unpersisted state cannot be audited, replayed, or reconciled.

## 14.5 Split-Brain Operation

Forbidden because multiple components may claim conflicting authority.

## 14.6 Uncontrolled Recovery

Forbidden because recovery may corrupt state if sequencing and validation are skipped.

## 14.7 Manual Runtime Synchronization

Forbidden because manual mutation bypasses deterministic ownership and audit evidence.

## 14.8 Dependency Inversion

Forbidden because execution systems must not control authoritative systems.

## 14.9 Silent Fallback

Forbidden because fallback behavior may create unauthorized shadow authority.

## 14.10 Stale Cache Promotion

Forbidden because cached runtime state is not durable authority.

## 14.11 Notification-Driven Authority

Forbidden because Telegram delivery cannot define operational truth.

## 14.12 Gateway-Driven Service Mutation

Forbidden because Midtrans cannot directly activate, suspend, or restore service.

## 14.13 NAS-Driven Billing Mutation

Forbidden because MikroTik session behavior cannot define billing truth.

## 14.14 Radius-Driven Customer Mutation

Forbidden because Radius protocol execution cannot define customer lifecycle.

## 14.15 Health-Driven Business Mutation

Forbidden because health state describes infrastructure condition, not customer or billing state.

---

# 15. Final Runtime Contract

The ISP Billing & AAA Infrastructure runtime must operate as a deterministic authority-preserving execution system.

Runtime coordinates live operation but never owns business truth.

Runtime detects, classifies, degrades, recovers, and shuts down infrastructure without violating authority contracts.

Any implementation that allows runtime behavior to override authority, create hidden state, skip synchronization validation, or recover without reconciliation violates this contract.

This document is the mandatory runtime authority contract for all future implementation phases.

