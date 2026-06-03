PROVISIONING_LIFECYCLE.md
1. Purpose

This document defines the deterministic provisioning lifecycle contract for the ISP Billing & AAA Infrastructure.

Provisioning is not account creation.

Provisioning is the authoritative orchestration process responsible for maintaining consistency between:

Customer State
Service State
Credential State
Authorization State
Radius State
Network Enforcement State

The objective of provisioning is to guarantee lifecycle consistency across all dependent infrastructure systems while preventing operational drift, orphaned objects, hidden state, and synchronization ambiguity.

2. Provisioning Doctrine
Core Principle

Provisioning is an orchestration authority.

Provisioning does not own business state.

Provisioning does not own network enforcement.

Provisioning transforms authoritative lifecycle decisions into synchronized infrastructure state.

Provisioning Objectives

Provisioning must guarantee:

deterministic execution
replay-safe execution
idempotent execution
transactional behavior
lifecycle consistency
synchronization consistency
auditability
recoverability

Provisioning must never create:

orphan credentials
orphan Radius objects
ghost subscribers
duplicate services
authorization drift
synchronization drift
3. Provisioning Authority Matrix
Domain	Authority Owner	Consumers
Customer Identity	Billing Core	Provisioning
Service Lifecycle	Billing Core	Provisioning
Package Assignment	Billing Core	Provisioning
Subscriber Status	Billing Core	Provisioning
PPP Credentials	Provisioning Domain	Radius
Radius Objects	Provisioning Domain	FreeRADIUS
Authorization Profiles	Radius Policy Engine	Provisioning
Network Enforcement	NAS Infrastructure	Subscriber Sessions
Authority Rule

Provisioning may generate infrastructure state.

Provisioning may never generate business state.

Business state always originates from Billing Core.

4. Provisioning Ownership Model
Billing Core Owns
customer existence
service existence
service status
billing status
suspension status
restoration status
termination decision
Provisioning Owns
lifecycle execution
infrastructure synchronization
credential lifecycle
Radius object lifecycle
synchronization verification
Radius Owns

Nothing.

Radius is execution infrastructure.

Radius must never become authoritative.

NAS Owns

Nothing.

NAS is enforcement infrastructure.

NAS must never become authoritative.

5. Customer Lifecycle State Machine
Customer States
CUSTOMER_CREATED

→ CUSTOMER_PENDING_ACTIVATION

→ CUSTOMER_ACTIVE

→ CUSTOMER_SUSPENDED

→ CUSTOMER_RESTORED

→ CUSTOMER_TERMINATED
Rules

Customer state transitions originate only from Billing Core.

Provisioning executes synchronization.

Provisioning never invents lifecycle transitions.

6. Service Lifecycle State Machine
Service States
SERVICE_CREATED

→ SERVICE_PENDING_PROVISION

→ SERVICE_PROVISIONED

→ SERVICE_ACTIVE

→ SERVICE_SUSPENDED

→ SERVICE_RESTORED

→ SERVICE_TERMINATED
Invariants

Service activation requires:

valid customer
valid package
valid authorization assignment
successful provisioning completion

No active service may exist in unprovisioned state.

7. PPP Credential Lifecycle
Credential States
CREDENTIAL_NONE

→ CREDENTIAL_GENERATED

→ CREDENTIAL_ASSIGNED

→ CREDENTIAL_ACTIVE

→ CREDENTIAL_REVOKED

→ CREDENTIAL_ARCHIVED
Credential Doctrine

Credentials are infrastructure artifacts.

Credentials do not define service existence.

Service existence defines credential necessity.

Credential Invariants

Every active credential must belong to:

exactly one customer
exactly one service

No shared credentials.

No floating credentials.

No orphan credentials.

8. Radius Object Lifecycle
Radius States
RADIUS_NONE

→ RADIUS_GENERATED

→ RADIUS_SYNCHRONIZED

→ RADIUS_ACTIVE

→ RADIUS_DISABLED

→ RADIUS_REMOVED
Radius Doctrine

Radius state is generated state.

Radius state is never authoritative.

Radius objects must always be reproducible from authoritative lifecycle state.

Radius Rebuild Rule

Radius database loss must be recoverable from authoritative provisioning state.

Radius must never become a permanent source of truth.

9. Authorization Profile Lifecycle
Authorization States
PROFILE_ASSIGNED

→ PROFILE_ACTIVE

→ PROFILE_MODIFIED

→ PROFILE_SUSPENDED

→ PROFILE_RESTORED

→ PROFILE_REVOKED
Authorization Authority

Radius Policy Engine owns policy generation.

Provisioning executes policy synchronization.

FreeRADIUS executes policy delivery.

NAS executes enforcement.

10. Synchronization Doctrine
Allowed Synchronization
Billing Core
      ↓

Provisioning

      ↓

Radius Policy Engine

      ↓

FreeRADIUS

      ↓

NAS Enforcement
Forbidden Synchronization
Radius → Billing

Forbidden.

NAS → Billing

Forbidden.

Radius → Provisioning Authority

Forbidden.

Manual Radius Modification

Forbidden.

Manual NAS Subscriber Modification

Forbidden.

11. Provisioning Transaction Model
Transaction Boundary

Provisioning transaction begins when:

authoritative lifecycle event is accepted.

Provisioning transaction ends when:

all dependent infrastructure state reaches deterministic completion.

Transaction Completion Criteria

A provisioning transaction is complete only when:

credential lifecycle complete
Radius lifecycle complete
authorization synchronization complete
enforcement synchronization complete
verification complete
Partial Completion

Forbidden.

Provisioning is atomic at lifecycle level.

12. Rollback Doctrine
Rollback Trigger

Rollback occurs when:

synchronization fails
dependency unavailable
validation fails
authority conflict detected
Rollback Objective

Return infrastructure to last known valid state.

Rollback Rules

Rollback must:

remove partial objects
revoke partial credentials
invalidate incomplete Radius entries
restore previous lifecycle state
Rollback Invariant

No failed transaction may leave infrastructure artifacts behind.

13. Replay-Safe Provisioning Model
Replay Doctrine

Every provisioning operation must tolerate:

retries
duplicate requests
network interruptions
orchestrator restarts
synchronization retries
Duplicate Provision Request

Repeated activation request:

ACTIVE → ACTIVE

Must produce:

No lifecycle mutation.

No duplicate objects.

No duplicate credentials.

No duplicate Radius records.

Duplicate Suspension
SUSPENDED → SUSPENDED

Produces:

No additional mutation.

Duplicate Restoration
ACTIVE → ACTIVE

Produces:

No additional mutation.

Replay Invariant

Replaying the same lifecycle event must converge to identical infrastructure state.

14. Provisioning Failure Doctrine
PostgreSQL Unavailable

Provisioning halted.

No lifecycle mutation permitted.

No synchronization permitted.

FreeRADIUS Unavailable

Provisioning incomplete.

Transaction remains unresolved.

No lifecycle completion declared.

NAS Unavailable

Provisioning incomplete.

Enforcement synchronization unresolved.

Lifecycle completion prohibited.

Radius Synchronization Failure

Provisioning enters reconciliation state.

No completion acknowledgement allowed.

Partial Infrastructure Failure

Infrastructure consistency takes priority over service activation speed.

15. Drift Detection Doctrine
Provisioning Drift Definition

Drift exists when infrastructure state differs from authoritative lifecycle state.

Drift Categories
Service Drift

Service state mismatch.

Credential Drift

Credential inconsistency.

Radius Drift

Radius object mismatch.

Authorization Drift

Policy mismatch.

Enforcement Drift

NAS enforcement mismatch.

16. Reconciliation Doctrine
Reconciliation Objective

Restore infrastructure state to authoritative lifecycle state.

Authority During Reconciliation

Billing Core remains authoritative.

Provisioning remains executor.

Radius remains generated state.

NAS remains enforcement state.

Reconciliation Rule

Infrastructure must be corrected.

Authority must never be rewritten.

17. Termination Contract
Termination Doctrine

Termination is irreversible lifecycle finalization.

Termination Sequence
Service Termination

→ Credential Revocation

→ Radius Disablement

→ Radius Removal

→ Authorization Removal

→ Enforcement Cleanup

→ Audit Preservation

→ Lifecycle Finalization
Termination Invariants

After termination:

no active credential exists
no active Radius object exists
no active authorization exists
no active enforcement exists
Audit Preservation

Termination must not destroy:

lifecycle history
provisioning history
accounting history
synchronization history
reconciliation history
18. Operational Invariants

The following invariants must always remain true.

INV-001

Billing Core is authoritative.

INV-002

Provisioning executes lifecycle synchronization.

INV-003

Radius is generated state.

INV-004

NAS is enforcement state.

INV-005

No active service without successful provisioning.

INV-006

No orphan credentials.

INV-007

No orphan Radius objects.

INV-008

No partial provisioning completion.

INV-009

All provisioning operations are replay-safe.

INV-010

Infrastructure must converge to authoritative lifecycle state.

19. Forbidden Provisioning Patterns

The following patterns are permanently prohibited.

FP-001

Manual Radius subscriber creation.

FP-002

Manual Radius subscriber deletion.

FP-003

Manual NAS subscriber lifecycle modification.

FP-004

Radius as source of truth.

FP-005

NAS as source of truth.

FP-006

Partial provisioning success.

FP-007

Credential creation outside provisioning authority.

FP-008

Service activation before synchronization completion.

FP-009

Infrastructure state that cannot be reconstructed from authoritative lifecycle state.

FP-010

Provisioning operations that are not replay-safe.

Final Contract Statement

The provisioning system shall operate as a deterministic lifecycle orchestration authority responsible for transforming authoritative subscriber lifecycle decisions into synchronized infrastructure state while guaranteeing atomicity, replay safety, drift prevention, rollback capability, and operational consistency across Billing Core, Radius infrastructure, and network enforcement domains from 100 to 1000+ subscribers without architectural redesign.
