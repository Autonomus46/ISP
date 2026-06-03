PHASE 3 AUDIT — ENTITY_STATE_MACHINE_CONTRACT.md

Source audited: ENTITY_STATE_MACHINE_CONTRACT.md

STATUS

STATUS: WARNING
AUTHORITY CLARITY: WARNING
DUPLICATION RISK: MEDIUM
LAYER VIOLATION: MEDIUM
IMPLEMENTATION READINESS: WARNING
PATCH REQUIRED: YES

NOTES
1. Core doctrine is strong

The contract correctly states:

state is not database enum
state is not UI label
state is not API response field
database, API, Telegram, Radius, MikroTik cannot mutate authoritative state directly
replay reconstructs state but does not create authority
derived state must not become source of truth

This is valid.

2. Main defect: some transition owners conflict with prior authority doctrine

Example:

Subscriber Verified → Active

Authority Owner:

Billing Core + Provisioning Lifecycle

This violates the “exactly one authority owner” doctrine.

A transition may require multiple evidence sources, but the transition authority owner must be singular.

Correct conceptual separation:

Billing Core provides eligibility evidence
Provisioning Lifecycle provides provisioning evidence
one lifecycle authority approves Subscriber Active transition

Defect severity: HIGH
Patch required: YES

3. Backend Orchestrator appears too often as authority owner

Examples:

Subscriber Draft → Registered
Subscriber Registered → Verified
Subscriber Active → Terminated
Service Requested → ProvisioningPending
Operator command execution

Risk:

Backend Orchestrator may become business authority instead of execution/orchestration layer.

In previous architecture doctrine, Backend Orchestrator should coordinate execution, not own business truth unless explicitly designated by authority contract.

Defect severity: HIGH
Patch required: YES

4. State machine duplicates some domain lifecycle ownership

The contract defines full lifecycle state machines for many entities.

That is acceptable only if:

Domain Model owns entity meaning and lifecycle ownership
State Machine owns legal transitions only

But this contract says:

Entity states are authoritative lifecycle positions.

This is acceptable, but risky. It must not redefine entity ownership already defined in DOMAIN_MODEL_CONTRACT.md.

Risk is medium because several authority owners differ from domain ownership language.

Duplication risk: MEDIUM
Patch required: YES

5. Billing State Machine overlaps with Invoice State Machine

Billing state contains:

InvoiceGenerated
DebtOpen
DebtResolved
BillingSuspended

Invoice state contains:

Issued
Paid
Overdue
WrittenOff

Potential duplicate truth:

InvoiceGenerated vs Invoice Issued
DebtOpen vs Invoice Overdue
DebtResolved vs Invoice Paid

This can cause split financial truth.

Billing aggregate state may exist, but must be explicitly derived/coordinating, not duplicate invoice truth.

Duplication risk: HIGH
Patch required: YES

6. Suspension and Service state overlap

Suspension state:

SuspensionActive

Service state:

Restricted

Cross-domain rule says:

SuspensionActive implies Service Restricted

This is acceptable if Service Restricted is a derived or mirrored controlled state.

But if both are independently authoritative, duplicated restriction truth occurs.

Patch must clarify:

Suspension owns restriction decision.
Service Restricted is lifecycle reflection validated by suspension evidence, not independent enforcement authority.

Duplication risk: MEDIUM
Patch required: YES

7. Restoration and Service state overlap

Restoration state:

ServiceRestored

Service state:

Active

This creates similar duplication risk.

ServiceRestored sounds like a service lifecycle state, but it appears inside Restoration State Machine.

Better doctrine:

Restoration owns restoration workflow completion.
Service owns Active state.
Restoration completion may authorize Service transition, but must not itself define service truth.

Defect severity: MEDIUM
Patch required: YES

8. NAS Attachment and Migration overlap

NAS Attachment has:

MigrationPending
Migrating
Migrated

Migration State Machine has:

MigrationRequested
MigrationApproved
MigrationPrepared
MigrationExecuting
MigrationVerified
MigrationCompleted

This is duplicate lifecycle modeling.

One of them must own migration lifecycle. The other should reference migration state as evidence or derived placement status.

Current model risks two migration truths.

Duplication risk: HIGH
Patch required: YES

9. Reconciliation Engine appears as transition authority in operational lifecycle

Examples:

Provisioning Applied → Verified
Suspension EnforcementApplied → EnforcementVerified
Restoration EnforcementRemoved → RestorationVerified
MigrationExecuting → MigrationVerified

This is risky.

Reconciliation Engine should validate evidence, not necessarily own the business transition.

It can be evidence owner or verification executor, but transition authority should remain with the owning orchestration domain unless reconciliation has explicit authority contract.

Authority clarity: WARNING
Patch required: YES

10. Operator as authority owner is dangerous

Operator Command State Machine:

Drafted → Submitted: Authority Owner = Operator
Drafted → Cancelled: Authority Owner = Operator

This is acceptable only for command draft lifecycle, not business lifecycle.

Patch must clearly separate:

operator owns command intent lifecycle
operator never owns target domain transition

The doctrine says this, but the matrix should reinforce it.

Defect severity: LOW to MEDIUM
Patch required: YES

DUPLICATE DETECTION
DOMAIN_MODEL vs ENTITY_STATE_MACHINE

Risk: MEDIUM

State Machine does not fully duplicate Domain Model, but it risks redefining lifecycle authority through transition matrices.

Patch required to state:

Domain Model owns entity meaning and lifecycle ownership.
Entity State Machine owns legal transition graph only.
It cannot assign authority inconsistent with Domain Model.
CONTRACT VERDICT
CONTRACTS THAT SHOULD REMAIN UNCHANGED

None.

CONTRACTS REQUIRING PATCHES
ENTITY_STATE_MACHINE_CONTRACT.md
CONTRACTS WITH DUPLICATE RESPONSIBILITIES

Potential duplicates inside this contract:

Billing State Machine vs Invoice State Machine
Suspension State Machine vs Service Restricted state
Restoration State Machine vs Service Active/Restored state
NAS Attachment State Machine vs Migration State Machine
Reconciliation verification vs lifecycle transition authority
CONTRACTS THAT SHOULD BE MERGED

None.

CONTRACTS THAT SHOULD BE SPLIT

None.

But migration responsibilities must be separated more strictly.

MISSING CONTRACTS IF ANY

No new contract required.

IMPLEMENTATION BLOCKERS
Multi-owner transition authority exists.
Backend Orchestrator is assigned authority too broadly.
Billing and Invoice state truth may conflict.
NAS Attachment and Migration duplicate lifecycle states.
Reconciliation Engine may become hidden lifecycle authority.
Restoration State Machine contains service-like terminal state.
SCORE

PHASE 3 ARCHITECTURE SCORE: 76 / 100
PHASE 3 PRODUCTION READINESS SCORE: 68 / 100

FINAL VERDICT

PATCH REQUIRED BEFORE PHASE 4
