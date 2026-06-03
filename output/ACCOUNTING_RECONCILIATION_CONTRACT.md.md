ACCOUNTING_RECONCILIATION_CONTRACT.md
1. Purpose

This contract defines the deterministic reconciliation architecture responsible for detecting, validating, recovering, and preserving infrastructure truth across all operational systems.

This contract exists to ensure:

evidence integrity
operational consistency
authority preservation
replay-safe recovery
deterministic conflict resolution

This document is not responsible for:

billing logic
payment processing
subscriber lifecycle
provisioning implementation

This contract governs truth validation only.

2. Reconciliation Doctrine
Definition

Reconciliation is the process of validating whether all infrastructure systems agree on the same operational reality.

Reconciliation is not synchronization.

Synchronization distributes state.

Reconciliation validates state.

Objective

Reconciliation exists to answer:

Is the system consistent?
If not, what is wrong?
Which authority is correct?
How can truth be restored safely?
Authority Principle

Reconciliation never creates truth.

Reconciliation only discovers truth.

Truth must already exist within an authoritative system.

Ownership

Reconciliation ownership belongs exclusively to:

Backend Orchestrator

No other component may execute reconciliation decisions.

3. Truth Source Hierarchy
Principle

Not all infrastructure components possess equal authority.

Truth precedence must be deterministic.

Authority Order

Level 1

Runtime Reality

Actual live operational state.

Level 2

MikroTik Enforcement State

Actual network enforcement.

Level 3

Accounting Evidence

Operational evidence stream.

Level 4

FreeRADIUS Policy State

Policy execution layer.

Level 5

PostgreSQL Stored State

Persisted operational representation.

Resolution Rule

Higher authority always wins.

Lower authority never overrides higher authority.

4. Accounting Consistency Model
Evidence Types
Start Evidence

Session creation evidence.

Interim Evidence

Session continuity evidence.

Stop Evidence

Session termination evidence.

Integrity Requirements

Evidence must be:

attributable
timestamped
traceable
immutable
auditable
Completeness Requirements

A session is considered complete when:

START exists

AND

STOP exists

Interim evidence is optional for completeness but mandatory for runtime visibility.

Ownership

Accounting Engine owns evidence production.

Reconciliation Engine owns evidence validation.

5. Session Reconciliation Model
Session Categories
Active Session

Exists consistently across:

Runtime
MikroTik
Accounting
Stale Session

Evidence exists.

Runtime no longer exists.

Termination not observed.

Ghost Session

Runtime exists.

Evidence absent.

Orphan Session

Accounting record exists.

Subscriber ownership absent.

Detection Principle

Detection must be deterministic.

No probabilistic inference permitted.

No timeout guessing permitted.

No operator assumptions permitted.

Recovery Ownership

Backend Orchestrator owns recovery execution.

6. Authentication Reconciliation Model
Validation Scope

Compare:

Subscriber State
Radius State
Runtime Authentication State
Consistency Requirement

All authentication decisions must be explainable through:

Subscriber State

→ Radius Policy

→ Authentication Result

Mismatch Categories
Policy Drift

Subscriber state differs from Radius policy.

Authentication Drift

Authentication result differs from expected policy.

State Drift

Runtime state differs from subscriber state.

Recovery Principle

Subscriber authority always wins.

Radius must never become source of truth.

7. Suspension Reconciliation Model
Validation Scope

Compare:

Billing State

Radius Policy

Network Enforcement

Expected State

Suspended Subscriber

Must produce:

Restricted Policy

AND

Restricted Enforcement

Drift Types
Policy Drift

Billing suspended.

Radius active.

Enforcement Drift

Radius suspended.

MikroTik active.

Dual Drift

Billing suspended.

Radius active.

MikroTik active.

Recovery Ownership

Backend Orchestrator.

8. Restoration Reconciliation Model
Validation Scope

Compare:

Payment State

Billing State

Radius State

Runtime State

Restoration Sequence
Payment Validated

↓

Subscriber Restored

↓

Radius Updated

↓

Enforcement Updated

↓

Runtime Verified

↓

Reconciliation Verified
Principle

Restoration completion requires verification.

State mutation alone is insufficient.

9. Reconciliation State Machine
CONSISTENT

All authorities agree.

INCONSISTENT

Conflict detected.

RECOVERING

Correction procedure executing.

VERIFIED

Correction completed.

Consistency proven.

FAILED

Recovery unable to establish consistency.

Operator intervention required.

Allowed Transitions

CONSISTENT

→ INCONSISTENT

INCONSISTENT

→ RECOVERING

→ FAILED

RECOVERING

→ VERIFIED

→ FAILED

VERIFIED

→ CONSISTENT

FAILED

→ RECOVERING

No other transitions permitted.

10. Reconciliation Event Model
Producer

Infrastructure components produce evidence.

Examples:

Accounting Engine
FreeRADIUS
MikroTik
Runtime Services
Authority Owner

Backend Orchestrator

Owns reconciliation decisions.

Consumers
Audit System
Billing System
Monitoring System
Operational Dashboard
Rule

Consumers may observe.

Consumers may not alter reconciliation outcomes.

11. Replay-Safe Reconciliation Model
Duplicate Evidence

Must be identifiable.

Must not create duplicate truth.

Delayed Evidence

Must remain processable.

Must preserve historical ordering.

Missing Evidence

Must be detectable.

Must not be silently ignored.

Out-of-Order Evidence

Must be reorderable.

Must not corrupt session state.

Replay Rule

Reconciliation replay must produce identical outcomes from identical evidence.

12. Operational Invariants
RI-001

Truth cannot be voted.

Truth must be authoritative.

RI-002

Reconciliation cannot create truth.

RI-003

Authority hierarchy is immutable.

RI-004

Evidence is immutable.

RI-005

Recovery must be auditable.

RI-006

Every inconsistency must be explainable.

RI-007

Every correction must be traceable.

RI-008

Verification is mandatory after recovery.

RI-009

Runtime reality has precedence over stored representations.

RI-010

Replay must reproduce identical reconciliation outcomes.

13. Forbidden Reconciliation Patterns
FRP-001

Manual Truth Repair

Operators manually editing state to force consistency.

Forbidden.

FRP-002

Hidden Reconciliation

Automatic corrections without audit trail.

Forbidden.

FRP-003

Authority Override

Lower authority replacing higher authority.

Forbidden.

FRP-004

Reconciliation Ownership Drift

Multiple systems performing reconciliation.

Forbidden.

FRP-005

Circular Reconciliation

System A validates System B while System B validates System A.

Forbidden.

FRP-006

Evidence Mutation

Changing accounting evidence after creation.

Forbidden.

FRP-007

Assumption-Based Recovery

Recovery based on guesses, timers, or operator beliefs.

Forbidden.

Final Contract Outcome

ACCOUNTING_RECONCILIATION_CONTRACT.md establishes a deterministic truth-recovery framework for the ISP AAA infrastructure.

After this document, the architecture can safely proceed into:

BILLING_CORE_CONTRACT.md
PAYMENT_LIFECYCLE_CONTRACT.md
SUSPENSION_ORCHESTRATION_CONTRACT.md
RESTORATION_ORCHESTRATION_CONTRACT.md
OPERATIONAL_AUDIT_CONTRACT.md
EVENT_BUS_CONTRACT.md
TELEGRAM_OPERATIONS_CONTRACT.md
MULTI_NAS_SCALING_CONTRACT.md

Those documents can now assume that infrastructure truth, conflict resolution, and recovery authority are formally defined and deterministic.
