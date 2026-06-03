APPLICATION_CONTRACT.md
PROJECT CONTINUATION — ISP BILLING & AAA INFRASTRUCTURE
MODE

INSTITUTIONAL APPLICATION ARCHITECTURE DESIGN

TARGET DOCUMENT

APPLICATION_CONTRACT.md

DOCUMENT STATUS

Authoritative Application Contract.

This document governs application ownership, application authority boundaries, application composition, application lifecycle governance, module governance, runtime behavior, internal execution boundaries, cross-domain application coordination, replay-safe application behavior, deterministic application execution, and audit-safe application operation across the ISP Billing & AAA Infrastructure.

INHERITED AUTHORITY

THIS CONTRACT INHERITS ALL AUTHORITY DEFINITIONS FROM SYSTEM_AUTHORITY_CONTRACT.md.

THIS CONTRACT INHERITS ALL RUNTIME DEFINITIONS FROM INFRASTRUCTURE_RUNTIME.md.

THIS CONTRACT INHERITS ALL RECONCILIATION DEFINITIONS FROM ACCOUNTING_RECONCILIATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL BILLING DEFINITIONS FROM BILLING_CORE_CONTRACT.md.

THIS CONTRACT INHERITS ALL PAYMENT DEFINITIONS FROM PAYMENT_LIFECYCLE_CONTRACT.md.

THIS CONTRACT INHERITS ALL SUSPENSION DEFINITIONS FROM SUSPENSION_ORCHESTRATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL RESTORATION DEFINITIONS FROM RESTORATION_ORCHESTRATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL EVENT DEFINITIONS FROM EVENT_BUS_CONTRACT.md.

THIS CONTRACT INHERITS ALL AUDIT DEFINITIONS FROM OPERATIONAL_AUDIT_CONTRACT.md.

THIS CONTRACT INHERITS ALL AUTHORIZATION DEFINITIONS FROM AUTHORIZATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL IDENTITY DEFINITIONS FROM IDENTITY_AND_ACCESS_CONTRACT.md.

THIS CONTRACT INHERITS ALL DATABASE DEFINITIONS FROM DATABASE_ARCHITECTURE_CONTRACT.md.

THIS CONTRACT INHERITS ALL EVENT SCHEMA DEFINITIONS FROM EVENT_SCHEMA_CONTRACT.md.

THIS CONTRACT INHERITS ALL PROJECTION DEFINITIONS FROM PROJECTION_CONTRACT.md.

THIS CONTRACT INHERITS ALL READ MODEL DEFINITIONS FROM READ_MODEL_CONTRACT.md.

THIS CONTRACT INHERITS ALL QUERY DEFINITIONS FROM QUERY_CONTRACT.md.

THIS CONTRACT INHERITS ALL API DEFINITIONS FROM API_CONTRACT.md.

THIS CONTRACT INHERITS ALL SEARCH DEFINITIONS FROM SEARCH_CONTRACT.md.

THIS CONTRACT INHERITS ALL REPORTING DEFINITIONS FROM REPORTING_CONTRACT.md.

THIS CONTRACT INHERITS ALL ANALYTICS DEFINITIONS FROM ANALYTICS_CONTRACT.md.

THIS CONTRACT INHERITS ALL DASHBOARD DEFINITIONS FROM DASHBOARD_CONTRACT.md.

THIS CONTRACT INHERITS ALL PORTAL DEFINITIONS FROM PORTAL_CONTRACT.md.

THIS CONTRACT INHERITS ALL WORKFLOW DEFINITIONS FROM WORKFLOW_CONTRACT.md.

THIS CONTRACT INHERITS ALL JOB DEFINITIONS FROM JOB_ORCHESTRATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL INTEGRATION DEFINITIONS FROM INTEGRATION_CONTRACT.md.

1. APPLICATION DOCTRINE
1.1 Application Purpose

The application exists to compose the ISP Billing & AAA Infrastructure into an executable operational system.

The application coordinates modules, domains, workflows, jobs, APIs, integrations, read models, projections, authorization, identity, audit, and runtime behavior without replacing the authority of any domain.

The application is a composition boundary.

The application is not the source of truth for all state.

The application must preserve all inherited authority boundaries.

1.2 Core Application Law

The application must never create authority by composition.

If Billing Core owns billing truth, the application must not override it.

If Payment Lifecycle owns payment truth, the application must not override it.

If Suspension Orchestration owns suspension truth, the application must not override it.

If Restoration Orchestration owns restoration truth, the application must not override it.

If Integration owns boundary execution, the application must not bypass it.

If Workflow owns orchestration, the application must not replace it with hidden procedural logic.

1.3 Application Authority Principle

Every application behavior must be traceable to:

authoritative domain,
module ownership,
actor identity,
authorization decision,
execution path,
state transition,
event emission,
audit evidence,
replay behavior,
reconstruction source.

Application behavior without traceability is invalid.

2. APPLICATION OWNERSHIP
2.1 Application Ownership Definition

Application ownership governs how system modules are composed, loaded, invoked, coordinated, exposed, and operated.

Application ownership includes:

module composition,
runtime entrypoints,
cross-domain coordination rules,
application-level validation,
application lifecycle,
module dependency rules,
execution safety,
audit preservation,
replay behavior,
operational readiness.
2.2 Ownership Separation

Application ownership does not own:

subscriber truth,
billing truth,
payment truth,
suspension truth,
restoration truth,
accounting truth,
Radius policy truth,
event truth,
workflow truth,
job truth,
API truth,
portal truth,
integration truth.

The application composes these domains.

The application must not absorb their authority.

2.3 Application Registry

Every application module must be registered.

The application registry must define:

module name,
module owner,
module responsibility,
authoritative domain,
allowed dependencies,
prohibited dependencies,
exposed capabilities,
required authorization,
emitted events,
consumed events,
workflow participation,
job participation,
integration participation,
read model usage,
audit requirements,
replay behavior.

Unregistered modules are prohibited.

3. APPLICATION AUTHORITY MODEL
3.1 Authority Preservation

The application must preserve domain authority during all execution.

Composition must not merge authority.

Shared runtime must not imply shared ownership.

Shared database access must not imply shared state authority.

Shared API surface must not imply shared business authority.

Shared operator interface must not imply shared mutation authority.

3.2 Authority Delegation

Authority may be delegated only through explicit inherited contracts.

Delegation must preserve:

source authority,
execution boundary,
allowed operation,
authorization context,
audit context,
idempotency context,
replay classification.
3.3 Prohibited Authority Leakage

The application must not allow:

Portal to mutate billing directly.
API to bypass authorization.
Workflow to invent billing state.
Job to create new business intent during retry.
Integration to finalize payment state.
Read Model to become write authority.
Dashboard to trigger hidden mutations.
Search to become business state.
Reporting to become reconciliation authority.
Telegram command to bypass workflow and authorization.
MikroTik state to override subscriber truth.
FreeRADIUS response to override billing truth.
4. APPLICATION COMPOSITION GOVERNANCE
4.1 Composition Doctrine

Application composition must be explicit, deterministic, auditable, and authority-safe.

Modules may be composed only when their dependencies are known and valid.

No module may depend on another module in a way that violates ownership boundaries.

4.2 Required Application Modules

The application may compose the following governed modules:

Billing Core Module.
Payment Lifecycle Module.
Subscriber Module.
Radius Policy Module.
Accounting Module.
Reconciliation Module.
Suspension Module.
Restoration Module.
Audit Module.
Event Bus Module.
Projection Module.
Read Model Module.
Query Module.
API Module.
Authorization Module.
Identity Module.
Search Module.
Reporting Module.
Analytics Module.
Dashboard Module.
Portal Module.
Workflow Module.
Job Orchestration Module.
Integration Module.
4.3 Module Dependency Rules

A module may depend on another module only through governed interfaces.

Direct hidden dependency is prohibited.

Direct database coupling across authority boundaries is prohibited.

Direct state mutation across module boundaries is prohibited.

Direct external system access outside Integration governance is prohibited.

Direct user interaction outside API and Portal governance is prohibited.

4.4 Application Boundary

The application boundary defines the executable system.

Inside the boundary, modules must follow authority contracts.

Outside the boundary, systems must interact through governed APIs, integrations, events, or operator channels.

5. APPLICATION LIFECYCLE
5.1 Application Initialization

Application initialization must verify:

configuration validity,
module registration,
dependency availability,
database readiness,
event bus readiness,
authorization readiness,
identity readiness,
audit readiness,
integration registry readiness,
runtime safety.

The application must not accept mutation traffic before critical readiness is confirmed.

5.2 Application Activation

Application activation may occur only after authoritative modules are available or explicitly degraded according to runtime governance.

Activation must preserve:

authority boundaries,
execution safety,
audit capture,
authorization enforcement,
replay classification,
integration safety.
5.3 Application Execution

Application execution must be deterministic.

Given the same authoritative state, same command, same identity, same authorization, and same replay mode, the application must resolve the same internal execution path.

5.4 Application Degradation

The application may enter degraded mode when non-critical modules are unavailable.

Degraded mode must not allow unsafe mutation.

Degraded mode must not bypass authorization.

Degraded mode must not hide audit gaps.

Degraded mode must not execute integrations without valid authority.

5.5 Application Suspension

The application or individual modules may be suspended when:

authority safety is compromised,
audit safety is unavailable,
authorization cannot be enforced,
identity cannot be verified,
database consistency is unsafe,
integration trust boundary is compromised,
replay safety cannot be guaranteed.
5.6 Application Restoration

Application restoration must verify:

authoritative state integrity,
pending workflow reconstruction,
pending job reconstruction,
integration recovery status,
event bus continuity,
projection rebuild requirements,
read model freshness,
audit continuity,
authorization validity,
runtime consistency.
5.7 Application Shutdown

Application shutdown must preserve:

in-flight execution evidence,
pending job state,
workflow state,
integration execution state,
audit records,
event publication guarantees,
recovery markers.

Shutdown must not create orphan execution.

6. APPLICATION RUNTIME GOVERNANCE
6.1 Runtime Doctrine

The application runtime executes governed modules.

Runtime does not own business truth.

Runtime must enforce readiness, dependency, lifecycle, and safety rules.

6.2 Runtime Entry Points

All application entry points must be governed.

Allowed entry point categories include:

API requests,
Portal-originated requests,
scheduled jobs,
workflow continuations,
event consumers,
integration callbacks,
administrative commands,
reconciliation tasks,
recovery tasks.

Hidden entry points are prohibited.

6.3 Runtime Context

Every runtime execution must carry:

actor identity or system identity,
authorization context,
correlation reference,
causation reference,
authority context,
execution mode,
replay classification,
audit reference.
6.4 Runtime Isolation

Runtime must isolate:

customer context,
subscriber context,
payment context,
invoice context,
NAS context,
workflow context,
job context,
integration context.

Runtime isolation failure is an application defect.

7. APPLICATION MODULE GOVERNANCE
7.1 Billing Module

The Billing Module must preserve Billing Core authority.

It may expose billing capabilities only through governed API, workflow, job, event, projection, and reporting boundaries.

It must not allow external systems to mutate billing state.

7.2 Payment Module

The Payment Module must preserve Payment Lifecycle authority.

It may coordinate provider evidence, settlement validation, payment state, and restoration eligibility according to inherited payment rules.

It must not allow Midtrans callback to directly restore subscriber service.

7.3 Subscriber Module

The Subscriber Module must preserve subscriber authority inherited from System Authority.

It must not allow Radius, MikroTik, Portal, or Telegram to mutate subscriber truth outside governed workflows and APIs.

7.4 Radius Policy Module

The Radius Policy Module owns deterministic policy translation.

It must not own billing truth.

It must not own payment truth.

It must not own subscriber business truth.

7.5 Accounting Module

The Accounting Module owns accounting evidence handling according to inherited accounting rules.

It must not finalize billing or payment truth without authoritative reconciliation.

7.6 Reconciliation Module

The Reconciliation Module owns recovery of truth from inconsistent evidence.

It must operate through explicit reconciliation authority.

7.7 Suspension Module

The Suspension Module owns suspension orchestration.

It must decide suspension execution only from authoritative eligibility.

7.8 Restoration Module

The Restoration Module owns restoration orchestration.

It must decide restoration execution only from authoritative payment and eligibility evidence.

7.9 Audit Module

The Audit Module owns audit evidence preservation.

It must receive application evidence without altering original business meaning.

7.10 Event Bus Module

The Event Bus Module owns event propagation governance.

Application modules must publish and consume events according to event authority.

7.11 Projection Module

The Projection Module owns event-to-read transformation.

It must not initiate business mutation.

7.12 Read Model Module

The Read Model Module owns operational visibility state.

It must not become write authority.

7.13 Query Module

The Query Module owns query execution boundaries.

It must enforce visibility, authorization, and consistency rules.

7.14 API Module

The API Module owns request surface governance.

It must validate input, identity, authorization, and command eligibility before invoking authoritative modules.

7.15 Authorization Module

The Authorization Module owns permission decision governance.

No application module may self-authorize.

7.16 Identity Module

The Identity Module owns identity, credential, and session governance.

No application module may invent identity context.

7.17 Search Module

The Search Module owns search visibility.

It must not become authoritative state.

7.18 Reporting Module

The Reporting Module owns report generation.

It must not mutate business state.

7.19 Analytics Module

The Analytics Module owns analytical interpretation.

It must not become operational authority.

7.20 Dashboard Module

The Dashboard Module owns dashboard composition.

It must not mutate authoritative state unless routed through governed commands.

7.21 Portal Module

The Portal Module owns user-facing interaction.

It must not bypass API, Authorization, Workflow, Job, or Integration governance.

7.22 Workflow Module

The Workflow Module owns orchestration behavior.

It must not replace domain authority.

7.23 Job Module

The Job Module owns asynchronous and scheduled execution.

It must not create new authority during retry or recovery.

7.24 Integration Module

The Integration Module owns system boundary execution.

It must transport authority without becoming authority.

8. CROSS-DOMAIN EXECUTION GOVERNANCE
8.1 Cross-Domain Doctrine

Cross-domain execution must be explicit.

No domain may call another domain in a way that hides authority, bypasses audit, or creates circular ownership.

8.2 Command Routing

Commands must be routed to the authoritative domain.

Application routing must not reinterpret command meaning.

8.3 Query Routing

Queries must be routed to governed query or read model boundaries.

Queries must not mutate state.

8.4 Event Routing

Events must be routed through Event Bus governance.

Direct event bypass is prohibited.

8.5 Integration Routing

External and internal system boundary interactions must be routed through Integration governance.

Direct module-to-external-system access is prohibited unless explicitly governed.

9. APPLICATION STATE GOVERNANCE
9.1 State Doctrine

The application must not create hidden global state.

All persistent state must belong to an authoritative domain, read model, projection, audit record, workflow state, job state, integration state, or runtime state defined by inherited contracts.

9.2 Prohibited State

The application must not maintain:

hidden billing state,
hidden payment state,
hidden subscriber state,
hidden suspension state,
hidden restoration state,
hidden authorization state,
hidden Radius state,
hidden MikroTik state,
undocumented operator state,
memory-only business truth.
9.3 Temporary Runtime State

Temporary runtime state may exist only for execution coordination.

Temporary runtime state must not become business truth.

Temporary runtime state must be recoverable or safely discardable.

9.4 State Reconstruction

Application state must be reconstructable from authoritative records, events, audit evidence, workflow records, job records, integration records, projections, and read models.

10. APPLICATION CONSISTENCY GOVERNANCE
10.1 Consistency Doctrine

Application consistency means that composed module behavior does not violate authoritative domain truth.

Consistency must be enforced at boundaries.

10.2 Consistency Rules

The application must ensure:

payment success precedes restoration eligibility,
suspension decision precedes enforcement execution,
billing state precedes invoice visibility,
policy translation precedes Radius policy execution,
provisioning precedes service activation,
audit capture accompanies mutation,
authorization precedes command execution,
integration evidence does not override authoritative state.
10.3 Consistency Failure

Consistency failure must result in safe rejection, recovery, reconciliation, or operator-safe review.

Consistency failure must not be hidden.

11. APPLICATION AUTHORIZATION GOVERNANCE
11.1 Authorization Requirement

Every application mutation requires authorization.

No module may assume authorization from upstream routing alone.

11.2 Authorization Propagation

Authorization context must be propagated across:

API,
workflow,
job,
integration,
event handler,
administrative command,
portal action.
11.3 Authorization Loss

If authorization context is lost, execution must stop.

11.4 Authorization Visibility

Application visibility must be filtered by authorization rules.

Operator and administrator visibility must not expose unauthorized customer, payment, credential, or operational data.

12. APPLICATION IDENTITY GOVERNANCE
12.1 Identity Requirement

Every application execution must identify its actor.

Actor may be:

subscriber,
administrator,
operator,
system component,
scheduled job,
workflow engine,
integration callback,
recovery process.
12.2 System Identity

System identity must be explicit.

System identity must not be confused with human operator identity.

12.3 Identity Propagation

Identity context must propagate across all execution boundaries.

Identity loss invalidates mutation execution.

13. APPLICATION AUDIT GOVERNANCE
13.1 Audit Doctrine

Every application mutation must produce audit evidence.

Application audit must preserve:

actor,
authority,
authorization,
command,
state before,
state after,
event emitted,
integration executed,
workflow or job context,
correlation and causation.
13.2 Audit Coverage

Audit coverage must include:

API commands,
Portal actions,
Workflow steps,
Job executions,
Integration calls,
Payment evidence processing,
Suspension execution,
Restoration execution,
Authorization decisions,
Administrative actions.
13.3 Audit Failure

If audit capture fails for a mutation, the mutation must not proceed unless the inherited authority contract explicitly defines emergency-safe behavior.

14. APPLICATION EVENT GOVERNANCE
14.1 Event Publication

Application modules may publish events only according to event ownership.

The application must not publish events on behalf of a domain unless authorized by that domain contract.

14.2 Event Consumption

Application modules may consume events only through registered consumers.

Event consumption must define replay behavior.

14.3 Event Replay

Event replay must not trigger external side effects unless classified as recovery execution and authorized.

14.4 Event Determinism

Given the same event stream and same projection rules, application visibility must be reconstructable.

15. APPLICATION WORKFLOW GOVERNANCE
15.1 Workflow Participation

Application modules may participate in workflows only through governed workflow steps.

Workflow participation must preserve module authority.

15.2 Workflow Boundaries

Workflow may coordinate modules.

Workflow must not own domain truth.

Application must not hide workflow behavior inside ad-hoc module calls where workflow governance is required.

15.3 Workflow Recovery

Application must allow workflow reconstruction after interruption.

Interrupted workflows must not be replaced by undocumented manual execution.

16. APPLICATION JOB GOVERNANCE
16.1 Job Participation

Application modules may participate in jobs only through governed job execution.

Job execution must preserve authority, idempotency, and auditability.

16.2 Scheduled Execution

Scheduled execution must not create new business intent unless the job contract authorizes it.

16.3 Job Recovery

Application must reconstruct interrupted jobs before retrying.

Job retry must not duplicate external side effects.

17. APPLICATION INTEGRATION GOVERNANCE
17.1 Integration Participation

Application modules may access external or boundary systems only through Integration governance.

17.2 External System Safety

The application must not directly embed external system authority into domain state.

External response is evidence.

Authoritative domain decides meaning.

17.3 Integration Recovery

Application must support integration recovery without duplicate execution.

Integration recovery must preserve idempotency, audit evidence, and authority context.

18. APPLICATION PORTAL GOVERNANCE
18.1 Portal Boundary

Portal is user-facing interaction.

Portal does not own business truth.

Portal must route mutation through API, authorization, workflow, job, integration, and authoritative domain rules.

18.2 Captive Payment Flow

For overdue subscriber payment flow:

Portal may display billing obligation.
Portal may request payment initiation through API.
API must validate identity and authorization.
Payment Lifecycle owns payment process.
Midtrans provides external payment evidence.
Payment Lifecycle validates settlement.
Restoration Orchestration decides restoration eligibility.
Integration executes FreeRADIUS or MikroTik policy changes.
Audit records the full chain.
Portal displays result from read model or authorized query.

Portal must not directly restore service.

18.3 Portal Visibility

Portal visibility must be derived from read models, queries, and authorization.

Portal must not read hidden internal state directly.

19. APPLICATION MULTI-NAS GOVERNANCE
19.1 Multi-NAS Doctrine

The application must support multiple NAS targets without changing business authority.

NAS is execution topology.

NAS is not business ownership.

19.2 NAS-Aware Execution

NAS-aware execution must preserve:

subscriber placement,
Radius policy ownership,
enforcement target selection,
accounting source identity,
reconciliation traceability,
integration audit evidence.
19.3 NAS-Independent Business Rules

Billing, payment, suspension, restoration, authorization, and subscriber truth must remain NAS-independent.

20. APPLICATION FAILURE GOVERNANCE
20.1 Failure Doctrine

Application failure must be explicit, classified, auditable, and recoverable.

Failure must not be hidden behind silent fallback.

20.2 Failure Classes

Application failures include:

validation failure,
authorization failure,
identity failure,
dependency failure,
database failure,
event publication failure,
integration failure,
workflow failure,
job failure,
audit failure,
consistency failure,
replay safety failure.
20.3 Failure Handling

Application failure must result in one of:

rejection,
deferred execution,
recovery state,
reconciliation state,
operator-safe review,
degraded mode,
suspension of unsafe module.
20.4 Failure Evidence

Every failure must produce audit evidence sufficient for forensic reconstruction.

21. APPLICATION RECOVERY GOVERNANCE
21.1 Recovery Doctrine

Application recovery must reconstruct state before resuming execution.

Recovery must not rely on memory, operator recollection, or external target-only state.

21.2 Recovery Sources

Application recovery may use:

authoritative domain records,
event records,
audit records,
workflow records,
job records,
integration execution records,
database records,
reconciliation evidence,
projection rebuilds,
read model rebuilds.
21.3 Recovery Execution

Recovery execution must preserve:

authority,
authorization,
identity,
idempotency,
auditability,
replay safety.
22. APPLICATION REPLAY SAFETY
22.1 Replay Doctrine

Application replay exists to reconstruct state, visibility, workflow progress, job status, integration evidence, and audit history.

Replay must not create duplicate external side effects.

22.2 Replay Modes

The application must distinguish:

live execution,
audit replay,
projection replay,
read model rebuild,
workflow reconstruction,
job reconstruction,
integration recovery execution.
22.3 Replay-Safe Behavior

During replay:

external integrations must not execute,
payment requests must not be recreated,
Telegram notifications must not be resent,
MikroTik commands must not be resent,
FreeRADIUS changes must not be reapplied,
billing commands must not duplicate,
restoration must not duplicate,
suspension must not duplicate.

Unless explicitly authorized recovery execution is active.

23. APPLICATION SECURITY GOVERNANCE
23.1 Security Doctrine

Application security must enforce identity, authorization, credential boundaries, trust boundaries, and data visibility boundaries.

23.2 Credential Boundaries

Application modules must not expose credentials.

Credentials must not appear in:

portal output,
dashboard output,
report output,
search output,
read model output,
error messages,
operator-visible logs.
23.3 Trust Boundaries

Every external and internal trust boundary must follow Integration and Authorization governance.

23.4 Administrative Safety

Administrator access must not bypass domain authority.

Administrative actions must be authorized and audited.

24. APPLICATION OPERATOR SAFETY
24.1 Operator Doctrine

Operator actions must be explicit, authorized, auditable, and reversible where applicable.

Operator convenience must not create hidden authority.

24.2 Operator Capabilities

Operators may be allowed to:

inspect subscriber status,
inspect billing visibility,
inspect payment visibility,
inspect suspension status,
inspect restoration status,
inspect Radius policy status,
inspect integration status,
retry eligible jobs,
trigger governed recovery,
execute approved administrative commands.

Operators must not manually mutate business truth outside governed authority.

24.3 Manual Actions

Manual actions must produce evidence.

Manual action must not erase prior failure evidence.

25. APPLICATION ADMINISTRATOR SAFETY
25.1 Administrator Doctrine

Administrators configure and govern the application within authorization boundaries.

Administrator access does not imply unlimited business authority.

25.2 Configuration Governance

Configuration changes must be:

explicit,
authorized,
audited,
reconstructable,
non-destructive to historical meaning.
25.3 Module Configuration

Module configuration must not silently change ownership, authority, replay behavior, retry policy, or audit policy.

26. PROHIBITED APPLICATION BEHAVIOR

The following are prohibited:

hidden business state inside application runtime,
direct Portal-to-database mutation,
direct Portal-to-MikroTik mutation,
direct Portal-to-FreeRADIUS mutation,
direct Midtrans-to-restoration mutation,
direct Telegram-to-business-state mutation,
API mutation without authorization,
workflow mutation without domain authority,
job retry creating new business intent,
integration response becoming automatic truth,
read model becoming write authority,
dashboard becoming command authority,
report becoming reconciliation authority,
analytics becoming operational authority,
search becoming source of truth,
event replay causing external side effects,
application startup accepting mutation before readiness,
application degradation bypassing safety,
operator memory replacing audit evidence,
undocumented module dependencies,
circular authority dependencies,
cross-domain database writes,
unregistered modules,
unregistered entry points,
unregistered integrations.
27. REQUIRED APPLICATION INVARIANTS

The following invariants must always hold:

Application composes authority; it does not replace authority.
Every module has an owner.
Every module is registered.
Every mutation has authorization.
Every execution has identity context.
Every mutation has audit evidence.
Every external side effect passes Integration governance.
Every workflow step preserves domain authority.
Every job retry preserves original authority.
Every event follows Event Bus governance.
Every read model remains read-only.
Every projection remains reconstruction-safe.
Every Portal mutation passes API governance.
Every administrative action is audited.
Every replay is side-effect safe.
Every NAS-specific execution preserves NAS-independent business authority.
Every failure is classified.
Every recovery is reconstructable.
No hidden state may become business truth.
No module may bypass inherited contracts.
28. COMPLETION CRITERIA

APPLICATION_CONTRACT.md is complete when it defines:

application doctrine,
application ownership,
application authority model,
application composition governance,
application lifecycle,
runtime governance,
module governance,
cross-domain execution governance,
state governance,
consistency governance,
authorization governance,
identity governance,
audit governance,
event governance,
workflow governance,
job governance,
integration governance,
portal governance,
multi-NAS governance,
failure governance,
recovery governance,
replay safety,
security governance,
operator safety,
administrator safety,
prohibited behavior,
required invariants.
29. FINAL AUTHORITY STATEMENT

APPLICATION_CONTRACT.md governs how the ISP Billing & AAA Infrastructure is composed into an executable application.

It governs module composition, runtime execution, cross-domain coordination, application lifecycle, application recovery, application replay safety, operator safety, administrator safety, and audit-safe application behavior.

It does not own subscriber truth.

It does not own billing truth.

It does not own payment truth.

It does not own suspension truth.

It does not own restoration truth.

It does not own workflow truth.

It does not own job truth.

It does not own integration truth.

It does not own event truth.

It does not own API truth.

The application composes authority.

The application preserves authority.

The application must never replace authority.
