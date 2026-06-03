# SYSTEM_AUTHORITY_CONTRACT.md

## 1. Contract Status

This document is the root authority contract for the ISP Billing + AAA Infrastructure.

All future architecture documents must explicitly inherit this contract using the following statement:

> This contract inherits all authority definitions from SYSTEM_AUTHORITY_CONTRACT.md.

No downstream contract may override, weaken, duplicate, or bypass any authority rule defined here.

---

## 2. Authority Doctrine

Authority means the exclusive right to define the truth of a domain.

A system may execute an action without owning the decision behind that action.

Authority is not the same as storage, execution, visibility, automation, or notification.

### Core Laws

1. Every domain must have exactly one authority owner.
2. Authority must be explicit.
3. Authority cannot be shared.
4. Authority cannot migrate during runtime.
5. Execution systems cannot become sources of truth.
6. Synchronization must be directional.
7. Manual synchronization is forbidden.
8. Hidden state is forbidden.
9. Replay must produce identical authority outcomes.

---

## 3. Source of Truth Doctrine

The Backend Orchestrator is the primary business authority.

PostgreSQL is the persistence authority, not the business authority.

FreeRADIUS is the authentication and policy execution layer.

MikroTik is the runtime enforcement layer.

Midtrans is the external payment evidence provider.

Telegram is the notification delivery layer.

No execution system may become the owner of business state.

---

## 4. Global Authority Matrix

| Domain               | Authority Owner                 | Consumers                            | Synchronization Targets                    | Forbidden Owners                         |
| -------------------- | ------------------------------- | ------------------------------------ | ------------------------------------------ | ---------------------------------------- |
| Customer             | Backend Orchestrator            | Billing, Provisioning, Notification  | PostgreSQL                                 | FreeRADIUS, MikroTik, Midtrans, Telegram |
| Service              | Backend Orchestrator            | Billing, Radius Policy, Provisioning | PostgreSQL, FreeRADIUS                     | MikroTik, Telegram                       |
| Credentials          | Backend Orchestrator            | FreeRADIUS                           | PostgreSQL, FreeRADIUS                     | MikroTik, Telegram                       |
| Billing State        | Billing Engine                  | Radius Policy, Notification          | PostgreSQL                                 | FreeRADIUS, MikroTik                     |
| Invoice              | Billing Engine                  | Payment Engine, Notification         | PostgreSQL                                 | Midtrans, Telegram                       |
| Payment Evidence     | Midtrans                        | Payment Engine                       | Backend Orchestrator, PostgreSQL           | Telegram, MikroTik                       |
| Payment State        | Payment Engine                  | Billing Engine, Notification         | PostgreSQL                                 | Midtrans, Telegram                       |
| Radius Policy        | Radius Policy Engine            | FreeRADIUS                           | FreeRADIUS                                 | MikroTik                                 |
| Authentication       | FreeRADIUS                      | Backend Orchestrator, Accounting     | PostgreSQL logs                            | MikroTik                                 |
| Authorization        | Radius Policy Engine            | FreeRADIUS, MikroTik                 | FreeRADIUS                                 | MikroTik                                 |
| Accounting Evidence  | Accounting Engine               | Billing, Audit, Reconciliation       | PostgreSQL                                 | MikroTik as final owner                  |
| Runtime Session      | MikroTik                        | Accounting, Backend Orchestrator     | Accounting Engine                          | Billing Engine                           |
| Suspension Decision  | Billing Engine                  | Radius Policy, Enforcement           | Backend Orchestrator, FreeRADIUS, MikroTik | MikroTik                                 |
| Suspension Execution | MikroTik                        | Accounting, Backend                  | Runtime logs                               | Billing Engine                           |
| Restoration Decision | Billing Engine / Payment Engine | Radius Policy, Enforcement           | Backend Orchestrator                       | MikroTik                                 |
| Notification         | Notification Engine             | Telegram                             | Telegram                                   | Telegram as state owner                  |

---

## 5. Component Authority Contracts

### 5.1 Backend Orchestrator

Owns business state orchestration.

Allowed:

* Create customer state.
* Create service state.
* Own lifecycle transitions.
* Coordinate synchronization.
* Validate infrastructure consistency.

Forbidden:

* Acting as raw packet authority.
* Treating execution confirmation as business truth.
* Allowing manual state mutation outside governed workflows.

### 5.2 PostgreSQL

Owns durable persistence.

Allowed:

* Store authoritative records.
* Store event history.
* Store audit logs.
* Support replay and reconciliation.

Forbidden:

* Making business decisions.
* Acting as a runtime authority.
* Becoming an implicit workflow engine.

### 5.3 FreeRADIUS

Owns authentication execution and Radius policy execution.

Allowed:

* Validate PPPoE authentication.
* Apply generated Radius policy.
* Emit authentication/accounting evidence.

Forbidden:

* Owning customer state.
* Owning billing state.
* Deciding suspension or restoration.
* Becoming the source of truth for subscriber status.

### 5.4 MikroTik

Owns runtime network enforcement.

Allowed:

* Execute PPPoE session enforcement.
* Disconnect sessions.
* Apply runtime service isolation.
* Emit runtime session evidence.

Forbidden:

* Owning billing state.
* Owning customer state.
* Deciding suspension.
* Deciding restoration.
* Becoming a manual policy source.

### 5.5 Midtrans

Owns external payment evidence.

Allowed:

* Emit payment notifications.
* Provide settlement evidence.
* Provide payment failure evidence.

Forbidden:

* Owning invoice state.
* Owning billing state.
* Triggering service restoration directly.
* Acting as customer authority.

### 5.6 Telegram

Owns notification delivery only.

Allowed:

* Deliver alerts.
* Deliver operational messages.
* Receive operator commands only through governed workflows.

Forbidden:

* Owning infrastructure state.
* Mutating customer, billing, Radius, or MikroTik state directly.
* Acting as an authority bypass channel.

---

## 6. Operational State Ownership Model

| Lifecycle                 | State Authority                 | Transition Authority | Execution Authority         |
| ------------------------- | ------------------------------- | -------------------- | --------------------------- |
| Customer Lifecycle        | Backend Orchestrator            | Backend Orchestrator | Backend Orchestrator        |
| Service Lifecycle         | Backend Orchestrator            | Backend Orchestrator | Provisioning Engine         |
| Credential Lifecycle      | Backend Orchestrator            | Backend Orchestrator | FreeRADIUS sync             |
| Billing Lifecycle         | Billing Engine                  | Billing Engine       | Backend Orchestrator        |
| Invoice Lifecycle         | Billing Engine                  | Billing Engine       | Payment Engine              |
| Payment Lifecycle         | Payment Engine                  | Payment Engine       | Midtrans evidence ingestion |
| Runtime Session Lifecycle | MikroTik                        | MikroTik runtime     | MikroTik                    |
| Suspension Lifecycle      | Billing Engine                  | Billing Engine       | FreeRADIUS + MikroTik       |
| Restoration Lifecycle     | Billing Engine / Payment Engine | Billing Engine       | FreeRADIUS + MikroTik       |
| Termination Lifecycle     | Backend Orchestrator            | Backend Orchestrator | Provisioning + Enforcement  |

---

## 7. Decision Authority Model

Decision Authority defines what must happen.

Execution Authority performs what has already been decided.

Execution never implies authority.

Examples:

* Billing decides suspension.
* Radius Policy Engine translates suspension into policy.
* FreeRADIUS serves the policy.
* MikroTik enforces the session result.

MikroTik executing suspension does not mean MikroTik owns suspension.

FreeRADIUS denying access does not mean FreeRADIUS owns billing state.

Midtrans confirming payment does not mean Midtrans owns restoration.

Telegram notifying operators does not mean Telegram owns operations.

---

## 8. AAA Authority Contract

### Authentication

Authority:

* FreeRADIUS executes authentication.
* Backend Orchestrator owns credential lifecycle.

Forbidden:

* MikroTik must not own credentials.
* Manual Radius credential edits are forbidden.

### Authorization

Authority:

* Radius Policy Engine owns policy generation.
* FreeRADIUS executes generated policy.

Forbidden:

* MikroTik must not invent subscriber policy.
* FreeRADIUS must not decide business eligibility.

### Accounting

Authority:

* Accounting Engine owns accounting evidence interpretation.
* MikroTik and FreeRADIUS may emit raw evidence.

Forbidden:

* Raw NAS counters must not become final billing truth without reconciliation.

### Runtime Enforcement

Authority:

* MikroTik owns runtime enforcement execution.
* Billing and Radius Policy own the decision chain.

Forbidden:

* Runtime enforcement must not create business authority.

---

## 9. Billing Authority Contract

Billing Engine owns:

* Invoice generation.
* Billing status.
* Due state.
* Overdue state.
* Suspension decision.
* Restoration decision after payment validation.

Billing Engine does not own:

* Runtime session execution.
* Raw authentication.
* Raw accounting packet emission.
* Payment gateway evidence.

All billing transitions must be replay-safe and auditable.

---

## 10. Enforcement Authority Contract

Enforcement is execution, not business truth.

MikroTik may:

* Disconnect a subscriber.
* Enforce isolated profiles.
* Enforce active service profiles.
* Report runtime session state.

MikroTik may not:

* Decide why a subscriber is suspended.
* Decide whether payment is valid.
* Decide whether restoration is allowed.
* Store independent subscriber business state.

---

## 11. Synchronization Authority Contract

Synchronization must always follow authority direction.

Allowed directions:

* Backend Orchestrator → PostgreSQL
* Backend Orchestrator → FreeRADIUS
* Billing Engine → Radius Policy Engine
* Radius Policy Engine → FreeRADIUS
* Payment Engine → Billing Engine
* Billing Engine → Enforcement Workflow
* Enforcement Workflow → MikroTik
* MikroTik / FreeRADIUS → Accounting Engine
* Accounting Engine → PostgreSQL
* Notification Engine → Telegram

Forbidden synchronization patterns:

* MikroTik → Billing decision
* FreeRADIUS → Customer truth
* Telegram → Direct infrastructure mutation
* Midtrans → Direct service restoration
* Manual database edits → Runtime sync
* Manual MikroTik edits → Business truth
* Manual FreeRADIUS edits → Business truth
* Circular sync loops

---

## 12. Event Authority Model

| Event                | Producer                          | Authority Owner      | Consumers                  |
| -------------------- | --------------------------------- | -------------------- | -------------------------- |
| Payment Event        | Midtrans                          | Payment Engine       | Billing, Notification      |
| Authentication Event | FreeRADIUS                        | FreeRADIUS           | Accounting, Audit          |
| Authorization Event  | Radius Policy Engine / FreeRADIUS | Radius Policy Engine | MikroTik, Accounting       |
| Accounting Event     | FreeRADIUS / MikroTik             | Accounting Engine    | Billing, Audit             |
| Provisioning Event   | Provisioning Engine               | Backend Orchestrator | FreeRADIUS, MikroTik       |
| Suspension Event     | Billing Engine                    | Billing Engine       | Radius Policy, Enforcement |
| Restoration Event    | Billing / Payment Engine          | Billing Engine       | Radius Policy, Enforcement |
| Notification Event   | Notification Engine               | Notification Engine  | Telegram                   |

---

## 13. Human Authority Model

### Administrator

Permitted:

* Approve governed state transitions.
* View audit logs.
* Trigger controlled recovery workflows.

Forbidden:

* Direct database mutation.
* Manual FreeRADIUS mutation.
* Manual MikroTik subscriber policy mutation.
* Manual payment state mutation.

### Support Operator

Permitted:

* View customer state.
* Request governed actions.
* Send customer notifications.

Forbidden:

* Direct suspend/restore outside workflow.
* Direct Radius edits.
* Direct MikroTik edits.

### Network Operator

Permitted:

* Inspect NAS runtime state.
* Execute approved emergency network actions.
* Report runtime anomalies.

Forbidden:

* Changing business state.
* Creating subscriber policy manually.
* Restoring suspended users manually.

### Emergency Operator

Permitted:

* Execute emergency containment.
* Trigger degraded operation mode.
* Escalate recovery.

Forbidden:

* Creating permanent state outside audit.
* Bypassing reconciliation.
* Converting emergency action into business truth.

---

## 14. Failure Authority Model

### PostgreSQL Unavailable

Authority is preserved.

No system may create unofficial truth.

Runtime may continue only under degraded rules.

Recovery requires reconciliation.

### FreeRADIUS Unavailable

Authentication execution is degraded.

Business authority remains in Backend and Billing.

No manual Radius reconstruction may become truth.

### MikroTik Unavailable

Runtime enforcement is degraded.

Billing and policy authority remain intact.

Pending enforcement actions must be queued and reconciled.

### Midtrans Unavailable

Payment evidence ingestion is degraded.

Billing must not infer successful payment without evidence.

Restoration must wait for valid payment authority.

### Telegram Unavailable

Notification delivery is degraded.

Infrastructure authority remains unaffected.

No state transition may depend solely on Telegram delivery.

### Synchronization Failure

Authority owner remains unchanged.

Failed sync must be retried, reconciled, and audited.

Sync failure must never transfer authority to the target system.

---

## 15. Replay-Safe Authority Model

Replay must produce identical authority outcomes.

Rules:

1. Duplicate events must be idempotent.
2. Delayed events must be validated against current authority state.
3. Stale events must not override newer authority state.
4. Out-of-order events must be reordered or rejected deterministically.
5. External evidence must be stored before interpretation.
6. Replayed payment events must not duplicate payment state.
7. Replayed suspension events must not duplicate enforcement state.
8. Replayed restoration events must not bypass billing validation.
9. Runtime evidence must not rewrite business truth.
10. Reconciliation must preserve authority ownership.

---

## 16. Operational Invariants

1. One domain has one authority owner.
2. Authority is explicit or invalid.
3. Execution is not authority.
4. Storage is not authority.
5. Notification is not authority.
6. Runtime state is not business truth.
7. Payment evidence is not invoice ownership.
8. Authentication execution is not customer ownership.
9. Accounting evidence is not billing ownership.
10. Synchronization is directional.
11. Manual synchronization is forbidden.
12. Hidden state is forbidden.
13. Duplicate ownership is forbidden.
14. Circular authority is forbidden.
15. Authority cannot migrate during runtime.
16. Emergency operation must remain auditable.
17. Failed synchronization must not create new authority.
18. Replay must preserve identical outcomes.
19. All state transitions must be attributable.
20. All future contracts must inherit this contract.

---

## 17. Forbidden Ownership Patterns

### Hidden State

A system stores meaningful state that is not declared in the authority matrix.

Forbidden because it creates invisible truth.

### Duplicate Authority

Two systems can decide the same domain state.

Forbidden because it creates conflict and drift.

### Mixed Ownership

Decision and execution are blended without boundary.

Forbidden because runtime systems become business systems.

### Circular Authority

System A depends on System B while System B depends on System A for truth.

Forbidden because recovery becomes non-deterministic.

### Manual Synchronization

Operators manually copy state between systems.

Forbidden because it is not replay-safe.

### Runtime Authority Migration

An execution system becomes authority during outage or emergency.

Forbidden because degraded mode must not redefine architecture.

### Authority Shadowing

A target system stores a second version of authority state.

Forbidden because shadow truth eventually diverges.

### Implicit Ownership

A system acts as owner because implementation makes it convenient.

Forbidden because convenience is not architecture.

### Undocumented Ownership

A domain exists without declared authority.

Forbidden because audit and recovery become impossible.

### Execution-Owned Business State

MikroTik, FreeRADIUS, Telegram, or Midtrans becomes business truth.

Forbidden because these systems execute, emit, or deliver; they do not own business decisions.

---

## 18. Inheritance Requirement For Future Contracts

Every future PRD, contract, or architecture document must include this statement near the top:

> This contract inherits all authority definitions from SYSTEM_AUTHORITY_CONTRACT.md.

This requirement applies to:

* NETWORK_TOPOLOGY.md
* RADIUS_POLICY_ENGINE.md
* ACCOUNTING_ENGINE.md
* PROVISIONING_LIFECYCLE.md
* BILLING_ENGINE.md
* PAYMENT_ENGINE.md
* NOTIFICATION_ENGINE.md
* INFRASTRUCTURE_RUNTIME.md
* ACCOUNTING_RECONCILIATION_CONTRACT.md
* MULTI_NAS_SCALING_CONTRACT.md

If a future document conflicts with this contract, the future document is invalid.

---

## 19. Final Authority Rule

SYSTEM_AUTHORITY_CONTRACT.md is the root of the architecture tree.

All downstream infrastructure documents are children of this contract.

Authority starts here.

Implementation must not begin until every major domain has a declared authority owner.

