# RESTORATION_ORCHESTRATION_CONTRACT.md

## 1. PURPOSE

The Restoration Orchestrator governs deterministic service reactivation across the ISP Billing and AAA Infrastructure.

The purpose of restoration is not to determine subscriber eligibility, payment validity, debt resolution, invoice state, or financial truth.

The purpose of restoration is to execute and verify removal of service restrictions after authoritative eligibility has been established by upstream systems.

Restoration is an execution authority.

Restoration is not a business authority.

Restoration consumes authority.

Restoration does not create authority.

---

# 2. RESTORATION DOCTRINE

## 2.1 Definition

Restoration is the deterministic orchestration process responsible for reactivating subscriber service access after authoritative restoration eligibility has been established.

Restoration is the controlled removal of enforcement restrictions.

Restoration is not account modification.

Restoration is not payment processing.

Restoration is not billing adjustment.

Restoration is not invoice manipulation.

---

## 2.2 Purpose

The objective of restoration is to:

* remove service restrictions
* restore policy eligibility
* restore network access capability
* verify restoration completion
* generate auditable restoration evidence

---

## 2.3 What Restoration Is Not

Restoration shall never:

* determine debt
* determine invoice validity
* determine payment validity
* determine settlement finality
* determine subscriber financial state
* modify Billing Core truth
* modify Payment Lifecycle truth

---

# 3. RESTORATION AUTHORITY MODEL

## 3.1 Authority Hierarchy

Billing Core:

Owns service eligibility.

Payment Lifecycle:

Owns settlement finality.

Suspension Orchestrator:

Owns restriction execution.

Restoration Orchestrator:

Owns restriction removal execution.

FreeRADIUS:

Executes policy instructions.

NAS:

Executes access enforcement.

---

## 3.2 Authorization Model

Restoration may only occur when:

Authoritative restoration eligibility exists.

No other component may independently authorize restoration.

---

## 3.3 Execution Authority

Restoration Orchestrator exclusively owns:

* restoration request processing
* restoration execution coordination
* restoration verification coordination
* restoration completion generation

---

## 3.4 Verification Authority

Restoration verification belongs to:

Restoration Orchestrator.

Verification authority may not be delegated to:

* NAS
* FreeRADIUS
* Payment Gateway
* Webhook sources

---

# 4. RESTORATION ENTITY MODEL

## 4.1 Restoration Request

Represents a request to initiate restoration processing.

Request does not imply authorization.

Request does not imply execution.

---

## 4.2 Restoration Decision

Represents authoritative determination that restoration may proceed.

---

## 4.3 Restoration Action

Represents execution of restoration operations.

---

## 4.4 Restoration State

Represents lifecycle progress of restoration processing.

---

## 4.5 Restoration Evidence

Represents authoritative proof of restoration execution.

---

## 4.6 Restoration Outcome

Represents final restoration result.

Possible outcomes:

* SUCCESS
* FAILURE
* PARTIAL_SUCCESS

---

# 5. RESTORATION OWNERSHIP MODEL

## Billing Core

Owns:

* subscriber eligibility
* service entitlement
* business-state truth

Does not execute restoration.

---

## Payment Lifecycle

Owns:

* settlement validation
* settlement finality

Does not execute restoration.

---

## Suspension Orchestrator

Owns:

* restriction application

Does not execute restoration.

---

## Radius Policy Engine

Owns:

* policy generation

Does not determine restoration eligibility.

---

## FreeRADIUS

Executes policy decisions.

Never becomes restoration authority.

---

## NAS

Executes network access behavior.

Never becomes restoration authority.

---

## Restoration Orchestrator

Owns:

* restoration execution
* restoration verification
* restoration completion generation

---

# 6. RESTORATION ELIGIBILITY MODEL

Restoration eligibility may only originate from authoritative business-state evaluation.

Eligibility sources may include:

* settlement completion
* administrative approval
* debt resolution
* business exception approval

Eligibility generation remains outside Restoration authority.

Restoration consumes eligibility.

---

# 7. RESTORATION LIFECYCLE MODEL

Lifecycle:

Eligibility Generated

↓

Restoration Requested

↓

Restoration Authorized

↓

Restoration Scheduled

↓

Restoration Executing

↓

Restoration Verified

↓

Restoration Completed

↓

Restoration Closed

Every stage must produce auditable evidence.

---

# 8. RESTORATION STATE MACHINE

States:

* ELIGIBLE
* PENDING
* SCHEDULED
* EXECUTING
* RESTORED
* FAILED
* VERIFIED
* CLOSED

---

## Legal Transitions

ELIGIBLE → PENDING

PENDING → SCHEDULED

SCHEDULED → EXECUTING

EXECUTING → RESTORED

EXECUTING → FAILED

RESTORED → VERIFIED

VERIFIED → CLOSED

---

## Forbidden Transitions

FAILED → VERIFIED

FAILED → CLOSED

ELIGIBLE → RESTORED

PENDING → VERIFIED

SCHEDULED → VERIFIED

EXECUTING → CLOSED

RESTORED → CLOSED

---

# 9. REACTIVATION DECISION MODEL

Restoration scope may exist at:

* subscriber level
* account level
* service level

Reactivation authority remains derived from Billing Core eligibility.

Restoration never creates reactivation authority.

---

# 10. RESTORATION EXECUTION MODEL

Execution responsibilities include:

* policy restoration
* enforcement removal
* authentication restoration
* access reactivation

Execution must remain deterministic.

Execution must remain idempotent.

Execution must remain replay-safe.

---

## Active Session Handling

Restoration must define deterministic behavior for:

* active sessions
* disconnected sessions
* reconnecting sessions

Behavior must remain consistent across all NAS devices.

---

# 11. RESTORATION EVIDENCE MODEL

Required evidence:

## Execution Evidence

Proof restoration action was attempted.

## Reactivation Evidence

Proof restrictions were removed.

## Verification Evidence

Proof restoration completed successfully.

## Failure Evidence

Proof restoration failed.

Evidence must be immutable.

Evidence must be auditable.

---

# 12. FAILED RESTORATION MODEL

Failure categories:

* Radius failure
* NAS failure
* communication failure
* infrastructure outage
* dependency failure

Failure ownership remains visible.

Failure recovery must be deterministic.

Failure states may not silently disappear.

---

# 13. PARTIAL RESTORATION MODEL

Partial restoration occurs when:

* one NAS restored
* another NAS not restored
* policy restored
* enforcement persists

Partial restoration is not restoration success.

Partial restoration requires reconciliation visibility.

---

# 14. MULTI-NAS RESTORATION MODEL

The system shall support:

* distributed enforcement
* distributed restoration
* cross-NAS verification

Authoritative restoration state must remain globally consistent.

Local NAS state may never become restoration truth.

---

# 15. REPLAY-SAFE RESTORATION MODEL

The architecture must tolerate:

* duplicate requests
* duplicate execution
* delayed processing
* stale requests
* out-of-order events

Repeated execution must not create inconsistent state.

Restoration must be idempotent.

---

# 16. RESTORATION CONSISTENCY MODEL

Consistency evaluation shall include:

* Billing state
* Payment state
* Radius state
* NAS state
* restoration state

Restoration is considered consistent only when all authoritative components agree on restoration outcome.

Inconsistent restoration state shall generate reconciliation visibility.

---

# 17. RECONCILIATION INTEGRATION

Reconciliation Engine owns:

* consistency validation
* drift detection
* state correction visibility

Restoration Orchestrator owns:

* restoration evidence generation
* restoration verification evidence

Correction authority remains governed by Reconciliation contracts.

---

# 18. RESTORATION EVENT MODEL

Restoration events may include:

* restoration requested
* restoration authorized
* restoration executing
* restoration restored
* restoration verified
* restoration failed
* restoration closed

This contract defines ownership only.

This contract does not define Event Bus implementation.

---

# 19. OPERATIONAL INVARIANTS

The following laws are immutable:

1. Restoration shall never determine debt.

2. Restoration shall never determine payment validity.

3. Restoration shall never determine invoice status.

4. Restoration shall never modify Billing truth.

5. Restoration shall never modify Payment truth.

6. Restoration consumes authority.

7. Restoration does not create authority.

8. Restoration requires verification.

9. Restoration requires evidence.

10. Restoration requires auditability.

11. Restoration requires reconciliation visibility.

12. NAS shall never become restoration truth.

13. FreeRADIUS shall never become restoration truth.

14. Billing Core remains service eligibility authority.

15. Payment Lifecycle remains settlement authority.

---

# 20. FORBIDDEN RESTORATION PATTERNS

Forbidden patterns include:

* restoration determining debt
* restoration determining payment validity
* restoration modifying invoice state
* restoration modifying billing truth
* restoration without settlement authority
* restoration without Billing Core eligibility
* restoration without verification
* restoration without evidence
* restoration without audit trail
* restoration bypassing suspension lifecycle
* restoration created directly by payment gateway
* restoration created directly by webhook
* restoration created by NAS
* restoration created by FreeRADIUS
* hidden restoration execution
* circular restoration authority
* manual restoration bypass
* unverified restoration completion

Any architecture containing these patterns violates this contract.

