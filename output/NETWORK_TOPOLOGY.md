# NETWORK_TOPOLOGY.md

# Network Topology Contract

## ISP Billing & AAA Infrastructure

---

## 1. Document Authority

This document defines the deterministic network topology contract for the ISP Billing & AAA Infrastructure.

This document governs:

* Infrastructure boundaries
* Trust zones
* Operational planes
* Network authority ownership
* Service exposure rules
* Multi-NAS readiness
* Multi-POP readiness
* Failure topology doctrine
* Security topology doctrine

This document does not define implementation configuration.

---

## 2. Scope Boundary

This document is limited to topology architecture.

This document does not include:

* MikroTik commands
* Firewall rules
* VLAN configuration
* IP address planning
* Docker networking configuration
* Deployment instructions
* Runtime tuning
* Product implementation

Topology correctness has priority over deployment simplicity.

---

## 3. Dependency Chain

This topology contract depends on:

1. `SYSTEM_AUTHORITY_CONTRACT.md`
2. `AAA_LIFECYCLE_CONTRACT.md`
3. `ACCOUNTING_ENGINE.md`

The network topology must not contradict authority, lifecycle, or accounting doctrine defined in those documents.

---

# 4. Core Topology Doctrine

The network is not treated as device connectivity.

The network is treated as an infrastructure boundary system.

Topology exists to enforce:

* Authority separation
* Trust isolation
* Runtime containment
* Operational auditability
* Failure containment
* Multi-NAS scalability
* Multi-POP consistency

No service may communicate with another service unless the communication is explicitly authorized by this contract or by a downstream contract.

---

# 5. Network Authority Model

## 5.1 Authority Principle

Network topology is not the source of truth for business state.

Topology only provides controlled paths between authority domains.

Business state belongs to the Billing Core.

Policy translation belongs to the Radius Policy Engine.

Authentication and accounting execution belong to FreeRADIUS.

Network enforcement belongs to MikroTik NAS.

Persistence belongs to PostgreSQL.

Runtime orchestration belongs to the Backend Orchestrator.

Public ingress belongs to Nginx.

---

## 5.2 Ownership Matrix

| Domain                        | Owner                     | Authority                                         |
| ----------------------------- | ------------------------- | ------------------------------------------------- |
| Subscriber access enforcement | MikroTik NAS              | Enforces access decisions                         |
| AAA execution                 | FreeRADIUS                | Executes deterministic AAA policy                 |
| Billing state                 | Billing Core              | Owns subscriber business state                    |
| Policy translation            | Radius Policy Engine      | Converts business state into network policy       |
| Accounting evidence           | Accounting Engine         | Owns accounting interpretation and reconciliation |
| Persistence                   | PostgreSQL                | Stores authoritative records                      |
| Runtime jobs                  | Backend Orchestrator      | Executes deterministic operational workflows      |
| Public ingress                | Nginx                     | Owns controlled public entry                      |
| Operator workflow             | Admin backend             | Owns management workflow                          |
| Payment ingress               | Midtrans webhook endpoint | Receives payment events only                      |
| Telegram automation           | Telegram Bot subsystem    | Executes operator-approved automation only        |

---

# 6. Operational Plane Model

The infrastructure is divided into operational planes.

A plane is a topology boundary with a specific authority purpose.

## 6.1 Subscriber Plane

The subscriber plane contains customer traffic and PPPoE access sessions.

Primary actor:

* MikroTik NAS

Responsibilities:

* Terminate PPPoE access
* Enforce subscriber session policy
* Apply service state received from AAA
* Emit accounting events

Forbidden:

* Direct subscriber access to backend services
* Direct subscriber access to database
* Direct subscriber access to FreeRADIUS
* Subscriber-originated management access

---

## 6.2 AAA Plane

The AAA plane contains FreeRADIUS and AAA-related policy execution.

Responsibilities:

* Receive authentication requests from trusted NAS devices
* Receive accounting events from trusted NAS devices
* Execute deterministic AAA decisions
* Forward accounting evidence into persistence and accounting workflows

Forbidden:

* Public internet exposure
* Subscriber-originated access
* Manual mutation of subscriber business state
* Acting as independent source of truth

---

## 6.3 Database Plane

The database plane contains PostgreSQL.

Responsibilities:

* Store authoritative system records
* Store billing state
* Store subscriber state
* Store accounting evidence
* Store reconciliation outputs
* Store operational audit records

Forbidden:

* Public exposure
* Direct NAS access unless explicitly required by AAA execution doctrine
* Direct customer portal access
* Direct Telegram bot mutation without backend mediation
* Manual operational mutation outside approved admin workflows

---

## 6.4 Application Plane

The application plane contains:

* Backend Orchestrator
* Billing Core
* Radius Policy Engine
* Accounting Engine
* Admin API
* Customer Portal backend
* Telegram Bot backend
* Midtrans integration handler

Responsibilities:

* Execute deterministic workflows
* Mediate access to persistence
* Enforce authority boundaries
* Coordinate billing, policy, accounting, and automation

Forbidden:

* Bypassing PostgreSQL authority
* Direct uncontrolled access to NAS management
* Hidden side-channel state
* Background mutation without audit trail

---

## 6.5 Public Ingress Plane

The public ingress plane contains Nginx and controlled externally reachable endpoints.

Allowed exposure:

* Customer portal
* Payment webhook endpoint
* Explicit public API endpoints if approved by system authority

Forbidden exposure:

* PostgreSQL
* Redis
* FreeRADIUS
* MikroTik management
* Internal admin services without access control
* Internal service discovery
* Docker runtime interfaces

---

## 6.6 Management Plane

The management plane contains operator access paths.

Responsibilities:

* Admin access
* Operational supervision
* Controlled maintenance
* Emergency intervention
* Audit review

Forbidden:

* Direct subscriber-plane access from public internet
* Shared credentials across operators
* Direct database changes outside approved workflows
* Direct NAS changes that bypass system authority
* Undocumented management paths

---

# 7. Trust Zone Model

## 7.1 Trusted Zone

The trusted zone contains core internal services.

Members:

* PostgreSQL
* Backend Orchestrator
* Billing Core
* Radius Policy Engine
* Accounting Engine
* Redis
* Internal FreeRADIUS service path

Rules:

* Communication must be explicit
* State mutation must be audited
* No public ingress allowed
* No subscriber-originated access allowed

---

## 7.2 Semi-Trusted Zone

The semi-trusted zone contains infrastructure components that interact with external or edge systems.

Members:

* MikroTik NAS
* Nginx public ingress
* Midtrans webhook receiver
* Telegram Bot integration layer

Rules:

* Must not own business state
* Must not directly mutate authority state without backend mediation
* Must be identity-bound
* Must be auditable
* Must be isolated from database internals unless explicitly authorized

---

## 7.3 Untrusted Zone

The untrusted zone contains:

* Subscribers
* Public internet
* External webhook senders
* Telegram platform
* Customer devices
* Unknown external clients

Rules:

* No direct access to trusted services
* No management access
* No database access
* No Radius access
* No internal topology visibility

---

# 8. Infrastructure Boundary Model

The topology must enforce the following boundaries:

## 8.1 Subscriber Boundary

Subscriber traffic must terminate at the NAS boundary.

Subscriber traffic must not enter internal service networks.

---

## 8.2 AAA Boundary

AAA traffic is only permitted between authorized NAS devices and FreeRADIUS.

FreeRADIUS must not be reachable from the public internet.

---

## 8.3 Persistence Boundary

PostgreSQL is an internal-only persistence authority.

All database access must be mediated by approved internal services.

---

## 8.4 Public Boundary

Nginx is the only public ingress boundary.

Public traffic must terminate at Nginx before reaching application services.

---

## 8.5 Management Boundary

Operator management paths must be separate from subscriber access paths and public ingress paths.

Management access must be controlled, logged, and limited.

---

# 9. Communication Contracts

## 9.1 MikroTik NAS to FreeRADIUS

Allowed:

* Authentication request
* Authorization request
* Accounting start
* Accounting interim update
* Accounting stop

Forbidden:

* Direct billing mutation
* Direct database ownership
* Public exposure
* Cross-NAS identity ambiguity

---

## 9.2 FreeRADIUS to PostgreSQL

Allowed:

* AAA lookup
* Accounting persistence
* Policy lookup if authorized by Radius Policy Engine contract

Forbidden:

* Business lifecycle ownership
* Manual subscriber state mutation
* Hidden Radius-local state as authority

---

## 9.3 Backend Orchestrator to PostgreSQL

Allowed:

* Billing lifecycle mutation
* Subscriber lifecycle mutation
* Policy state mutation
* Accounting reconciliation storage
* Audit log writing

Forbidden:

* Unlogged mutation
* Non-deterministic state repair
* Manual topology-dependent assumptions

---

## 9.4 Backend Orchestrator to Redis

Allowed:

* Queueing
* Runtime coordination
* Job state cache
* Temporary operational state

Forbidden:

* Permanent authority state
* Billing source of truth
* Subscriber source of truth
* Accounting source of truth

---

## 9.5 Nginx to Application Services

Allowed:

* Customer portal routing
* Webhook routing
* Explicit public API routing

Forbidden:

* Routing to database
* Routing to Redis
* Routing to FreeRADIUS
* Routing to NAS management
* Routing to internal admin interfaces without access control

---

## 9.6 Telegram Integration to Backend

Allowed:

* Operator command submission
* Notification delivery
* Approved automation trigger

Forbidden:

* Direct database mutation
* Direct NAS mutation
* Direct FreeRADIUS mutation
* Bypassing audit workflow

---

# 10. MikroTik Topology Contract

MikroTik is an access concentrator and enforcement executor.

MikroTik owns:

* PPPoE access termination
* Session enforcement
* Subscriber traffic shaping
* NAS identity
* Accounting emission

MikroTik does not own:

* Billing state
* Subscriber lifecycle state
* Payment state
* Policy source of truth
* Accounting interpretation
* Admin workflow authority

Every MikroTik NAS must have a stable identity.

NAS identity must be explicit in accounting, authentication, reconciliation, and audit workflows.

---

# 11. AAA Network Contract

FreeRADIUS is an internal AAA execution component.

FreeRADIUS must be isolated from:

* Public internet
* Subscriber devices
* Untrusted clients
* Management exposure

FreeRADIUS may communicate only with:

* Authorized NAS devices
* PostgreSQL
* Approved internal application services

Radius traffic must be treated as control-plane traffic, not public application traffic.

Accounting transport must be deterministic, attributable, and NAS-bound.

---

# 12. Database Topology Contract

PostgreSQL is the persistence authority boundary.

PostgreSQL must remain internal-only.

Allowed database clients:

* Backend Orchestrator
* Billing Core
* Accounting Engine
* Radius Policy Engine
* FreeRADIUS where explicitly authorized
* Approved internal maintenance workflow

Forbidden database clients:

* Public users
* Subscribers
* Customer portal frontend
* Telegram platform directly
* MikroTik direct mutation workflows
* External payment provider directly

Database topology must preserve auditability over convenience.

---

# 13. Application Topology Contract

Application services must be placed behind internal boundaries.

Application services must not rely on public reachability for internal communication.

Backend Orchestrator owns workflow execution.

Nginx owns ingress routing only.

Redis owns runtime coordination only.

No application service may create hidden topology authority.

---

# 14. Public Access Contract

Public exposure is limited to explicitly approved entrypoints.

Allowed public entrypoints:

* Customer portal
* Midtrans webhook endpoint
* Optional public status endpoint if approved

Forbidden public exposure:

* Admin backend without protected access boundary
* PostgreSQL
* Redis
* FreeRADIUS
* MikroTik management
* Docker runtime
* Internal metrics without protection
* Internal service discovery

Webhook endpoints must be treated as untrusted ingress until verified by application logic.

---

# 15. Management Plane Contract

Management access must be isolated from:

* Subscriber plane
* Public customer traffic
* Public webhook traffic
* AAA execution traffic

Operator access must follow:

* Explicit identity
* Least privilege
* Audit logging
* No shared authority
* No undocumented bypass path

Manual intervention must not create permanent topology drift.

Any emergency manual change must be reconciled back into system authority.

---

# 16. Multi-NAS Readiness

The topology must support multiple MikroTik NAS devices without redesign.

Each NAS must have:

* Explicit identity
* Explicit AAA trust relationship
* Explicit accounting attribution
* Explicit operational ownership
* Explicit failure boundary

Multi-NAS topology must preserve:

* Centralized billing authority
* Centralized policy authority
* Centralized accounting interpretation
* Per-NAS enforcement responsibility
* Per-NAS audit visibility

Forbidden:

* Shared anonymous NAS identity
* NAS-local billing decisions
* NAS-local subscriber lifecycle ownership
* Accounting records without NAS attribution

---

# 17. Multi-POP Readiness

The topology must support future POP expansion without authority redesign.

A POP may contain:

* One or more MikroTik NAS devices
* Subscriber access infrastructure
* Local operational boundary
* Local failure boundary

A POP must not contain independent business authority unless explicitly promoted by future architecture contract.

Central authority must remain with:

* Billing Core
* Radius Policy Engine
* Accounting Engine
* PostgreSQL authority domain

Multi-POP expansion must preserve:

* Subscriber identity consistency
* NAS identity consistency
* Accounting attribution
* Policy determinism
* Audit continuity

---

# 18. Failure Topology Doctrine

## 18.1 NAS Failure

NAS failure must be contained to subscribers served by that NAS.

NAS failure must not corrupt billing state, database state, or global accounting authority.

---

## 18.2 Radius Failure

Radius failure may interrupt authentication and accounting execution.

Radius failure must not mutate billing authority incorrectly.

Recovered Radius operation must reconcile with accounting evidence.

---

## 18.3 Database Failure

Database failure is an authority-plane failure.

Application services must not invent substitute authority.

Redis must not become temporary source of truth.

NAS must not become billing authority.

---

## 18.4 Backend Failure

Backend failure may pause lifecycle workflows.

Backend failure must not cause topology drift.

FreeRADIUS and MikroTik may continue current enforcement, but no new business authority may be invented.

---

## 18.5 Public Ingress Failure

Public ingress failure may interrupt portal and webhook access.

It must not expose internal services.

It must not alter AAA topology.

---

## 18.6 Network Partition

Network partition events must be treated as evidence gaps.

Partitioned components must not independently rewrite authority state.

Reconciliation must occur after connectivity restoration.

---

# 19. Security Topology Doctrine

Security is enforced by topology boundaries before application logic.

The topology must minimize:

* Public attack surface
* Management exposure
* Database exposure
* Radius exposure
* Cross-plane communication
* Hidden service paths

Every exposed path must have:

* Known owner
* Known purpose
* Known trust level
* Known allowed source
* Known allowed destination
* Audit capability

---

# 20. Operational Invariants

The following invariants must always hold:

1. Subscribers never access internal services directly.
2. PostgreSQL is never publicly exposed.
3. Redis is never publicly exposed.
4. FreeRADIUS is never publicly exposed.
5. MikroTik management is never publicly exposed.
6. Nginx is the only public ingress boundary.
7. Radius does not own billing state.
8. MikroTik does not own subscriber lifecycle state.
9. Redis does not own persistent authority.
10. Telegram automation does not bypass backend authority.
11. Midtrans webhook does not directly mutate database state.
12. Every NAS has explicit identity.
13. Every accounting record is attributable to a NAS.
14. Every management action is auditable.
15. Multi-NAS expansion does not require authority redesign.
16. Multi-POP expansion does not require business authority duplication.
17. Failure must degrade service, not corrupt authority.
18. Manual topology changes must not become hidden architecture.

---

# 21. Forbidden Topology Patterns

The following patterns are forbidden:

## 21.1 Public Database Exposure

PostgreSQL must never be reachable from public or subscriber networks.

---

## 21.2 Public Radius Exposure

FreeRADIUS must never be reachable from public internet.

---

## 21.3 Subscriber-to-Backend Access

Subscriber devices must never directly access backend internal services.

---

## 21.4 NAS as Business Authority

MikroTik must never become the source of billing, payment, or lifecycle truth.

---

## 21.5 Radius as Hidden Source of Truth

FreeRADIUS must not store hidden subscriber state that diverges from system authority.

---

## 21.6 Redis as Authority

Redis must not store permanent billing, subscriber, accounting, or policy authority.

---

## 21.7 Telegram Direct Mutation

Telegram automation must not directly mutate database, Radius, or NAS state.

---

## 21.8 Webhook Direct Persistence

External webhook payloads must not directly mutate authoritative records without validation and backend mediation.

---

## 21.9 Shared NAS Identity

Multiple MikroTik devices must not share indistinguishable NAS identity.

---

## 21.10 Management Through Public Plane

Operator management must not share the same uncontrolled exposure path as public customer ingress.

---

## 21.11 Hidden Side-Channel Connectivity

Any undocumented service-to-service path is forbidden.

---

## 21.12 Topology Drift by Manual Fix

Manual fixes must not create permanent divergence from documented topology authority.

---

# 22. Final Architecture Position

This topology contract establishes the network as an authority boundary system.

The design remains valid for:

* Single NAS RT/RW Net deployment
* Multi-NAS local ISP deployment
* Future multi-POP expansion

The topology must scale by adding explicit NAS and POP identities, not by changing authority ownership.

The correct scaling model is:

* More NAS devices
* More POP boundaries
* Same authority doctrine
* Same trust model
* Same accounting attribution
* Same management isolation
* Same public exposure discipline

Architecture must not be redesigned merely because subscriber count increases.

---

