# SUSPENSION_ORCHESTRATION_CONTRACT.md

## 1. Contract Authority

This contract inherits all authority definitions from `SYSTEM_AUTHORITY_CONTRACT.md`.

This contract inherits all runtime definitions from `INFRASTRUCTURE_RUNTIME.md`.

This contract inherits all reconciliation definitions from `ACCOUNTING_RECONCILIATION_CONTRACT.md`.

This contract inherits all billing definitions from `BILLING_CORE_CONTRACT.md`.

This contract inherits all payment definitions from `PAYMENT_LIFECYCLE_CONTRACT.md`.

This contract defines deterministic suspension orchestration only.

It does not define restoration logic.

It does not define scheduler implementation.

It does not define Radius configuration.

It does not define MikroTik commands.

It does not define Event Bus implementation.

---

# 2. Suspension Doctrine

Suspension is the deterministic enforcement process that restricts subscriber service after an authoritative business state declares the subscriber ineligible for normal service.

Suspension is not billing.

Suspension is not payment validation.

Suspension is not debt calculation.

Suspension is not invoice mutation.

Suspension is not restoration eligibility.

Suspension exists only to convert authorized service-ineligibility into verified infrastructure enforcement.

The Suspension Orchestrator consumes business authority and produces enforcement state.

It may never create business authority.

---

# 3. Suspension Authority Model

## 3.1 Business-State Ownership

Billing Core owns:

* subscriber financial state
* invoice state
* debt state
* service eligibility
* suspension eligibility

Suspension Orchestrator may read suspension eligibility.

Suspension Orchestrator may not compute suspension eligibility independently.

## 3.2 Payment Authority

Payment Lifecycle owns:

* payment evidence
* settlement validity
* payment finality
* payment-state transitions

Suspension Orchestrator may not validate payment.

Suspension Orchestrator may not reverse suspension because of payment.

## 3.3 Enforcement Ownership

Suspension Orchestrator owns:

* suspension request lifecycle
* suspension decision materialization
* enforcement action planning
* enforcement execution coordination
* enforcement verification tracking
* failed enforcement recovery intent

## 3.4 Execution Ownership

FreeRADIUS executes policy decisions.

NAS devices execute network enforcement.

Suspension Orchestrator coordinates execution.

Suspension Orchestrator is not the physical enforcement endpoint.

## 3.5 Verification Ownership

Reconciliation Engine owns independent verification of infrastructure truth.

Suspension Orchestrator owns suspension-specific verification state.

Infrastructure truth always overrides assumed execution success.

---

# 4. Suspension Entity Model

## 4.1 Suspension Request

A Suspension Request is a deterministic request to restrict subscriber service.

It must reference:

* subscriber identity
* service identity
* authoritative eligibility source
* suspension reason
* requested enforcement scope
* request timestamp
* request origin
* idempotency identity

A Suspension Request is not enforcement proof.

## 4.2 Suspension Decision

A Suspension Decision is the accepted authorization to proceed with enforcement.

It must be derived only from Billing Core eligibility.

Suspension Decision may not derive from:

* webhook payload
* payment gateway state
* manual operator opinion
* NAS session state
* Radius table state

## 4.3 Suspension Action

A Suspension Action is a concrete enforcement intent generated from a valid Suspension Decision.

It may target:

* future authentication
* active sessions
* policy downgrade
* access isolation
* service restriction

It may not target billing state.

## 4.4 Suspension State

Suspension State represents lifecycle progress of the orchestration process.

Suspension State is operational state.

It is not subscriber financial state.

## 4.5 Suspension Evidence

Suspension Evidence records what enforcement was attempted, observed, verified, failed, or reconciled.

Evidence must be immutable.

## 4.6 Suspension Outcome

Suspension Outcome is the final operational result of a suspension lifecycle.

Allowed outcomes:

* enforced
* verified
* failed
* partially enforced
* closed after reconciliation

---

# 5. Enforcement Ownership Model

## 5.1 Billing Core

Billing Core owns suspension eligibility.

Billing Core does not execute suspension.

## 5.2 Radius Policy Engine

Radius Policy Engine owns deterministic policy translation.

It converts authorized subscriber state into Radius policy representation.

It does not decide debt.

It does not decide payment validity.

## 5.3 FreeRADIUS

FreeRADIUS executes authentication and authorization policy.

FreeRADIUS is not suspension authority.

## 5.4 NAS

NAS executes network session enforcement.

NAS is not suspension authority.

NAS state is observable infrastructure evidence only.

## 5.5 Suspension Orchestrator

Suspension Orchestrator owns enforcement orchestration.

It coordinates:

* policy update intent
* active-session handling intent
* enforcement verification intent
* failure recovery intent

---

# 6. Suspension Lifecycle Model

The deterministic lifecycle is:

1. Billing Core declares subscriber suspension-eligible.
2. Suspension Request is created.
3. Suspension Decision is materialized.
4. Enforcement Actions are generated.
5. Enforcement Actions are executed.
6. Execution Evidence is recorded.
7. Enforcement is verified.
8. Suspension lifecycle is closed.

Suspension may not be considered complete until verification exists or failure is explicitly recorded.

---

# 7. Suspension State Machine

Allowed states:

* `ELIGIBLE`
* `PENDING`
* `SCHEDULED`
* `EXECUTING`
* `ENFORCED`
* `FAILED`
* `VERIFIED`
* `CLOSED`

## 7.1 Legal Transitions

`ELIGIBLE -> PENDING`

`PENDING -> SCHEDULED`

`PENDING -> EXECUTING`

`SCHEDULED -> EXECUTING`

`EXECUTING -> ENFORCED`

`EXECUTING -> FAILED`

`ENFORCED -> VERIFIED`

`ENFORCED -> FAILED`

`FAILED -> EXECUTING`

`FAILED -> CLOSED`

`VERIFIED -> CLOSED`

## 7.2 Forbidden Transitions

`ELIGIBLE -> EXECUTING`

`ELIGIBLE -> VERIFIED`

`PENDING -> VERIFIED`

`SCHEDULED -> VERIFIED`

`EXECUTING -> CLOSED`

`FAILED -> VERIFIED`

`CLOSED -> EXECUTING`

`CLOSED -> ENFORCED`

`CLOSED -> VERIFIED`

A closed suspension is immutable.

Any new enforcement attempt requires a new lifecycle identity or a replay-safe retry under the same lifecycle rules.

---

# 8. Enforcement Decision Model

Enforcement Decisions must be generated from authorized suspension state only.

Decision scope must be explicit:

* subscriber-level
* service-level
* account-level

Subscriber-level enforcement affects all services owned by the subscriber only if Billing Core declares subscriber-wide ineligibility.

Service-level enforcement affects only the specific service.

Account-level enforcement must not be used unless the authority model explicitly permits it.

Enforcement scope may not be widened by Suspension Orchestrator.

---

# 9. Enforcement Execution Model

Suspension execution must cover two enforcement surfaces:

1. Future authentication
2. Existing active sessions

Future authentication is controlled through Radius policy state.

Existing active sessions are controlled through NAS enforcement actions.

Both surfaces must be treated independently.

A subscriber is not fully suspended if future authentication is blocked but stale active sessions remain alive.

A subscriber is not fully suspended if active sessions are terminated but future authentication still permits normal access.

---

# 10. Suspension Evidence Model

Authoritative suspension evidence includes:

* suspension request record
* suspension decision record
* generated enforcement action record
* Radius policy execution evidence
* NAS execution evidence
* active-session termination evidence
* verification result
* failure result
* reconciliation correction record

Evidence must be append-only.

Evidence must identify:

* actor
* system origin
* lifecycle identity
* target subscriber
* target service
* timestamp
* result
* error class if failed

---

# 11. Failed Enforcement Model

Failure classes:

* Radius policy failure
* NAS execution failure
* communication failure
* infrastructure outage
* partial enforcement
* verification mismatch
* stale session failure
* duplicate request conflict

Failure does not invalidate Billing Core authority.

Failure only means enforcement did not complete deterministically.

Suspension Orchestrator owns retry intent.

Reconciliation Engine owns independent visibility and correction detection.

Manual correction must produce evidence.

---

# 12. Partial Enforcement Model

Partial enforcement exists when one enforcement surface succeeds while another fails.

Examples:

* Radius policy updated but active session remains alive
* one NAS enforces suspension while another NAS does not
* subscriber blocked on future login but still online
* stale session exists after enforcement

Partial enforcement must never be reported as verified suspension.

Partial enforcement state must remain visible until reconciled, retried, or closed as failed.

---

# 13. Multi-NAS Suspension Model

In multi-NAS environments, enforcement state is authoritative only when all relevant NAS enforcement surfaces are accounted for.

Suspension Orchestrator must maintain per-NAS enforcement visibility.

A subscriber with sessions across multiple NAS devices requires distributed verification.

A single NAS success does not imply global suspension success.

Global suspension status must be derived from all required enforcement targets.

---

# 14. Replay-Safe Suspension Model

Suspension must be idempotent.

Duplicate requests with the same lifecycle identity must not create duplicate enforcement authority.

Duplicate execution attempts must be treated as retries, not new suspensions.

Out-of-order events must not advance state illegally.

Delayed enforcement evidence must be attached to the correct lifecycle identity.

Stale enforcement state must not overwrite newer verified state.

Replay must produce the same suspension state from the same input evidence.

---

# 15. Reconciliation Integration

Reconciliation Engine verifies whether infrastructure reality matches intended suspension state.

It may detect:

* ghost active sessions
* Radius policy drift
* NAS enforcement drift
* orphan enforcement state
* subscriber still online after suspension
* blocked subscriber without valid suspension authority

Correction ownership must follow authority boundaries.

Reconciliation may report drift.

Reconciliation may request correction.

Reconciliation may not create suspension eligibility.

---

# 16. Suspension Event Model

Suspension events are contract-level facts.

This contract does not define Event Bus implementation.

## 16.1 Event Producers

Billing Core may produce suspension eligibility events.

Suspension Orchestrator may produce suspension lifecycle events.

Radius Policy Engine may produce policy execution events.

NAS integration may produce enforcement execution events.

Reconciliation Engine may produce verification and drift events.

## 16.2 Event Consumers

Suspension Orchestrator consumes eligibility and execution feedback.

Reconciliation Engine consumes intended suspension state.

Operational systems may consume suspension status.

Notification systems may consume suspension events later.

## 16.3 Event Classes

Allowed event classes:

* suspension eligibility detected
* suspension request created
* suspension decision authorized
* enforcement action generated
* enforcement execution started
* enforcement execution succeeded
* enforcement execution failed
* partial enforcement detected
* suspension verified
* suspension closed

---

# 17. Operational Invariants

1. Billing Core owns suspension eligibility.
2. Suspension Orchestrator owns enforcement orchestration.
3. Payment Lifecycle owns settlement validity.
4. Radius Policy Engine owns policy translation.
5. FreeRADIUS executes policy.
6. NAS executes network enforcement.
7. Reconciliation Engine verifies infrastructure truth.
8. Suspension never owns debt.
9. Suspension never owns invoice state.
10. Suspension never owns payment validity.
11. Suspension cannot restore service.
12. Suspension cannot bypass Billing Core authority.
13. Suspension cannot trust NAS as source of truth.
14. Suspension cannot trust Radius as source of business truth.
15. Suspension is incomplete without evidence.
16. Suspension is incomplete without verification or recorded failure.
17. Partial enforcement is not verified enforcement.
18. Multi-NAS suspension requires per-NAS visibility.
19. Duplicate suspension requests must be idempotent.
20. Closed suspension lifecycle is immutable.

---

# 18. Forbidden Suspension Patterns

The following patterns are forbidden:

* suspension determining debt
* suspension determining payment validity
* suspension modifying invoice state
* suspension modifying billing truth
* suspension creating subscriber financial state
* NAS becoming source of suspension truth
* Radius becoming source of suspension truth
* manual enforcement bypass
* hidden suspension creation
* direct suspension from webhook
* direct suspension from payment gateway
* direct suspension from Midtrans event
* suspension without Billing Core eligibility
* suspension without lifecycle identity
* suspension without enforcement evidence
* suspension without verification
* suspension without audit trail
* circular enforcement authority
* restoration inside suspension lifecycle
* scheduler-owned suspension authority
* Telegram-owned suspension authority
* operator-owned debt override
* silent partial enforcement
* treating disconnect success as full suspension
* treating Radius update as full suspension
* treating NAS response as business truth

---

# 19. Final Contract Statement

`SUSPENSION_ORCHESTRATION_CONTRACT.md` defines the authoritative deterministic enforcement-orchestration contract for subscriber service restriction.

Suspension consumes Billing Core authority.

Suspension consumes Payment Lifecycle finality indirectly through Billing Core state.

Suspension coordinates Radius and NAS enforcement.

Suspension produces auditable enforcement state.

Suspension does not own financial truth.

Suspension does not own restoration.

Suspension does not own infrastructure truth.

Suspension is valid only when generated, executed, verified, reconciled, and audited according to this contract.

