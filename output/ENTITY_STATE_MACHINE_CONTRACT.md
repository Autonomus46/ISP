# ENTITY_STATE_MACHINE_CONTRACT.md

## 1. Contract Authority

This contract defines the authoritative entity state machine model for the ISP Billing + AAA Infrastructure.

`SUBSCRIBER_STATE_MACHINE.md` is considered a subset of this contract.

This contract inherits all authority definitions from `SYSTEM_AUTHORITY_CONTRACT.md`.

This contract inherits all runtime definitions from `INFRASTRUCTURE_RUNTIME.md`.

This contract inherits all reconciliation definitions from `ACCOUNTING_RECONCILIATION_CONTRACT.md`.

This contract inherits all billing definitions from `BILLING_CORE_CONTRACT.md`.

This contract inherits all payment definitions from `PAYMENT_LIFECYCLE_CONTRACT.md`.

This contract inherits all suspension definitions from `SUSPENSION_ORCHESTRATION_CONTRACT.md`.

This contract inherits all restoration definitions from `RESTORATION_ORCHESTRATION_CONTRACT.md`.

This contract inherits all audit definitions from `OPERATIONAL_AUDIT_CONTRACT.md`.

This contract inherits all event definitions from `EVENT_BUS_CONTRACT.md`.

This contract inherits all Telegram operations definitions from `TELEGRAM_OPERATIONS_CONTRACT.md`.

This contract inherits all Multi-NAS definitions from `MULTI_NAS_SCALING_CONTRACT.md`.

This contract inherits all domain model definitions from `DOMAIN_MODEL_CONTRACT.md`.

---

# 2. State Machine Doctrine

Entity states are authoritative lifecycle positions.

A state is not a database enum, UI label, API response field, operational note, manual flag, or temporary execution marker.

A valid authoritative state must define:

* authority owner
* lifecycle owner
* transition owner
* evidence owner
* operational executor
* allowed source state
* allowed target state
* replay behavior
* audit visibility

No entity state may exist without ownership.

No transition may occur without authority.

No replay may create new authority.

No UI, API, database row, infrastructure component, Telegram command, or operator action may independently mutate authoritative state.

---

# 3. State Ownership Model

## 3.1 Authority Owner

The authority owner decides whether a lifecycle transition is valid.

## 3.2 Lifecycle Owner

The lifecycle owner owns the entity’s full state progression.

## 3.3 Evidence Owner

The evidence owner preserves the proof required to validate the transition.

## 3.4 Operational Executor

The operational executor performs the required infrastructure or system action but does not own the authoritative state.

## 3.5 Audit Owner

The audit owner preserves traceability, causality, and forensic reconstruction.

---

# 4. Entity State Machine Taxonomy

The infrastructure recognizes the following authoritative state machines:

* Subscriber State Machine
* Service State Machine
* Billing State Machine
* Invoice State Machine
* Payment State Machine
* Provisioning State Machine
* Accounting Session State Machine
* Suspension State Machine
* Restoration State Machine
* NAS Attachment State Machine
* Migration State Machine
* Operator Command State Machine
* Audit Reconstruction State Machine

Each state machine is independent in ownership but coordinated through authority-safe transitions.

---

# 5. Subscriber State Machine

## 5.1 States

* Draft
* Registered
* Verified
* Active
* Suspended
* Terminated
* Archived

## 5.2 Doctrine

The Subscriber State Machine represents the authoritative lifecycle of a subscriber identity.

Subscriber state does not directly represent service availability, payment status, NAS placement, Radius authorization, or operational session state.

## 5.3 Allowed Transitions

| Source     | Target     | Authority Owner                       | Evidence                                            |
| ---------- | ---------- | ------------------------------------- | --------------------------------------------------- |
| Draft      | Registered | Backend Orchestrator                  | registration evidence                               |
| Registered | Verified   | Backend Orchestrator                  | identity/service validation                         |
| Verified   | Active     | Billing Core + Provisioning Lifecycle | billing eligibility + service provisioning evidence |
| Active     | Suspended  | Suspension Orchestration              | suspension decision evidence                        |
| Suspended  | Active     | Restoration Orchestration             | debt/service restoration evidence                   |
| Active     | Terminated | Backend Orchestrator                  | termination decision                                |
| Suspended  | Terminated | Backend Orchestrator                  | termination decision                                |
| Terminated | Archived   | Operational Audit                     | archival evidence                                   |

## 5.4 Forbidden Transitions

* Draft → Active
* Registered → Active
* Terminated → Active
* Archived → any active lifecycle state
* Suspended → Verified
* Any UI-driven direct mutation

## 5.5 Cross-Domain Rules

Subscriber Active requires valid Service Active or Service Provisioned state.

Subscriber Suspended must not directly mutate Invoice or Payment state.

Subscriber Terminated must trigger service termination workflow but must not erase historical accounting evidence.

---

# 6. Service State Machine

## 6.1 States

* Requested
* ProvisioningPending
* ProvisioningInProgress
* Provisioned
* Active
* Restricted
* RestorationPending
* Terminated
* Failed

## 6.2 Doctrine

Service state represents the lifecycle of a subscriber’s access product.

Service state is not Radius state, MikroTik state, billing state, or session state.

## 6.3 Allowed Transitions

| Source                 | Target                 | Authority Owner           | Executor               |
| ---------------------- | ---------------------- | ------------------------- | ---------------------- |
| Requested              | ProvisioningPending    | Backend Orchestrator      | Provisioning Lifecycle |
| ProvisioningPending    | ProvisioningInProgress | Provisioning Lifecycle    | Backend Orchestrator   |
| ProvisioningInProgress | Provisioned            | Provisioning Lifecycle    | FreeRADIUS / MikroTik  |
| Provisioned            | Active                 | Billing Core              | Backend Orchestrator   |
| Active                 | Restricted             | Suspension Orchestration  | MikroTik / FreeRADIUS  |
| Restricted             | RestorationPending     | Restoration Orchestration | Backend Orchestrator   |
| RestorationPending     | Active                 | Restoration Orchestration | MikroTik / FreeRADIUS  |
| Active                 | Terminated             | Backend Orchestrator      | Provisioning Lifecycle |
| Any non-terminal       | Failed                 | Lifecycle Owner           | Operational executor   |

## 6.4 Rules

Provisioned does not mean Active.

Active requires billing eligibility.

Restricted does not mean subscriber identity is terminated.

Failed requires evidence and audit trace.

---

# 7. Billing State Machine

## 7.1 States

* BillingEligible
* BillingCycleOpen
* InvoiceGenerated
* DebtOpen
* DebtResolved
* BillingSuspended
* BillingClosed

## 7.2 Doctrine

Billing Core owns all billing lifecycle states.

No payment, service, subscriber, Telegram command, or Radius system may directly mutate billing state.

## 7.3 Allowed Transitions

| Source                  | Target           | Authority Owner | Evidence                    |
| ----------------------- | ---------------- | --------------- | --------------------------- |
| BillingEligible         | BillingCycleOpen | Billing Core    | billing cycle rule          |
| BillingCycleOpen        | InvoiceGenerated | Billing Core    | invoice generation evidence |
| InvoiceGenerated        | DebtOpen         | Billing Core    | unpaid invoice evidence     |
| DebtOpen                | DebtResolved     | Billing Core    | settlement confirmation     |
| DebtOpen                | BillingSuspended | Billing Core    | overdue rule                |
| DebtResolved            | BillingEligible  | Billing Core    | resolved debt               |
| BillingSuspended        | DebtResolved     | Billing Core    | settlement confirmation     |
| Any valid billing state | BillingClosed    | Billing Core    | subscriber/service closure  |

## 7.4 Forbidden Rules

Payment settlement may inform Billing Core but may not directly mutate billing state.

Service restriction may be triggered by billing state but cannot define billing state.

---

# 8. Invoice State Machine

## 8.1 States

* Draft
* Issued
* PartiallyPaid
* Paid
* Overdue
* Cancelled
* WrittenOff

## 8.2 Doctrine

Invoice state is owned by Billing Core.

Invoice state represents financial claim lifecycle, not payment execution lifecycle.

## 8.3 Allowed Transitions

| Source        | Target        | Authority Owner | Evidence                      |
| ------------- | ------------- | --------------- | ----------------------------- |
| Draft         | Issued        | Billing Core    | invoice issue evidence        |
| Issued        | PartiallyPaid | Billing Core    | partial settlement evidence   |
| Issued        | Paid          | Billing Core    | full settlement evidence      |
| PartiallyPaid | Paid          | Billing Core    | remaining settlement evidence |
| Issued        | Overdue       | Billing Core    | due-date rule                 |
| Overdue       | Paid          | Billing Core    | settlement evidence           |
| Draft         | Cancelled     | Billing Core    | cancellation reason           |
| Issued        | Cancelled     | Billing Core    | authorized cancellation       |
| Overdue       | WrittenOff    | Billing Core    | write-off decision            |

## 8.4 Audit Rules

All invoice transitions require audit visibility.

Cancelled and WrittenOff are historically reconstructable terminal financial states.

---

# 9. Payment State Machine

## 9.1 States

* Initiated
* Pending
* EvidenceReceived
* SettlementPending
* Settled
* Failed
* Expired
* Reversed
* Disputed

## 9.2 Doctrine

Payment state is owned by the Payment Lifecycle.

Settlement confirmation is not equivalent to user-submitted evidence.

## 9.3 Allowed Transitions

| Source            | Target            | Authority Owner   | Evidence                |
| ----------------- | ----------------- | ----------------- | ----------------------- |
| Initiated         | Pending           | Payment Lifecycle | payment intent          |
| Pending           | EvidenceReceived  | Payment Lifecycle | gateway/user evidence   |
| EvidenceReceived  | SettlementPending | Payment Lifecycle | validation evidence     |
| SettlementPending | Settled           | Payment Lifecycle | settlement confirmation |
| Pending           | Expired           | Payment Lifecycle | expiration evidence     |
| Pending           | Failed            | Payment Lifecycle | failure evidence        |
| Settled           | Reversed          | Payment Lifecycle | reversal evidence       |
| Settled           | Disputed          | Payment Lifecycle | dispute evidence        |
| Disputed          | Settled           | Payment Lifecycle | dispute resolution      |
| Disputed          | Reversed          | Payment Lifecycle | reversal confirmation   |

## 9.4 Duplicate Payment Doctrine

Duplicate payments must not create duplicate invoice settlement.

Duplicate evidence must be replay-safe and linked to the original payment lifecycle.

---

# 10. Provisioning State Machine

## 10.1 States

* Requested
* Accepted
* InProgress
* Applied
* Verified
* Failed
* RolledBack
* Cancelled

## 10.2 Doctrine

Provisioning state represents infrastructure synchronization lifecycle.

Provisioning does not own subscriber identity, billing status, or payment status.

## 10.3 Allowed Transitions

| Source     | Target     | Authority Owner        | Executor              |
| ---------- | ---------- | ---------------------- | --------------------- |
| Requested  | Accepted   | Provisioning Lifecycle | Backend Orchestrator  |
| Accepted   | InProgress | Provisioning Lifecycle | Backend Orchestrator  |
| InProgress | Applied    | Provisioning Lifecycle | FreeRADIUS / MikroTik |
| Applied    | Verified   | Provisioning Lifecycle | Reconciliation Engine |
| InProgress | Failed     | Provisioning Lifecycle | Operational executor  |
| Failed     | RolledBack | Provisioning Lifecycle | Backend Orchestrator  |
| Requested  | Cancelled  | Provisioning Lifecycle | Backend Orchestrator  |
| Accepted   | Cancelled  | Provisioning Lifecycle | Backend Orchestrator  |

## 10.4 Orphan Prevention

No provisioning state may be considered Verified unless Radius, NAS, and authoritative backend expectations are consistent.

---

# 11. Accounting Session State Machine

## 11.1 States

* Started
* Active
* InterimReceived
* Stale
* Stopped
* Reconciled
* Orphaned
* Invalidated

## 11.2 Doctrine

Accounting session state is evidence state.

It does not own subscriber lifecycle, billing lifecycle, or service lifecycle.

## 11.3 Allowed Transitions

| Source               | Target          | Authority Owner       | Evidence                   |
| -------------------- | --------------- | --------------------- | -------------------------- |
| Started              | Active          | Accounting Engine     | Start packet               |
| Active               | InterimReceived | Accounting Engine     | Interim update             |
| InterimReceived      | Active          | Accounting Engine     | session continuity         |
| Active               | Stale           | Reconciliation Engine | timeout evidence           |
| Stale                | Reconciled      | Reconciliation Engine | NAS/Radius validation      |
| Active               | Stopped         | Accounting Engine     | Stop packet                |
| Stopped              | Reconciled      | Reconciliation Engine | session closure validation |
| Any accounting state | Orphaned        | Reconciliation Engine | missing authority link     |
| Any accounting state | Invalidated     | Reconciliation Engine | invalid evidence           |

## 11.4 Ghost Session Doctrine

Ghost sessions are not authoritative sessions.

They are reconciliation findings requiring evidence classification.

---

# 12. Suspension State Machine

## 12.1 States

* SuspensionRequested
* SuspensionApproved
* EnforcementPending
* EnforcementApplied
* EnforcementVerified
* SuspensionActive
* SuspensionFailed
* SuspensionCancelled

## 12.2 Doctrine

Suspension state is owned by Suspension Orchestration.

Billing may trigger suspension eligibility but does not execute enforcement directly.

## 12.3 Allowed Transitions

| Source              | Target              | Authority Owner          | Executor              |
| ------------------- | ------------------- | ------------------------ | --------------------- |
| SuspensionRequested | SuspensionApproved  | Suspension Orchestration | Backend Orchestrator  |
| SuspensionApproved  | EnforcementPending  | Suspension Orchestration | Backend Orchestrator  |
| EnforcementPending  | EnforcementApplied  | Suspension Orchestration | MikroTik / FreeRADIUS |
| EnforcementApplied  | EnforcementVerified | Reconciliation Engine    | Backend Orchestrator  |
| EnforcementVerified | SuspensionActive    | Suspension Orchestration | Backend Orchestrator  |
| Any non-terminal    | SuspensionFailed    | Suspension Orchestration | Operational executor  |
| SuspensionRequested | SuspensionCancelled | Suspension Orchestration | Backend Orchestrator  |
| SuspensionApproved  | SuspensionCancelled | Suspension Orchestration | Backend Orchestrator  |

---

# 13. Restoration State Machine

## 13.1 States

* RestorationRequested
* RestorationApproved
* EnforcementRemovalPending
* EnforcementRemoved
* RestorationVerified
* ServiceRestored
* RestorationFailed
* RestorationCancelled

## 13.2 Doctrine

Restoration state is owned by Restoration Orchestration.

Payment resolution may authorize restoration but does not directly restore service.

## 13.3 Allowed Transitions

| Source                    | Target                    | Authority Owner           | Executor              |
| ------------------------- | ------------------------- | ------------------------- | --------------------- |
| RestorationRequested      | RestorationApproved       | Restoration Orchestration | Backend Orchestrator  |
| RestorationApproved       | EnforcementRemovalPending | Restoration Orchestration | Backend Orchestrator  |
| EnforcementRemovalPending | EnforcementRemoved        | Restoration Orchestration | MikroTik / FreeRADIUS |
| EnforcementRemoved        | RestorationVerified       | Reconciliation Engine     | Backend Orchestrator  |
| RestorationVerified       | ServiceRestored           | Restoration Orchestration | Backend Orchestrator  |
| Any non-terminal          | RestorationFailed         | Restoration Orchestration | Operational executor  |
| RestorationRequested      | RestorationCancelled      | Restoration Orchestration | Backend Orchestrator  |
| RestorationApproved       | RestorationCancelled      | Restoration Orchestration | Backend Orchestrator  |

---

# 14. NAS Attachment State Machine

## 14.1 States

* Unassigned
* Assigned
* ActiveAttachment
* MigrationPending
* Migrating
* Migrated
* Detached
* Failed

## 14.2 Doctrine

NAS attachment state represents subscriber placement within access infrastructure.

It does not own subscriber identity or billing state.

## 14.3 Allowed Transitions

| Source           | Target           | Authority Owner               | Evidence                     |
| ---------------- | ---------------- | ----------------------------- | ---------------------------- |
| Unassigned       | Assigned         | Multi-NAS Placement Authority | placement decision           |
| Assigned         | ActiveAttachment | Multi-NAS Runtime             | accounting/access evidence   |
| ActiveAttachment | MigrationPending | Multi-NAS Placement Authority | migration intent             |
| MigrationPending | Migrating        | Migration Authority           | migration execution evidence |
| Migrating        | Migrated         | Migration Authority           | migration verification       |
| Migrated         | ActiveAttachment | Multi-NAS Runtime             | new NAS attachment evidence  |
| ActiveAttachment | Detached         | Multi-NAS Placement Authority | detachment decision          |
| Any non-terminal | Failed           | Multi-NAS Runtime             | failure evidence             |

---

# 15. Migration State Machine

## 15.1 States

* MigrationRequested
* MigrationApproved
* MigrationPrepared
* MigrationExecuting
* MigrationVerified
* MigrationCompleted
* MigrationFailed
* MigrationRolledBack

## 15.2 Doctrine

Migration state governs controlled subscriber movement between NAS infrastructure.

Migration must preserve accounting attribution and service continuity.

## 15.3 Allowed Transitions

| Source             | Target              | Authority Owner               | Evidence              |
| ------------------ | ------------------- | ----------------------------- | --------------------- |
| MigrationRequested | MigrationApproved   | Multi-NAS Placement Authority | approval evidence     |
| MigrationApproved  | MigrationPrepared   | Migration Authority           | preparation evidence  |
| MigrationPrepared  | MigrationExecuting  | Migration Authority           | execution start       |
| MigrationExecuting | MigrationVerified   | Reconciliation Engine         | verification evidence |
| MigrationVerified  | MigrationCompleted  | Migration Authority           | completion evidence   |
| Any non-terminal   | MigrationFailed     | Migration Authority           | failure evidence      |
| MigrationFailed    | MigrationRolledBack | Migration Authority           | rollback evidence     |

---

# 16. Operator Command State Machine

## 16.1 States

* Drafted
* Submitted
* AwaitingApproval
* Approved
* Rejected
* Executing
* Executed
* Failed
* Cancelled
* Audited

## 16.2 Doctrine

Operator commands are not authority by default.

A command may request a transition but cannot own the transition unless explicitly granted by the relevant authority contract.

## 16.3 Allowed Transitions

| Source           | Target           | Authority Owner               | Evidence              |
| ---------------- | ---------------- | ----------------------------- | --------------------- |
| Drafted          | Submitted        | Operator                      | command draft         |
| Submitted        | AwaitingApproval | Telegram Operations           | command submission    |
| AwaitingApproval | Approved         | Approval Authority            | approval evidence     |
| AwaitingApproval | Rejected         | Approval Authority            | rejection evidence    |
| Approved         | Executing        | Backend Orchestrator          | execution start       |
| Executing        | Executed         | Backend Orchestrator          | execution result      |
| Executing        | Failed           | Backend Orchestrator          | failure evidence      |
| Drafted          | Cancelled        | Operator                      | cancellation          |
| Submitted        | Cancelled        | Operator / Approval Authority | cancellation evidence |
| Executed         | Audited          | Operational Audit             | audit record          |
| Failed           | Audited          | Operational Audit             | audit record          |

---

# 17. Audit Reconstruction State Machine

## 17.1 States

* ReconstructionRequested
* EvidenceCollected
* TimelineBuilt
* ConflictDetected
* Reconciled
* ReconstructionCompleted
* ReconstructionFailed

## 17.2 Doctrine

Audit reconstruction does not mutate business truth.

It reconstructs historical truth from preserved evidence.

## 17.3 Allowed Transitions

| Source                  | Target                  | Authority Owner       | Evidence                |
| ----------------------- | ----------------------- | --------------------- | ----------------------- |
| ReconstructionRequested | EvidenceCollected       | Operational Audit     | evidence collection     |
| EvidenceCollected       | TimelineBuilt           | Operational Audit     | timeline evidence       |
| TimelineBuilt           | ConflictDetected        | Operational Audit     | inconsistency evidence  |
| ConflictDetected        | Reconciled              | Reconciliation Engine | reconciliation result   |
| TimelineBuilt           | ReconstructionCompleted | Operational Audit     | completed timeline      |
| Reconciled              | ReconstructionCompleted | Operational Audit     | resolved reconstruction |
| Any non-terminal        | ReconstructionFailed    | Operational Audit     | failure evidence        |

---

# 18. Cross-Domain State Consistency Model

## 18.1 Subscriber ↔ Service

Subscriber Active requires service eligibility.

Service Restricted must not automatically terminate subscriber identity.

## 18.2 Service ↔ Provisioning

Service Provisioned requires provisioning Verified.

Provisioning Failed prevents Service Active.

## 18.3 Service ↔ Billing

Billing determines service eligibility.

Service state does not determine invoice truth.

## 18.4 Invoice ↔ Payment

Invoice Paid requires valid settlement evidence.

Payment Settled informs invoice settlement but does not bypass Billing Core authority.

## 18.5 Billing ↔ Suspension

Billing DebtOpen may authorize suspension request.

Suspension state owns enforcement lifecycle.

## 18.6 Suspension ↔ Service

SuspensionActive implies Service Restricted.

Service Restricted must reference suspension evidence.

## 18.7 Restoration ↔ Service

Service restoration requires restoration verification.

Payment alone does not restore service.

## 18.8 Subscriber ↔ Session

Session activity does not prove subscriber authority.

Accounting evidence must be attributed to authoritative subscriber identity.

## 18.9 Subscriber ↔ NAS

NAS attachment does not define subscriber ownership.

Placement must be authority-owned.

## 18.10 Migration ↔ Accounting

Migration must preserve session attribution.

Accounting discontinuity during migration must be reconciled.

## 18.11 Operator Command ↔ Audit

Every operator command affecting lifecycle state requires audit traceability.

---

# 19. Transition Authority Matrix

Every transition must contain:

* source state
* target state
* authority owner
* lifecycle owner
* evidence owner
* operational executor
* audit requirement

A transition is invalid if any of these fields are undefined.

No database mutation may bypass this matrix.

No event replay may bypass this matrix.

No operator override may bypass this matrix.

---

# 20. Terminal State Doctrine

Terminal states are lifecycle endpoints or controlled lifecycle exits.

## 20.1 Final Terminal States

* Subscriber Archived
* Service Terminated
* Invoice WrittenOff
* Provisioning RolledBack
* Session Invalidated
* NAS Detached
* MigrationCompleted
* ReconstructionCompleted

## 20.2 Recoverable Terminal States

* Payment Failed
* Payment Expired
* SuspensionCancelled
* RestorationCancelled
* MigrationRolledBack

## 20.3 Historically Reconstructable States

All terminal states must remain historically reconstructable.

Terminal does not mean deleted.

Terminal does not mean evidence removal.

Terminal does not mean audit closure without trace.

---

# 21. Derived State Doctrine

Derived states are computed from authoritative states.

Derived states must never become source of truth unless explicitly owned.

Examples:

* subscriber operational status derives from service + billing + enforcement
* service availability derives from provisioning + enforcement
* debt status derives from invoice + payment
* restoration eligibility derives from debt resolution + enforcement state
* NAS placement derives from placement assignment + attachment state

Derived state may be cached for UI or reporting only if it can be reconstructed from authoritative lifecycle states.

---

# 22. Replay-Safe State Progression

Replay reconstructs state.

Replay does not create authority.

Replay must validate:

* transition authority
* evidence existence
* source state validity
* target state validity
* ordering
* idempotency
* terminal state constraints
* cross-domain consistency

## 22.1 Duplicate Transition Replay

Duplicate transitions must resolve to the same lifecycle state without creating duplicate effects.

## 22.2 Stale Transition Replay

Stale transitions must be rejected or classified as historical evidence.

## 22.3 Missing Transition Recovery

Missing transitions must be reconstructed only from authoritative evidence.

## 22.4 Out-of-Order Transition Handling

Out-of-order transitions must not mutate current state until causal order is reconstructed.

---

# 23. Forbidden State Patterns

The following patterns are forbidden:

* state without authority owner
* transition without authority owner
* hidden state mutation
* database-driven authority
* UI-driven authority
* API-driven authority
* manual state override
* duplicate active states
* conflicting terminal states
* cross-domain authority leakage
* derived state used as source of truth
* replay creating new lifecycle authority
* auditless transition
* orphan lifecycle state
* direct infrastructure mutation of business state
* direct payment mutation of service state
* direct Telegram mutation of lifecycle state
* direct Radius mutation of subscriber authority
* direct MikroTik mutation of billing authority

---

# 24. Contract Enforcement Doctrine

Before database architecture, API contracts, event contracts, UI behavior, or implementation begins, every major domain entity must be mapped to this state machine contract.

Any future implementation artifact must prove:

* which entity state machine it affects
* which transition it requests
* which authority owns the transition
* which evidence validates the transition
* which audit record preserves the transition
* how replay handles the transition

If an implementation artifact cannot prove these conditions, it is architecturally invalid.

---

# 25. Final Doctrine

The ISP Billing + AAA Infrastructure shall treat lifecycle state as authority-owned truth.

State shall not be inferred from UI, database presence, infrastructure side effects, payment gateway callbacks, Telegram commands, Radius rows, or MikroTik runtime behavior.

State progression must be deterministic.

Transition authority must be explicit.

Historical reconstruction must be possible.

Replay must be safe.

Cross-domain consistency must be preserved.

No entity may move through lifecycle state without evidence, authority, and audit visibility.

