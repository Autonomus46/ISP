# PAYMENT_LIFECYCLE_CONTRACT.md

## 1. Contract Authority

This contract inherits all authority definitions from `SYSTEM_AUTHORITY_CONTRACT.md`.

This contract inherits all runtime definitions from `INFRASTRUCTURE_RUNTIME.md`.

This contract inherits all reconciliation definitions from `ACCOUNTING_RECONCILIATION_CONTRACT.md`.

This contract inherits all billing definitions from `BILLING_CORE_CONTRACT.md`.

This document defines the deterministic payment lifecycle architecture for the ISP Billing & AAA Infrastructure.

This contract governs:

* payment authority
* settlement evidence ownership
* payment-state transitions
* evidence validation
* payment-to-billing integration
* partial payment behavior
* overpayment behavior
* refund and reversal behavior
* replay-safe payment handling
* reconciliation integration

This contract does not define implementation, database schema, API endpoints, webhook logic, Midtrans logic, QRIS logic, UI behavior, or payment gateway configuration.

---

# 2. Payment Doctrine

## 2.1 Payment Definition

A payment is a financial settlement attempt or confirmed money movement associated with a subscriber financial obligation.

A payment may originate from:

* payment gateway transaction
* QRIS transaction
* bank transfer
* manual cashier receipt
* administrative financial adjustment
* refund or reversal event

A payment is not financial truth.

A payment is settlement evidence.

## 2.2 Core Doctrine

Billing Core owns financial truth.

Payment Lifecycle owns settlement evidence.

Reconciliation owns consistency verification.

Enforcement systems consume business state after Billing Core has updated financial state.

Payment may indicate that money has been received.

Payment may not decide that debt is resolved.

Only Billing Core may decide:

* invoice paid
* invoice partially paid
* debt cleared
* debt still outstanding
* customer eligible for restoration
* customer still suspended
* credit balance generated

## 2.3 Payment Authority Boundary

Payment Lifecycle is authoritative only for:

* payment request lifecycle
* settlement evidence intake
* payment evidence validation
* settlement status classification
* duplicate settlement prevention
* reversal evidence intake
* refund evidence tracking

Payment Lifecycle is not authoritative for:

* invoice creation
* debt creation
* debt mutation
* billing period ownership
* service eligibility
* suspension decision
* restoration decision
* subscriber financial truth

---

# 3. Payment Authority Model

## 3.1 Authority Ownership

| Domain                            | Authority                                               |
| --------------------------------- | ------------------------------------------------------- |
| Invoice ownership                 | Billing Core                                            |
| Debt ownership                    | Billing Core                                            |
| Payment request ownership         | Payment Lifecycle                                       |
| Payment evidence ownership        | Payment Lifecycle                                       |
| Settlement status ownership       | Payment Lifecycle                                       |
| Debt resolution ownership         | Billing Core                                            |
| Credit generation ownership       | Billing Core                                            |
| Refund evidence ownership         | Payment Lifecycle                                       |
| Refund financial effect ownership | Billing Core                                            |
| Consistency verification          | Reconciliation Engine                                   |
| Enforcement consumption           | Radius Policy / Provisioning / Suspension / Restoration |

## 3.2 Authoritative Payment Source

The authoritative payment source is the validated settlement evidence record owned by Payment Lifecycle.

Examples:

* confirmed payment gateway settlement
* verified bank transfer evidence
* verified manual receipt
* confirmed reversal notification
* verified refund settlement

Webhook payloads are not automatically authoritative.

Raw gateway notifications are evidence candidates only.

A payment source becomes authoritative only after deterministic validation.

## 3.3 Authoritative Settlement Source

The authoritative settlement source is the Payment Lifecycle settlement record after validation.

Settlement authority requires:

* unique payment reference
* source traceability
* amount identity
* currency identity
* payer or invoice linkage
* timestamp evidence
* provider or operator evidence
* duplicate protection
* replay-safe settlement lock

---

# 4. Payment Entity Model

## 4.1 Payment Request

A payment request is a generated instruction for a subscriber to pay a specific financial obligation.

Payment request may reference:

* invoice
* subscriber
* billing account
* amount requested
* expiry boundary
* payment channel
* external provider reference

Payment request does not resolve debt.

## 4.2 Payment

A payment is a lifecycle object representing a settlement attempt or settlement result.

Payment may be:

* initiated
* pending
* verified
* settled
* failed
* expired
* reversed
* closed

Payment references evidence.

Payment does not own debt.

## 4.3 Payment Evidence

Payment evidence is a validated record proving that a financial event occurred.

Evidence may include:

* provider transaction ID
* bank reference
* QRIS reference
* cashier receipt
* settlement timestamp
* settlement amount
* channel identity
* payer identity
* verification status

Evidence must be immutable once accepted.

## 4.4 Settlement

Settlement is the validated recognition that money movement occurred.

Settlement may be:

* pending
* successful
* failed
* expired
* reversed

Settlement does not equal invoice paid.

Settlement only becomes billing effect after Billing Core consumes it.

## 4.5 Refund

A refund is evidence that money has been returned to payer.

Refund evidence is owned by Payment Lifecycle.

Refund financial effect is owned by Billing Core.

## 4.6 Reversal

A reversal is evidence that a previously accepted settlement is invalidated, charged back, cancelled, or withdrawn.

Payment Lifecycle owns reversal evidence.

Billing Core owns financial correction.

## 4.7 Adjustment

An adjustment is an authorized financial correction.

Adjustment may affect billing truth only through Billing Core.

Payment Lifecycle may record adjustment evidence but may not mutate invoice debt directly.

---

# 5. Payment Lifecycle Model

## 5.1 Lifecycle Stages

The deterministic lifecycle is:

```text
INITIATED
→ PENDING
→ VERIFIED
→ SETTLED
→ CLOSED
```

Failure paths:

```text
INITIATED → EXPIRED → CLOSED
PENDING → FAILED → CLOSED
PENDING → EXPIRED → CLOSED
VERIFIED → FAILED → CLOSED
SETTLED → REVERSED → CLOSED
```

## 5.2 Initiation

Payment initiation creates a payment request.

Authority:

* Billing Core defines payable obligation.
* Payment Lifecycle creates payment request.
* Payment Lifecycle does not create invoice.
* Payment Lifecycle does not create debt.

## 5.3 Submission

Payment submission means an external or manual payment attempt has been received.

Submission creates evidence candidate.

Evidence candidate is not settlement truth.

## 5.4 Verification

Verification validates whether evidence candidate is acceptable.

Verification requires deterministic checks:

* reference uniqueness
* amount match or allocation compatibility
* source trust validation
* invoice or account linkage
* duplicate detection
* expiry check
* replay consistency
* provider status confirmation if applicable

## 5.5 Settlement

Settlement occurs only after evidence becomes valid.

Settlement creates settlement evidence.

Settlement does not mutate invoice status directly.

## 5.6 Resolution

Resolution is performed only by Billing Core.

Billing Core consumes settlement evidence and decides:

* invoice paid
* invoice partially paid
* invoice unpaid
* credit generated
* debt remains
* restoration eligibility changed

## 5.7 Cancellation

Cancellation ends unpaid or invalid payment attempt.

Cancellation may occur due to:

* expiry
* failed verification
* provider failure
* operator cancellation
* duplicate rejection

Cancellation does not affect debt.

---

# 6. Payment Evidence Model

## 6.1 Evidence Doctrine

Raw notification is not settlement truth.

Webhook is not settlement truth.

Payment gateway response is not settlement truth until validated.

Manual operator input is not settlement truth until verified.

Accepted evidence must be:

* traceable
* immutable
* source-bound
* duplicate-safe
* replay-safe
* auditable

## 6.2 Trusted Evidence Sources

Trusted evidence may originate from:

* payment gateway settlement confirmation
* payment gateway reconciliation report
* bank statement confirmation
* QRIS settlement report
* authorized cashier receipt
* administrative correction approved by financial authority

## 6.3 Evidence Validation Rules

Evidence is valid only if:

1. source is recognized
2. reference is unique
3. amount is deterministic
4. currency is deterministic
5. payment target is resolvable
6. timestamp is recorded
7. duplicate settlement is rejected
8. state transition is legal
9. reconciliation can verify the record later

---

# 7. Settlement Model

## 7.1 Pending Settlement

Pending settlement means payment is in progress but not confirmed.

Pending settlement may not resolve debt.

## 7.2 Successful Settlement

Successful settlement means validated money movement exists.

Successful settlement may be submitted to Billing Core.

Successful settlement does not automatically close invoice.

## 7.3 Failed Settlement

Failed settlement means payment attempt failed or evidence was rejected.

Failed settlement has no billing effect.

## 7.4 Expired Settlement

Expired settlement means payment request exceeded allowed payment window.

Expired settlement has no billing effect unless late settlement evidence later arrives.

Late evidence must be reconciled deterministically.

## 7.5 Reversed Settlement

Reversed settlement means previously accepted settlement is invalidated.

Reversal must trigger Billing Core correction workflow.

Payment Lifecycle records reversal evidence only.

---

# 8. Payment to Billing Integration

## 8.1 Integration Doctrine

Payment provides settlement evidence.

Billing consumes settlement evidence.

Billing determines financial effect.

Payment may never directly mutate:

* invoice status
* debt balance
* subscriber balance
* credit balance
* suspension status
* restoration status

## 8.2 Debt Resolution Flow

Deterministic flow:

```text
Payment Evidence Validated
→ Settlement Created
→ Settlement Event Emitted
→ Billing Core Consumes Settlement
→ Billing Core Allocates Payment
→ Billing Core Updates Financial Truth
→ Business State Updated
→ Enforcement Systems Consume Business State
```

## 8.3 Invoice Settlement Flow

Billing Core decides whether settlement applies to:

* one invoice
* multiple invoices
* oldest debt first
* explicitly referenced invoice
* customer credit balance

Allocation rule must be deterministic.

## 8.4 Partial Payment Handling

If payment amount is less than outstanding debt:

* Payment Lifecycle records full settlement amount.
* Billing Core applies partial allocation.
* Invoice remains partially paid or unpaid according to Billing rules.
* Service eligibility remains Billing-owned.

## 8.5 Overpayment Handling

If payment amount exceeds outstanding debt:

* Payment Lifecycle records actual settlement amount.
* Billing Core decides excess allocation.
* Billing Core may create credit balance.
* Payment Lifecycle may not create credit directly.

---

# 9. Payment State Machine

## 9.1 States

### INITIATED

Payment request exists.

No payment evidence confirmed.

### PENDING

Payment attempt exists.

Evidence candidate may exist.

Settlement not confirmed.

### VERIFIED

Evidence candidate passed validation.

Settlement record may be created.

### SETTLED

Settlement evidence accepted.

Ready for Billing Core consumption.

### FAILED

Payment attempt failed or evidence invalid.

No billing effect.

### EXPIRED

Payment request expired.

No billing effect unless later reconciled.

### REVERSED

Previously settled payment was reversed.

Requires Billing Core correction.

### CLOSED

Payment lifecycle is terminal.

No further mutation allowed except append-only audit records.

## 9.2 Legal Transitions

| From      | To       |
| --------- | -------- |
| INITIATED | PENDING  |
| INITIATED | EXPIRED  |
| INITIATED | FAILED   |
| PENDING   | VERIFIED |
| PENDING   | FAILED   |
| PENDING   | EXPIRED  |
| VERIFIED  | SETTLED  |
| VERIFIED  | FAILED   |
| SETTLED   | REVERSED |
| SETTLED   | CLOSED   |
| FAILED    | CLOSED   |
| EXPIRED   | CLOSED   |
| REVERSED  | CLOSED   |

## 9.3 Forbidden Transitions

Forbidden:

* INITIATED → SETTLED
* PENDING → SETTLED without VERIFIED
* FAILED → SETTLED
* EXPIRED → SETTLED without reconciliation recovery
* CLOSED → any mutable state
* REVERSED → SETTLED
* SETTLED → FAILED
* Payment state → Billing debt mutation directly

---

# 10. Partial Payment Model

## 10.1 Doctrine

Partial payment is valid settlement evidence with incomplete debt coverage.

Payment Lifecycle owns settlement amount.

Billing Core owns allocation and remaining debt.

## 10.2 Allocation Rules

Allocation must be deterministic.

Allowed allocation strategies:

* explicitly referenced invoice first
* oldest unpaid invoice first
* billing-priority order
* admin-approved allocation policy

Allocation strategy must be defined by Billing Core.

Payment Lifecycle must not choose allocation policy.

## 10.3 Replay-Safe Behavior

On replay:

* same settlement evidence must produce same settlement record
* same billing allocation input must produce same financial result
* duplicates must not increase paid amount
* out-of-order evidence must not corrupt debt state

---

# 11. Overpayment Model

## 11.1 Doctrine

Overpayment is settlement amount exceeding current payable debt.

Payment Lifecycle records actual money received.

Billing Core decides excess effect.

## 11.2 Credit Generation

Only Billing Core may generate credit.

Credit must be traceable to settlement evidence.

Credit must not be manually created inside Payment Lifecycle.

## 11.3 Ownership Boundary

Payment Lifecycle:

* records settlement amount
* validates evidence
* prevents duplicate settlement

Billing Core:

* applies settlement
* detects excess
* creates credit
* updates subscriber balance

---

# 12. Refund and Reversal Model

## 12.1 Refund Authority

Refund evidence is owned by Payment Lifecycle.

Refund financial consequence is owned by Billing Core.

Refund may not directly mutate invoice status.

## 12.2 Reversal Authority

Reversal evidence is owned by Payment Lifecycle.

Billing Core determines:

* debt reinstatement
* credit removal
* service eligibility change
* suspension requalification
* restoration rollback

## 12.3 Recovery Behavior

If reversal occurs after service restoration:

```text
Reversal Evidence Accepted
→ Billing Core Recalculates Financial Truth
→ Business State Updated
→ Suspension/Restoration Systems Consume Result
```

Payment Lifecycle may not suspend customer directly.

## 12.4 Reconciliation Behavior

Refunds and reversals must be reconciliation-visible.

Reconciliation Engine must be able to verify:

* original settlement
* reversal reference
* refund reference
* financial correction result
* audit chain integrity

---

# 13. Payment Event Model

## 13.1 Event Producers

Payment Lifecycle may produce:

* PaymentRequestCreated
* PaymentPending
* PaymentEvidenceReceived
* PaymentEvidenceVerified
* PaymentSettled
* PaymentFailed
* PaymentExpired
* PaymentReversed
* PaymentClosed

Billing Core may produce:

* InvoicePaymentApplied
* InvoicePartiallyPaid
* InvoicePaid
* CreditGenerated
* DebtResolved
* DebtRemaining
* BillingCorrectionApplied

Reconciliation Engine may produce:

* PaymentMismatchDetected
* SettlementMismatchDetected
* DuplicateSettlementDetected
* ReversalMismatchDetected
* PaymentRecoveryRequired

## 13.2 Event Ownership

Payment events describe settlement lifecycle.

Billing events describe financial truth.

Reconciliation events describe consistency status.

No event may cross ownership boundaries.

## 13.3 Event Consumers

Consumers may include:

* Billing Core
* Reconciliation Engine
* Notification system
* Audit system
* Restoration Orchestrator
* Suspension Orchestrator
* Telegram Operations

Consumers may not mutate Payment Lifecycle state unless explicitly authorized.

---

# 14. Replay-Safe Payment Model

## 14.1 Duplicate Notifications

Duplicate notifications must be idempotent.

Same payment reference must not create multiple settlements.

## 14.2 Duplicate Settlements

Duplicate settlement acceptance is forbidden.

A settlement identity must be unique across:

* provider transaction ID
* payment request ID
* invoice reference
* amount
* settlement timestamp
* channel identity

## 14.3 Duplicate Webhooks

Webhook duplication must produce same final state.

Webhook delivery order must not determine financial truth.

## 14.4 Delayed Events

Delayed events must be classified by current payment state.

Late successful payment after expiry must enter reconciliation recovery path.

It may not directly reopen closed payment state without authorized recovery.

## 14.5 Out-of-Order Events

Out-of-order events must be normalized into legal state transitions.

Illegal transitions must be rejected and audited.

## 14.6 Replay Determinism

Given the same ordered evidence set, the Payment Lifecycle must produce the same:

* payment state
* settlement state
* evidence status
* emitted payment events
* reconciliation outputs

---

# 15. Reconciliation Integration

## 15.1 Payment Verification

Reconciliation Engine verifies whether Payment Lifecycle state matches external financial reality.

It may compare:

* payment records
* settlement records
* gateway reports
* bank statements
* QRIS settlement reports
* refund reports
* reversal reports

## 15.2 Settlement Verification

Settlement must remain externally auditable.

Every settlement must trace to accepted evidence.

## 15.3 Correction Ownership

Reconciliation Engine detects inconsistency.

Payment Lifecycle corrects settlement evidence state when payment evidence is wrong.

Billing Core corrects financial truth when debt or allocation is wrong.

Reconciliation Engine does not directly mutate financial truth.

## 15.4 Recovery Boundaries

Recovery must preserve authority:

* Payment recovery fixes settlement evidence.
* Billing recovery fixes financial state.
* Reconciliation recovery verifies consistency.
* Enforcement recovery consumes business state only.

---

# 16. Operational Invariants

## 16.1 Immutable Payment Laws

1. Billing Core owns financial truth.
2. Payment Lifecycle owns settlement evidence.
3. Payment may not resolve debt directly.
4. Payment may not create invoice.
5. Payment may not create debt.
6. Payment may not mutate invoice status.
7. Webhook is not source of truth.
8. Raw payment notification is evidence candidate only.
9. Settlement requires validation.
10. Duplicate settlement acceptance is forbidden.
11. Reversal must be append-only and auditable.
12. Refund evidence must not mutate billing directly.
13. Overpayment credit must be created only by Billing Core.
14. Partial payment allocation must be owned by Billing Core.
15. Closed payment state is immutable.
16. Late evidence must enter reconciliation recovery.
17. Manual settlement override is forbidden.
18. All settlement effects must be traceable.
19. Payment state transitions must be legal.
20. Enforcement systems may not consume raw payment events as business truth.

---

# 17. Forbidden Payment Patterns

The following patterns are explicitly forbidden:

* payment resolving debt directly
* payment creating invoices
* payment creating debt
* payment mutating billing truth
* payment mutating subscriber balance directly
* payment changing service eligibility directly
* payment triggering suspension directly
* payment triggering restoration directly
* hidden settlement creation
* duplicate settlement acceptance
* manual settlement override
* circular authority ownership
* webhook becoming source of truth
* gateway status directly marking invoice paid
* QRIS callback directly restoring service
* bank transfer screenshot directly resolving debt
* refund directly mutating invoice
* reversal directly suspending subscriber
* notification system acting as payment authority
* Telegram operator command acting as settlement authority
* reconciliation engine silently changing payment state without audit

---

# 18. Final Contract Statement

`PAYMENT_LIFECYCLE_CONTRACT.md` defines Payment Lifecycle as the authoritative settlement-evidence system.

Payment Lifecycle validates money movement evidence.

Payment Lifecycle does not own debt.

Payment Lifecycle does not own invoice truth.

Payment Lifecycle does not own service eligibility.

Billing Core remains the only authority that may convert settlement evidence into financial-state change.

Reconciliation Engine remains the authority that verifies consistency across payment evidence, billing truth, and external financial reality.

This contract must be completed before designing:

* `SUSPENSION_ORCHESTRATION_CONTRACT.md`
* `RESTORATION_ORCHESTRATION_CONTRACT.md`
* `OPERATIONAL_AUDIT_CONTRACT.md`
* `EVENT_BUS_CONTRACT.md`
* `TELEGRAM_OPERATIONS_CONTRACT.md`
* `MULTI_NAS_SCALING_CONTRACT.md`

Payment is evidence.

Billing is truth.

Reconciliation is verification.

Enforcement is consumption.

No authority overlap is permitted.

