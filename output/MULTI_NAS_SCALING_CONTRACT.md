MULTI_NAS_SCALING_CONTRACT.md
1. MULTI-NAS SCALING DOCTRINE
Purpose

Multi-NAS Scaling exists to govern deterministic horizontal infrastructure expansion while preserving authority boundaries, operational consistency, auditability, and replay-safe behavior.

Multi-NAS Scaling governs:

infrastructure expansion doctrine
NAS lifecycle governance
subscriber placement governance
infrastructure segmentation
operational continuity
migration governance
infrastructure consistency visibility
infrastructure reconstruction capability
Multi-NAS Scaling Is

A deterministic infrastructure expansion authority responsible for governing how additional NAS devices become operational participants inside the ISP infrastructure ecosystem.

Multi-NAS Scaling Is Not

Multi-NAS Scaling is not:

subscriber truth authority
billing authority
payment authority
provisioning authority
accounting authority
suspension authority
restoration authority
reconciliation authority
audit authority
event authority
operator authority
Scaling Doctrine

Infrastructure growth shall:

preserve authority ownership
preserve subscriber ownership continuity
preserve evidence continuity
preserve auditability
preserve reconstruction capability

Infrastructure expansion shall never alter authority definitions established by inherited contracts.

2. NAS AUTHORITY MODEL
NAS Ownership

A NAS is an infrastructure execution entity.

A NAS owns:

network access execution
session admission execution
policy execution
accounting generation execution

A NAS does not own:

subscriber truth
billing truth
payment truth
suspension truth
restoration truth
accounting truth
Subscriber Attachment Ownership

Subscriber attachment ownership belongs to Multi-NAS Scaling governance.

Physical execution occurs through NAS.

Authoritative ownership remains external to NAS.

Policy Enforcement Ownership

Policy authority:

Radius Policy Engine

Policy execution:

NAS

Accounting Ownership

Accounting Engine owns accounting truth.

NAS produces accounting evidence only.

Operational Ownership

Operational ownership remains governed by Infrastructure Runtime and inherited authority contracts.

3. NAS ENTITY MODEL
NAS

Infrastructure execution endpoint participating in subscriber access services.

Subscriber Attachment

Authoritative relationship between subscriber service and assigned NAS.

Service Location

Logical infrastructure location where subscriber service is delivered.

Infrastructure Segment

Operational boundary grouping infrastructure resources.

Operational Region

Administrative grouping of infrastructure segments.

Access Domain

Boundary where authentication and authorization execution occur.

Enforcement Domain

Boundary where service restrictions are executed.

Accounting Domain

Boundary where accounting evidence originates.

Ownership Responsibilities

Multi-NAS Scaling owns:

attachment governance
domain consistency
placement governance

Execution remains delegated to participating systems.

4. SUBSCRIBER PLACEMENT MODEL
Subscriber-to-NAS Association

Every subscriber shall possess exactly one authoritative attachment location.

A subscriber may interact with multiple infrastructure components.

A subscriber shall possess one authoritative NAS assignment.

Subscriber Movement

Movement shall occur only through governed migration processes.

Subscriber Relocation

Relocation shall preserve:

subscriber identity
service identity
billing identity
accounting lineage
enforcement lineage
Subscriber Reassignment

Reassignment shall create auditable migration evidence.

Ownership Continuity

Subscriber ownership remains unchanged regardless of NAS relocation.

NAS assignment does not transfer subscriber authority ownership.

5. NAS REGISTRATION MODEL
NAS Onboarding

New NAS enters infrastructure under controlled registration.

Registration Requirements
globally unique identity
operational ownership assignment
infrastructure classification
operational region assignment
NAS Activation

Activation occurs only after successful registration completion.

NAS Retirement

Retirement removes operational participation while preserving historical evidence.

NAS Replacement

Replacement shall preserve lineage between predecessor and successor infrastructure.

NAS Migration

Migration shall create reconstructable infrastructure history.

6. NAS LIFECYCLE MODEL
States
CREATED

NAS entity defined but not registered.

REGISTERED

NAS identity accepted into infrastructure authority model.

ACTIVE

NAS permitted to provide subscriber access services.

DEGRADED

NAS operational but exhibiting impaired behavior.

MAINTENANCE

NAS intentionally removed from normal operations.

RETIRED

NAS permanently removed from active participation.

Legal Transitions

CREATED → REGISTERED

REGISTERED → ACTIVE

ACTIVE → DEGRADED

DEGRADED → ACTIVE

ACTIVE → MAINTENANCE

MAINTENANCE → ACTIVE

ACTIVE → RETIRED

DEGRADED → RETIRED

MAINTENANCE → RETIRED

Forbidden Transitions

CREATED → ACTIVE

CREATED → RETIRED

REGISTERED → RETIRED

RETIRED → ACTIVE

RETIRED → DEGRADED

RETIRED → MAINTENANCE

Lifecycle Ownership

Multi-NAS Scaling owns lifecycle governance.

Operational Audit owns lifecycle evidence.

Event Bus owns lifecycle event distribution.

7. POLICY DISTRIBUTION MODEL
Policy Ownership

Policy truth belongs exclusively to Radius Policy Engine.

Policy Visibility

All participating NAS environments shall consume authoritative policy outputs.

Policy Consistency

No NAS may generate independent policy truth.

Policy Propagation Doctrine

Policy propagation shall preserve:

determinism
traceability
auditability
replay capability

Distribution mechanisms are outside scope.

8. ACCOUNTING CONSISTENCY MODEL
Ownership

Accounting Engine owns accounting truth.

Visibility

Accounting evidence shall remain attributable to originating NAS.

Integrity

Accounting records shall preserve:

NAS identity
subscriber identity
session identity
temporal lineage
Continuity

Migration or scaling shall not invalidate accounting lineage.

9. ENFORCEMENT CONSISTENCY MODEL
Suspension Consistency

Suspension authority remains owned by Suspension Orchestrator.

NAS executes enforcement.

Restoration Consistency

Restoration authority remains owned by Restoration Orchestrator.

NAS executes restoration.

Enforcement Consistency

Subscriber enforcement outcome shall remain consistent regardless of NAS location.

Policy Consistency

No NAS may independently determine enforcement state.

10. SUBSCRIBER MIGRATION MODEL
Planned Migration

Governed infrastructure relocation with prior authorization.

Unplanned Migration

Infrastructure relocation caused by operational events.

Infrastructure Migration

Movement between infrastructure segments.

Service Migration

Movement preserving subscriber service continuity.

Continuity Requirements

Migration shall preserve:

ownership continuity
authority continuity
accounting continuity
enforcement continuity
evidence continuity
11. MULTI-NAS FAILURE MODEL
NAS Outage

NAS becomes unavailable.

Subscriber truth remains unaffected.

NAS Isolation

NAS loses infrastructure connectivity.

Authority ownership remains external.

NAS Degradation

NAS continues operation with reduced capability.

NAS Replacement

Infrastructure replacement shall preserve lineage.

Infrastructure Partition

Partition shall not create alternative truth authorities.

Infrastructure Inconsistency

Detected inconsistencies become reconciliation subjects.

12. FAILOVER VISIBILITY MODEL
Visibility Doctrine

Infrastructure events shall remain observable.

Operational Visibility

Operators shall observe:

affected NAS
affected subscribers
affected services
infrastructure status
Subscriber Visibility

Subscriber impact shall remain reconstructable.

Failover Visibility

Failover existence must remain visible.

Hidden failover behavior is forbidden.

Implementation mechanisms remain outside scope.

13. INFRASTRUCTURE PARTITION MODEL
Isolated NAS

Operationally disconnected but still identifiable.

Disconnected NAS

Unable to participate in authoritative synchronization.

Stale NAS

Operating with outdated infrastructure knowledge.

Inconsistent NAS

Observed state differs from authoritative state.

Operational Doctrine

Partitioned infrastructure shall never become authoritative truth.

Partition visibility shall remain auditable.

14. RECONCILIATION INTEGRATION
Infrastructure Consistency Validation

Reconciliation validates:

NAS registration consistency
attachment consistency
lifecycle consistency
Subscriber Consistency Validation

Validate authoritative subscriber placement.

NAS Consistency Validation

Validate infrastructure state integrity.

Correction Visibility

All corrections shall remain visible and auditable.

Authority Boundaries

Reconciliation owns validation.

Multi-NAS Scaling owns scaling governance.

15. AUDIT INTEGRATION
NAS Evidence

Maintain NAS lifecycle evidence.

Subscriber Evidence

Maintain attachment and migration evidence.

Migration Evidence

Preserve migration lineage.

Enforcement Evidence

Preserve enforcement execution lineage.

Infrastructure Reconstruction

Historical infrastructure state shall remain reconstructable.

Ownership

Operational Audit owns forensic reconstruction.

16. EVENT BUS INTEGRATION
Event Types
NAS Lifecycle Events
created
registered
activated
degraded
maintenance
retired
Migration Events
initiated
completed
cancelled
Failure Events
outage
isolation
degradation
partition
Consistency Events
validation success
validation failure
correction completed
Ownership

Event Bus owns distribution.

Multi-NAS Scaling owns event production authority.

17. TELEGRAM OPERATIONS INTEGRATION
Operator Visibility

Operators may observe infrastructure state.

Infrastructure Visibility

Infrastructure conditions shall remain visible.

Migration Visibility

Migration actions shall remain visible.

Failure Visibility

Infrastructure failures shall remain visible.

Authority Boundary

Telegram Operations owns operator interaction.

Telegram Operations does not own:

infrastructure truth
subscriber truth
migration truth
accounting truth
18. SCALING STATE MACHINE
SCALING_PREPARATION

Infrastructure expansion planned.

SCALING_ACTIVATION

New infrastructure enters controlled operation.

SCALING_STABILIZATION

Operational consistency evaluation period.

SCALING_VERIFICATION

Infrastructure validation phase.

SCALING_COMPLETION

Expansion accepted as operationally complete.

Legal Transitions

SCALING_PREPARATION → SCALING_ACTIVATION

SCALING_ACTIVATION → SCALING_STABILIZATION

SCALING_STABILIZATION → SCALING_VERIFICATION

SCALING_VERIFICATION → SCALING_COMPLETION

Forbidden Transitions

SCALING_PREPARATION → SCALING_COMPLETION

SCALING_ACTIVATION → SCALING_COMPLETION

SCALING_ACTIVATION → SCALING_VERIFICATION

SCALING_COMPLETION → SCALING_ACTIVATION

19. REPLAY-SAFE SCALING MODEL
Duplicate NAS Registration

Duplicate registration shall not create multiple authoritative identities.

Duplicate Migrations

Repeated migration execution shall not create duplicate ownership outcomes.

Stale Migrations

Historical migration requests shall remain identifiable.

Historical Replay

Replay shall preserve original lineage.

Infrastructure Recovery

Recovery shall preserve infrastructure identity continuity.

Subscriber Recovery

Recovery shall preserve subscriber attachment lineage.

Replay Doctrine

Historical events may be replayed.

Historical authority outcomes shall remain identical.

20. OPERATIONAL INVARIANTS
A subscriber shall possess exactly one authoritative attachment location at any moment.
NAS identity shall be globally unique.
NAS shall never become subscriber truth authority.
NAS shall never become billing truth authority.
NAS shall never become payment truth authority.
NAS shall never become suspension truth authority.
NAS shall never become restoration truth authority.
Subscriber ownership shall remain reconstructable.
Infrastructure ownership shall remain reconstructable.
Migration shall preserve evidence lineage.
Accounting lineage shall survive scaling.
Enforcement lineage shall survive scaling.
Infrastructure growth shall not create authority ambiguity.
Infrastructure growth shall not create hidden state.
Infrastructure growth shall remain auditable.
Infrastructure changes shall remain traceable.
Lifecycle state shall remain reconstructable.
Subscriber placement shall remain deterministic.
Partitioned infrastructure shall not create alternative truth.
Reconciliation shall remain capable of validating infrastructure consistency.
Operational Audit shall remain capable of forensic reconstruction.
Event lineage shall remain reconstructable.
Subscriber migration shall remain attributable.
Infrastructure expansion shall preserve inherited authority contracts.
No infrastructure action shall violate authority ownership boundaries.
21. FORBIDDEN MULTI-NAS PATTERNS

The following patterns are permanently prohibited:

multiple authoritative subscriber locations
multiple authoritative NAS identities
NAS becoming source of truth
NAS creating billing truth
NAS creating payment truth
NAS creating provisioning truth
NAS creating suspension truth
NAS creating restoration truth
NAS creating accounting truth
hidden subscriber migration
silent infrastructure migration
untraceable infrastructure changes
infrastructure authority overlap
manual state synchronization
direct infrastructure truth creation
unowned NAS
orphan subscriber assignments
duplicate authoritative placement
replay without traceability
migration without evidence
failover without visibility
scaling without reconstruction capability
partition creating independent authority
lifecycle transitions outside state machine
infrastructure growth that bypasses auditability
infrastructure growth that bypasses reconciliation
infrastructure growth that violates inherited authority contracts
CONTRACT CONCLUSION

Multi-NAS Scaling is an infrastructure governance authority responsible for deterministic horizontal expansion of NAS environments while preserving authority ownership, subscriber placement integrity, accounting continuity, enforcement consistency, operational traceability, forensic reconstruction capability, and replay-safe infrastructure growth.

Under no circumstance shall infrastructure expansion alter, duplicate, replace, override, or compete with the authoritative domains defined by Billing, Payment, Provisioning, Radius Policy, Accounting, Suspension, Restoration, Reconciliation, Audit, Event Bus, or Telegram Operations contracts. Multi-NAS Scaling exists solely to ensure that growth from a single NAS to large-scale multi-region infrastructure remains deterministic, auditable, reconstructable, and authority-safe.
