PHASE 2 AUDIT — PROVISIONING_LIFECYCLE.md

Audited source:

AUDIT RESULT
Category	Result
STATUS	WARNING
AUTHORITY CLARITY	WARNING
DUPLICATION RISK	MEDIUM
LAYER VIOLATION	MEDIUM
IMPLEMENTATION READINESS	WARNING
PATCH REQUIRED	YES
EXECUTIVE SUMMARY

Provisioning is mostly correct in intent, but it contains several dangerous authority terms.

The contract correctly states:

Provisioning does not own business state.
Billing Core owns customer/service/subscriber lifecycle state.
Radius is generated state.
NAS is enforcement state.
Manual Radius/NAS mutation is forbidden.
Provisioning must be deterministic, replay-safe, and auditable.

However, it also gives Provisioning too much lifecycle authority in some places.

The main risk:

Provisioning becomes hidden lifecycle authority

instead of remaining:

Lifecycle execution / synchronization authority only
PRIMARY DEFECTS
PLF-01 — Provisioning Called “Authoritative Orchestration Process”

Severity: HIGH

The Purpose section says Provisioning is:

the authoritative orchestration process responsible for maintaining consistency between
Customer State
Service State
Credential State
Authorization State
Radius State
Network Enforcement State

This is too broad.

Provisioning may orchestrate infrastructure synchronization, but it must not be authoritative over customer state, service state, authorization state, or network enforcement state.

This wording risks making Provisioning a cross-domain authority.

Status: FAIL

PLF-02 — Billing Core Owns Too Much Lifecycle

Severity: MEDIUM

The authority matrix says Billing Core owns:

Customer Identity
Service Lifecycle
Package Assignment
Subscriber Status

This may be acceptable if SYSTEM_AUTHORITY_CONTRACT already defines Billing Core as the customer/service business authority.

But previous doctrine suggested subscriber identity may belong to a separate Subscriber Authority.

Risk:

Billing Core becomes overloaded as:

Billing + Customer Identity + Service Authority + Subscriber Lifecycle

Status: WARNING

Needs alignment with SYSTEM_AUTHORITY_CONTRACT.

PLF-03 — Provisioning Owns Radius Object Lifecycle

Severity: MEDIUM

Provisioning owns:

credential lifecycle
Radius object lifecycle
synchronization verification

Radius Policy Engine owns:

policy generation

This is mostly valid.

But the boundary between:

Radius object lifecycle

and

Radius policy state

must be explicit.

Provisioning may create/sync objects.

Provisioning must not decide policy meaning.

Status: PATCH REQUIRED

PLF-04 — Provisioning Transaction Claims Atomicity Across NAS Enforcement

Severity: HIGH

Section 11 says a provisioning transaction completes only when:

credential lifecycle complete
Radius lifecycle complete
authorization synchronization complete
enforcement synchronization complete
verification complete

and:

Provisioning is atomic at lifecycle level.

Problem:

NAS enforcement is not owned by Provisioning.

Suspension/Restoration own enforcement orchestration.

MikroTik/NAS executes enforcement.

Provisioning cannot safely claim atomic lifecycle authority over network enforcement.

Status: FAIL

PLF-05 — Rollback Restores Previous Lifecycle State

Severity: HIGH

Section 12 says rollback must:

restore previous lifecycle state

This is dangerous.

Provisioning must not restore business lifecycle state.

Only the business authority owner may change lifecycle state.

Provisioning rollback may restore infrastructure state only.

If Provisioning rolls back customer/service lifecycle state, it becomes business authority.

Status: FAIL

PLF-06 — Reconciliation Rule Grants Provisioning Correction Authority

Severity: MEDIUM

Section 16 says:

Infrastructure must be corrected.
Authority must never be rewritten.

This is good.

But it does not define who authorizes correction.

Given previous ACCOUNTING_RECONCILIATION defects, this must be tightened.

Provisioning can execute correction only for provisioning-owned infrastructure artifacts:

credentials
Radius objects
sync records

It must not correct Billing, Suspension, Restoration, or Payment state.

Status: PATCH REQUIRED

CROSS-CONTRACT ANALYSIS
Provisioning vs Billing

Question: Can Provisioning activate service without Billing authority?

Answer: mostly no.

The contract says business state originates from Billing Core and Provisioning never invents lifecycle transitions.

Result: PASS with warning.

Risk remains because rollback wording allows Provisioning to “restore previous lifecycle state.”

Billing vs Provisioning

Question: Can Billing bypass Provisioning?

The contract prevents active service without successful provisioning.

Result: PASS.

Good invariant.

Provisioning vs Radius Policy Engine

Question: Is there policy ownership overlap?

Yes, minor.

Provisioning owns Radius object lifecycle.

Radius Policy Engine owns policy generation.

This is valid only if:

Provisioning owns object existence/sync.
Policy Engine owns policy meaning.

Current wording does not fully lock this boundary.

Result: WARNING.

Provisioning vs Suspension / Restoration

Question: Can Provisioning suspend or restore?

The contract includes states:

CUSTOMER_SUSPENDED
CUSTOMER_RESTORED
SERVICE_SUSPENDED
SERVICE_RESTORED
PROFILE_SUSPENDED
PROFILE_RESTORED

This risks overlap with Suspension and Restoration contracts.

Provisioning may synchronize artifacts after authoritative suspension/restoration decisions.

Provisioning must not own suspension/restoration lifecycle.

Result: WARNING / PATCH REQUIRED.

Provisioning vs Reconciliation

Question: Can Provisioning become correction authority?

Yes, potentially.

Section 16 suggests infrastructure correction but not bounded enough.

Given previous reconciliation authority creep, this must be restricted.

Result: WARNING.

DUPLICATE RESPONSIBILITY FINDINGS
Provisioning lifecycle execution vs Suspension/Restoration orchestration
Provisioning contains suspended/restored states.
Suspension/Restoration own enforcement orchestration.
Risk: duplicated lifecycle ownership.
Provisioning rollback vs Billing lifecycle authority
Rollback says restore previous lifecycle state.
Business lifecycle belongs upstream.
Risk: Provisioning mutates business state.
Provisioning Radius lifecycle vs Radius Policy Engine
Provisioning owns Radius objects.
Policy Engine owns policy generation.
Must separate object sync from policy meaning.
MERGE RECOMMENDATIONS

None.

Provisioning should remain separate.

Do not merge it with Billing, Radius Policy, Suspension, or Restoration.

SPLIT RECOMMENDATIONS

None.

The current contract can remain one document.

The problem is boundary language, not scope size.

MISSING CONTRACTS

No new contract required.

However, this contract must explicitly inherit and obey:

Suspension Orchestration
Restoration Orchestration
Radius Policy Engine

because it currently interacts with all three.

IMPLEMENTATION BLOCKERS
Provisioning authority scope is too broad.
Rollback may mutate business lifecycle state.
Atomic provisioning across NAS enforcement is not valid unless bounded.
Suspension/restoration states may duplicate orchestration ownership.
Radius object lifecycle vs policy meaning boundary is underdefined.
CONTRACT SCORE
Area	Score
Authority Separation	78
Billing Boundary	84
Radius Boundary	82
Suspension/Restoration Boundary	72
Replay Safety	95
Rollback Safety	65
Implementation Readiness	76

Total: 79 / 100

CONTRACT VERDICT

PATCH REQUIRED

Provisioning is directionally correct, but it is not safe unchanged.

The dangerous pattern is:

Provisioning
=
Lifecycle Synchronization Authority

turning into:

Provisioning
=
Business Lifecycle / Enforcement Recovery Authority

That must be patched before Phase 3.

FINAL PHASE 2 STATUS
Contract	Result
BILLING_CORE_CONTRACT	PASS WITH PATCH
PAYMENT_LIFECYCLE_CONTRACT	PASS WITH PATCH
SUSPENSION_ORCHESTRATION_CONTRACT	PASS WITH MINOR PATCH
RESTORATION_ORCHESTRATION_CONTRACT	PATCH REQUIRED
ACCOUNTING_ENGINE	PATCH REQUIRED
ACCOUNTING_RECONCILIATION_CONTRACT	MAJOR PATCH REQUIRED
RADIUS_POLICY_ENGINE	PASS WITH MINOR PATCH
PROVISIONING_LIFECYCLE	PATCH REQUIRED
PHASE 2 VERDICT

MAJOR PATCH REQUIRED BEFORE PHASE 3

Main blockers:

Reconciliation authority creep.
Restoration authority ambiguity.
Undefined Session Authority boundary.
Provisioning lifecycle authority creep.
Rollback/correction authority ambiguity.
