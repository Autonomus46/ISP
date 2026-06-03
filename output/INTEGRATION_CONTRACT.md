INTEGRATION_CONTRACT.md
PROJECT CONTINUATION — ISP BILLING & AAA INFRASTRUCTURE
MODE

INSTITUTIONAL INTEGRATION ARCHITECTURE DESIGN

TARGET DOCUMENT

INTEGRATION_CONTRACT.md

DOCUMENT STATUS

Authoritative Integration Contract.

This document governs integration ownership, integration authority boundaries, integration lifecycle governance, external system interaction governance, internal system interaction governance, message exchange governance, trust boundary governance, deterministic integration execution, replay-safe integration behavior, recovery governance, and audit-safe system connectivity across the ISP Billing & AAA Infrastructure.

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

THIS CONTRACT INHERITS ALL WORKFLOW DEFINITIONS FROM WORKFLOW_CONTRACT.md.

THIS CONTRACT INHERITS ALL JOB DEFINITIONS FROM JOB_ORCHESTRATION_CONTRACT.md.

THIS CONTRACT INHERITS ALL API DEFINITIONS FROM API_CONTRACT.md.

THIS CONTRACT INHERITS ALL PORTAL DEFINITIONS FROM PORTAL_CONTRACT.md.

THIS CONTRACT INHERITS ALL PROJECTION DEFINITIONS FROM PROJECTION_CONTRACT.md.

THIS CONTRACT INHERITS ALL READ MODEL DEFINITIONS FROM READ_MODEL_CONTRACT.md.

1. INTEGRATION DOCTRINE
1.1 Integration Purpose

Integration exists to allow authoritative systems, execution systems, external systems, operational systems, and visibility systems to exchange information or execute controlled interactions without transferring ownership of truth.

Integration is a controlled boundary.

Integration is not business authority.

Integration is not financial authority.

Integration is not subscriber authority.

Integration is not workflow authority.

Integration is not job authority.

Integration is not event authority.

Integration is not portal authority.

Integration transports authority from the authoritative source to the target system according to governed rules.

1.2 Core Integration Law

No integration may create, mutate, infer, override, or finalize authoritative state unless the inherited authoritative contract explicitly grants that authority to the calling domain.

An integration may carry a command.

An integration may carry an event.

An integration may carry evidence.

An integration may carry a query.

An integration may carry a response.

An integration may not become the source of truth for the state it transports.

1.3 Integration Authority Principle

Every integration interaction must identify:

the authoritative source,
the initiating actor,
the executing integration boundary,
the target system,
the authority being transported,
the reason for transport,
the expected result,
the evidence produced,
the audit trace,
the replay behavior.

If any of these cannot be reconstructed, the integration is not valid.

2. INTEGRATION OWNERSHIP
2.1 Integration Ownership Definition

An integration is owned by the domain responsible for governing the boundary between two systems.

Integration ownership includes:

boundary definition,
allowed interaction definition,
request validation,
response validation,
timeout governance,
retry governance,
failure classification,
evidence preservation,
audit trace preservation,
replay-safety enforcement.
2.2 Ownership Separation

Integration ownership does not imply ownership of the target system state.

Integration ownership does not imply ownership of the source domain state.

Integration ownership does not override workflow ownership.

Integration ownership does not override job ownership.

Integration ownership does not override API ownership.

Integration ownership does not override event ownership.

Integration ownership does not override billing ownership.

Integration ownership does not override payment ownership.

Integration ownership does not override suspension ownership.

Integration ownership does not override restoration ownership.

2.3 Integration Registry Requirement

Every integration must be registered before use.

The integration registry must define:

integration name,
integration purpose,
source domain,
target system,
interaction type,
allowed initiators,
required authorization,
required identity context,
trust boundary classification,
timeout policy,
retry policy,
duplicate handling policy,
recovery policy,
audit evidence requirements,
replay-safety requirements,
retirement policy.

Unregistered integrations are prohibited.

3. INTEGRATION AUTHORITY MODEL
3.1 Authority Transport

Integration may transport authority only when authority has already been established by the authoritative domain.

Examples:

Billing Core may authorize billing-derived actions.
Payment Lifecycle may authorize payment-derived actions.
Suspension Orchestration may authorize restriction actions.
Restoration Orchestration may authorize reactivation actions.
Workflow may coordinate approved execution steps.
Job Orchestration may execute scheduled or asynchronous tasks.
API may expose controlled interaction surfaces.
Portal may initiate user-facing interactions under authorization.

The integration layer must validate the authority envelope before execution.

3.2 Prohibited Authority Escalation

An integration must not:

convert notification into command authority,
convert external callback into financial truth without payment validation,
convert MikroTik state into subscriber truth,
convert FreeRADIUS response into billing truth,
convert Telegram message into administrative authority without authorization,
convert portal input into workflow execution without API authorization,
convert job retry into new business intent,
convert event replay into duplicate external execution.
3.3 Authority Envelope

Every authoritative integration request must carry an authority envelope.

The authority envelope must identify:

source authority domain,
command or event identity,
actor identity,
authorization decision reference,
correlation reference,
causation reference,
execution purpose,
replay classification,
idempotency reference,
audit reference.

Any integration request without a valid authority envelope must be rejected.

4. INTEGRATION VISIBILITY MODEL
4.1 Visibility Purpose

Integration visibility exists to allow administrators, operators, auditors, and system components to inspect integration execution without granting mutation authority.

4.2 Visibility Boundaries

Integration visibility may expose:

integration status,
target system name,
execution result,
failure reason,
retry count,
last execution timestamp,
correlation reference,
audit reference.

Integration visibility must not expose:

secrets,
credentials,
private keys,
raw tokens,
unmasked sensitive payloads,
unauthorized subscriber data,
unauthorized payment data.
4.3 Visibility Does Not Create Authority

Seeing an integration result does not authorize the viewer to retry, override, cancel, approve, or mutate it.

All operational actions must pass through authorization governance.

5. INTEGRATION TRUST MODEL
5.1 Trust Doctrine

No integration boundary is trusted by default.

Every integration must verify:

source identity,
target identity,
message integrity,
authority context,
authorization context,
replay context,
request freshness,
response validity.
5.2 Internal Trust

Internal systems are trusted only within their governed authority boundaries.

An internal service may be trusted to execute its role.

An internal service is not trusted to mutate state outside its authority.

5.3 External Trust

External systems are never authoritative over internal business state unless the relevant authoritative contract explicitly permits their evidence to be accepted after validation.

External systems may provide:

payment evidence,
network execution result,
communication delivery result,
callback evidence,
session evidence,
operational signal.

External systems must not directly own internal business state.

6. INTEGRATION LIFECYCLE
6.1 Registration

An integration must be registered before activation.

Registration must define ownership, authority, trust, execution, retry, recovery, and audit rules.

6.2 Activation

An integration may be activated only when:

ownership is defined,
authority boundary is defined,
credentials are governed,
timeout policy is defined,
retry policy is defined,
recovery policy is defined,
audit evidence is defined,
authorization policy is defined,
replay-safety behavior is defined.
6.3 Execution

Integration execution must be deterministic.

Execution must be based on explicit input, valid authority, valid authorization, valid identity context, valid idempotency reference, and valid target classification.

6.4 Suspension

An integration may be suspended when:

target system becomes unsafe,
credential integrity is compromised,
repeated failures exceed governed limits,
replay safety cannot be guaranteed,
audit preservation fails,
authority envelope validation fails,
dependency state becomes inconsistent.

Suspended integrations must not execute outbound commands.

6.5 Restoration

An integration may be restored only after:

failure cause is identified,
trust boundary is revalidated,
credentials are verified or rotated,
pending executions are reconstructed,
duplicate risk is resolved,
audit continuity is preserved,
replay behavior is confirmed.
6.6 Retirement

An integration may be retired only when:

no active workflows depend on it,
no active jobs depend on it,
no pending recovery depends on it,
audit records are preserved,
replacement integration is defined when required,
historical reconstruction remains possible.
7. INTEGRATION AUTHORITY BOUNDARIES
7.1 Integration Versus Workflow

Workflow owns orchestration intent and step governance.

Integration owns system boundary execution.

Workflow may request integration execution.

Integration must not decide workflow progression unless the workflow contract explicitly defines that result as a valid step outcome.

7.2 Integration Versus API

API owns external and internal request surfaces.

Integration owns downstream system boundary interaction.

API may initiate integration only after authorization and validation.

Integration must not expose public API behavior.

7.3 Integration Versus Event Bus

Event Bus owns event propagation and delivery governance.

Integration may consume or publish events only according to event authority.

Integration must not redefine event meaning.

Integration must not mutate event payloads.

Integration must not publish events claiming authority it does not own.

7.4 Integration Versus Billing State

Billing Core owns billing state.

Integration may transport billing commands, billing notifications, billing evidence, or billing-derived requests.

Integration must not create invoice truth, debt truth, overdue truth, balance truth, or eligibility truth.

7.5 Integration Versus Payment State

Payment Lifecycle owns payment state.

Midtrans or any external payment provider may provide evidence.

Integration must transport and validate payment evidence.

Integration must not declare payment settlement without Payment Lifecycle authority.

7.6 Integration Versus Subscriber State

Subscriber ownership remains governed by the authoritative subscriber domain inherited from system authority.

Integration may transport subscriber-related execution requests.

Integration must not create, modify, suspend, restore, or delete subscriber truth independently.

7.7 Integration Versus Enforcement State

Suspension and Restoration contracts own enforcement intent.

MikroTik and FreeRADIUS execute enforcement.

Integration may transport enforcement instructions.

Integration must not decide enforcement eligibility.

8. INTERNAL INTEGRATION GOVERNANCE
8.1 Billing Core Integrations

Billing Core integrations must preserve Billing Core authority.

Billing integration may:

transport billing decisions,
request invoice-related side effects,
emit billing-derived events,
provide billing evidence to read models.

Billing integration must not allow external systems to mutate billing truth.

8.2 Payment Integrations

Payment integrations must preserve Payment Lifecycle authority.

Payment integration may:

send payment creation requests to providers,
receive payment callbacks,
validate provider evidence,
transport evidence to Payment Lifecycle,
record provider communication evidence.

Payment integration must not finalize settlement outside Payment Lifecycle authority.

8.3 Workflow Integrations

Workflow integrations must preserve workflow authority.

Workflow may coordinate integration execution, but integration result must be treated as step evidence, not business truth.

8.4 Job Integrations

Job integrations must preserve job authority and replay safety.

Jobs may execute integration calls only when:

job ownership is valid,
job execution is authorized,
idempotency is enforced,
duplicate execution is prevented,
audit evidence is recorded.
8.5 Event Bus Integrations

Event Bus integrations must preserve event authority.

Integration may consume events for external communication or internal coordination only when consumption is registered.

Integration must not convert event replay into repeated external side effects unless replay-safe handling explicitly permits it.

8.6 Projection Integrations

Projection integrations must preserve projection authority.

Projection may consume integration evidence for visibility.

Projection must not trigger integration execution unless explicitly governed by workflow or job authority.

8.7 Read Model Integrations

Read Model integrations must preserve read-side authority.

Read models may expose integration status.

Read models must not become execution sources.

8.8 Portal Integrations

Portal integrations must pass through API and authorization governance.

Portal must not directly invoke external systems.

Portal must not bypass workflow, job, API, authorization, or integration authority.

8.9 Audit Integrations

Audit integrations must preserve evidence integrity.

Audit integration may receive integration evidence.

Audit integration must not alter original execution meaning.

8.10 Authorization Integrations

Authorization integrations must preserve authorization authority.

Integration may request authorization decisions.

Integration must not self-authorize.

Integration must not cache authorization decisions beyond governed validity.

9. EXTERNAL INTEGRATION GOVERNANCE
9.1 Midtrans Integration

Midtrans integration is an external payment provider boundary.

Midtrans may provide payment request responses and payment callback evidence.

Midtrans does not own internal payment truth.

Midtrans does not own invoice truth.

Midtrans does not own restoration eligibility.

Midtrans evidence must be validated before Payment Lifecycle accepts it.

9.2 MikroTik Integration

MikroTik integration is a network enforcement execution boundary.

MikroTik may execute policy, session, or enforcement actions.

MikroTik does not own subscriber truth.

MikroTik does not own billing truth.

MikroTik does not own suspension truth.

MikroTik does not own restoration truth.

MikroTik state may be used as execution evidence only.

9.3 FreeRADIUS Integration

FreeRADIUS integration is an AAA policy execution boundary.

FreeRADIUS may authenticate, authorize, and account according to governed policy.

FreeRADIUS does not own billing truth.

FreeRADIUS does not own subscriber truth.

FreeRADIUS does not own payment truth.

FreeRADIUS execution evidence must be reconciled according to accounting and reconciliation contracts.

9.4 Telegram Integration

Telegram integration is an operator communication and command boundary.

Telegram messages are not authority by themselves.

Telegram commands must pass identity, authorization, command validation, workflow governance, and audit governance.

Telegram notifications are informational unless explicitly tied to authorized command flow.

9.5 Email Integration

Email integration is a notification and communication boundary.

Email delivery does not prove subscriber acknowledgement unless governed evidence exists.

Email does not own billing state, payment state, subscriber state, suspension state, or restoration state.

9.6 External Notification Integrations

External notification systems may transport messages.

They must not create business truth.

Delivery result may be recorded as evidence but must not override authoritative state.

9.7 Future Third-Party Integrations

Future third-party integrations must be registered before activation.

They must define authority, trust, credential, retry, recovery, and audit behavior before execution.

No future integration may bypass this contract.

10. TRUST BOUNDARY GOVERNANCE
10.1 Trust Boundary Definition

A trust boundary exists whenever information or command execution crosses from one governed authority context to another.

Trust boundaries include:

internal service to internal service,
backend to external provider,
backend to MikroTik,
backend to FreeRADIUS,
backend to Telegram,
portal to API,
API to workflow,
job engine to integration target,
event bus to consumer,
integration layer to audit.
10.2 Trust Verification

Every trust boundary crossing must verify:

identity,
authorization,
message integrity,
request origin,
target classification,
authority envelope,
idempotency key,
correlation context,
replay classification.
10.3 Trust Preservation

Trust context must be preserved across every integration call.

If trust context is lost, downstream execution must stop.

10.4 Trust Failure Handling

Trust failure must result in:

execution rejection,
audit evidence recording,
failure classification,
no state mutation outside the authoritative domain,
no retry unless retry is explicitly safe.
11. MESSAGE GOVERNANCE
11.1 Message Ownership

A message is owned by the domain that creates it.

Integration transports messages.

Integration does not own the meaning of messages it transports unless the message is an integration-status message.

11.2 Message Authority

Message authority depends on the source domain.

A message from Billing Core carries billing authority only within the scope granted by Billing Core.

A message from Payment Lifecycle carries payment authority only within the scope granted by Payment Lifecycle.

A message from an external provider carries external evidence only.

11.3 Message Visibility

Message visibility must follow authorization governance.

Sensitive message fields must be protected.

Operational views may show message status without revealing confidential content.

11.4 Message Consistency

Messages must preserve:

identity,
source,
target,
correlation,
causation,
timestamp,
authority envelope,
payload integrity,
replay classification.
11.5 Message Reconstruction

Every integration message must be reconstructable from preserved evidence.

Reconstruction must show:

what was sent,
why it was sent,
who or what initiated it,
which authority permitted it,
which target received it,
what response was received,
what retry or recovery occurred,
what final status was reached.
11.6 Message Traceability

Every message must be traceable to a workflow, job, API request, event, or authorized system action.

Orphan messages are prohibited.

12. INTEGRATION EXECUTION GOVERNANCE
12.1 Execution Authority

Integration execution requires valid execution authority.

Execution authority may originate from:

authorized API request,
workflow step,
scheduled job,
event-driven handler,
administrative command,
reconciliation process,
recovery process.

Execution without authority is prohibited.

12.2 Execution Consistency

Execution must be consistent with source state at the time of execution.

If source state changes before execution, the integration must revalidate authority before proceeding.

12.3 Execution Isolation

Integration execution must be isolated by target, authority context, subscriber context, and operation type.

Failure in one integration must not corrupt unrelated integrations.

12.4 Execution Ordering

Ordering must be preserved when business correctness depends on order.

Examples:

payment validation before restoration,
suspension decision before MikroTik restriction,
subscriber provisioning before Radius policy availability,
invoice creation before payment request creation,
policy generation before FreeRADIUS execution.
12.5 Concurrency Governance

Concurrent integration executions must not create duplicate side effects, contradictory target state, or inconsistent audit history.

Concurrency must be controlled through idempotency, authority validation, and target-specific consistency rules.

13. INTEGRATION FAILURE GOVERNANCE
13.1 Communication Failures

Communication failure must be classified as unknown result unless evidence proves failure or success.

Unknown result must not be treated as successful.

Unknown result must not be blindly retried without duplicate-risk evaluation.

13.2 Timeout Handling

Timeouts must preserve uncertainty.

A timeout means the system did not receive a timely response.

A timeout does not prove that the target did not execute the request.

Timeout recovery must check idempotency and target evidence when possible.

13.3 Retry Handling

Retries must follow retry governance.

Retry must not create duplicate external side effects.

Retry must not create new business intent.

Retry must not change original authority context.

13.4 Duplicate Message Handling

Duplicate inbound or outbound messages must be detected through message identity, idempotency references, correlation references, and target evidence.

Duplicate messages must not create duplicate state transitions.

13.5 Unavailable Dependency Handling

If a dependency is unavailable, integration execution must fail safely, defer safely, or enter recovery state.

Unavailable dependency must not cause unauthorized fallback behavior.

14. RETRY GOVERNANCE
14.1 Retry Doctrine

Retry exists to recover from transient failure.

Retry is not a new command.

Retry is not new business authority.

Retry is not an operator override.

Retry is continuation of the original authorized execution.

14.2 Retry Eligibility

Retry is eligible only when:

original authority remains valid,
idempotency can be preserved,
duplicate risk is controlled,
target behavior is known,
retry limit is not exceeded,
audit trail can be preserved,
retry is allowed by integration registry.
14.3 Retry Safety

Retry must be safe against:

duplicate payment creation,
duplicate restoration,
duplicate suspension,
duplicate notification,
duplicate Radius policy update,
duplicate MikroTik enforcement action,
duplicate event publication.
14.4 Retry Consistency

Retry must use the same authority context as the original execution unless the authoritative domain explicitly issues a new command.

14.5 Replay-Safe Retries

Replay must not cause retry unless replay mode explicitly allows internal reconstruction without external side effects.

Replay of historical events must not re-send payment requests, Telegram messages, MikroTik commands, FreeRADIUS changes, or external notifications unless governed as recovery execution.

15. RECOVERY GOVERNANCE
15.1 Interrupted Integrations

Interrupted integrations must be reconstructed before recovery.

The system must determine:

whether request was sent,
whether target received it,
whether target executed it,
whether response was received,
whether state changed,
whether retry is safe,
whether manual review is required.
15.2 Failed Integrations

Failed integrations must be classified.

Failure classes include:

validation failure,
authorization failure,
trust failure,
timeout,
target rejection,
target unavailable,
duplicate detected,
inconsistent response,
credential failure,
unknown result.
15.3 Partial Execution Recovery

Partial execution must not be hidden.

If an integration partially executed, recovery must preserve evidence and continue only through governed workflow, job, reconciliation, or administrative authority.

15.4 Dependency Recovery

When a dependency recovers after outage, pending integrations must be re-evaluated before execution.

The system must not blindly flush backlog without authority and idempotency validation.

15.5 Integration Reconstruction

Integration reconstruction must be possible from audit records, message records, event records, job records, workflow records, and target evidence.

16. EVENT INTEGRATION GOVERNANCE
16.1 Event Consumption

Integration event consumption must be registered.

Event consumption must define:

consumed event type,
consuming integration,
purpose,
authority mapping,
side-effect policy,
replay behavior,
failure policy,
audit policy.
16.2 Event Publication

Integration may publish events only when it owns the event type or is explicitly authorized to publish that event type.

Integration may publish integration-result events.

Integration must not publish billing, payment, subscriber, suspension, restoration, or workflow events unless governed by the authoritative contract.

16.3 Replay-Safe Event Interaction

During replay, integration handlers must distinguish reconstruction from live execution.

Replay must not trigger external side effects unless the replay is explicitly classified as recovery execution and authorized.

16.4 Deterministic Event Handling

Given the same event, same authority context, same registry policy, and same historical evidence, integration handling must produce the same internal decision.

External side effects must be controlled through idempotency and replay classification.

17. SECURITY GOVERNANCE
17.1 Integration Authentication

Every integration must authenticate source and target where applicable.

Unauthenticated requests must be rejected.

17.2 Integration Authorization

Authentication does not imply authorization.

Every integration action must pass authorization governance.

17.3 Credential Boundaries

Credentials must be scoped to integration purpose.

Credentials must not be shared across unrelated integrations.

Credentials must not be exposed to portal, read models, dashboards, reports, logs, or operator-visible output.

17.4 Trust Verification

Security verification must include identity, authorization, credential validity, message integrity, and replay classification.

17.5 Access Governance

Access to integration execution, retry, recovery, suspension, restoration, and retirement must be governed by authorization.

Operator convenience must not bypass access governance.

18. AUDITABILITY
18.1 Integration Traceability

Every integration execution must be traceable from origin to result.

Traceability must include:

initiator,
source authority,
request identity,
target system,
execution timestamp,
response timestamp,
result,
failure class,
retry history,
recovery history,
audit reference.
18.2 Execution Attribution

Execution attribution must identify whether execution was initiated by:

user,
administrator,
operator,
API,
workflow,
job,
event handler,
reconciliation process,
recovery process.
18.3 Message Attribution

Every message must preserve source, target, actor, correlation, causation, and authority context.

18.4 Integration Evidence

Integration evidence must include request evidence, response evidence, failure evidence, retry evidence, recovery evidence, and final outcome evidence.

18.5 Forensic Reconstruction

Forensic reconstruction must answer:

what integration executed,
who initiated it,
what authority permitted it,
what target was contacted,
what payload was sent,
what result was received,
whether retries occurred,
whether recovery occurred,
whether duplicate prevention worked,
whether final state matched authoritative state.
19. REPLAY SAFETY
19.1 Replay-Safe Integration Doctrine

Replay must reconstruct decisions without duplicating external side effects.

Integration replay must distinguish:

live execution,
historical reconstruction,
recovery execution,
audit replay,
projection rebuild,
read model rebuild.
19.2 Duplicate Execution Prevention

Duplicate execution must be prevented through idempotency, message identity, authority envelope, target evidence, and audit correlation.

19.3 Idempotent Integration Doctrine

Every integration that can produce side effects must define idempotency behavior.

If idempotency cannot be guaranteed, execution must require stronger recovery governance and operator-safe review.

19.4 Deterministic Reconstruction

Reconstruction must not depend on hidden state, volatile external state, undocumented operator memory, or target-only state.

20. MULTI-NAS GOVERNANCE
20.1 Cross-NAS Integration

Cross-NAS integrations must preserve subscriber placement authority and NAS ownership boundaries.

Integration must not assume that all subscribers belong to one NAS.

20.2 Integration Consistency Across NAS

Policy, enforcement, accounting, suspension, and restoration integrations must remain consistent across NAS boundaries.

NAS-specific execution must not create system-wide authority drift.

20.3 Authority Preservation Across NAS Boundaries

MikroTik NAS devices execute enforcement.

They do not own subscriber authority.

They do not own billing authority.

They do not own payment authority.

They do not own restoration authority.

20.4 NAS-Independent Integration Doctrine

Business decisions must remain NAS-independent.

NAS selection affects execution target only.

NAS selection must not redefine billing, payment, subscriber, suspension, or restoration truth.

21. INTEGRATION STATE GOVERNANCE
21.1 Integration State Types

Integration state may include:

registered,
active,
suspended,
retiring,
retired,
failed,
recovering,
degraded.
21.2 Integration Execution State

Execution state may include:

pending,
authorized,
sending,
sent,
acknowledged,
succeeded,
failed,
timed out,
unknown,
retrying,
recovered,
abandoned with evidence.
21.3 State Ownership

Integration execution state is owned by the integration boundary.

Business state remains owned by the authoritative domain.

22. OPERATOR SAFETY
22.1 Operator Actions

Operator actions against integrations must be authorized and audited.

Operator may be allowed to:

inspect status,
retry eligible execution,
suspend integration,
restore integration,
mark for review,
trigger recovery workflow.

Operator must not manually mutate business truth through integration tools.

22.2 Administrative Overrides

Administrative override must be explicit, authorized, audited, and tied to a reason.

Override must not erase original failure evidence.

22.3 Manual Recovery

Manual recovery must create evidence.

Manual recovery must not rely on undocumented memory.

Manual recovery must not bypass authority contracts.

23. ADMINISTRATOR SAFETY
23.1 Administrator Authority

Administrators may govern integration configuration only within authorization boundaries.

Administrator access does not imply authority to mutate billing, payment, subscriber, suspension, or restoration truth.

23.2 Configuration Changes

Integration configuration changes must be audited.

Configuration changes must not retroactively alter historical execution meaning.

23.3 Credential Changes

Credential changes must be governed, attributed, and auditable.

Credential rotation must preserve integration continuity and recovery safety.

24. PROHIBITED INTEGRATION BEHAVIOR

The following are prohibited:

external provider directly mutating billing state,
MikroTik directly mutating subscriber state,
FreeRADIUS directly mutating payment state,
Telegram command bypassing authorization,
portal directly calling external systems,
retry without idempotency control,
event replay causing external side effects,
integration creating authority it does not own,
integration hiding partial execution,
integration swallowing failures without audit,
integration using hidden state,
integration depending on operator memory,
integration bypassing workflow where workflow is required,
integration bypassing job governance for asynchronous execution,
integration bypassing API governance for request entry,
integration bypassing authorization governance,
integration exposing credentials through visibility surfaces,
integration executing unregistered third-party calls.
25. REQUIRED INTEGRATION INVARIANTS

The following invariants must always hold:

Integrations transport authority; they do not become authority.
Every integration has an owner.
Every integration is registered before activation.
Every integration execution has authority context.
Every integration execution has authorization context.
Every integration execution has traceability.
Every external side effect has idempotency governance.
Every retry preserves original authority.
Every timeout preserves uncertainty.
Every failure is classified.
Every recovery is auditable.
Every replay is side-effect safe.
Every trust boundary is verified.
Every target response is evidence, not automatic truth.
Every multi-NAS interaction preserves NAS-independent business authority.
26. COMPLETION CRITERIA

INTEGRATION_CONTRACT.md is complete when it defines:

integration doctrine,
integration ownership,
integration authority boundaries,
integration lifecycle,
internal integration governance,
external integration governance,
trust boundary governance,
message governance,
execution governance,
failure governance,
retry governance,
recovery governance,
event integration governance,
security governance,
auditability,
replay safety,
multi-NAS governance,
operator safety,
administrator safety,
prohibited behavior,
required invariants.
27. FINAL AUTHORITY STATEMENT

INTEGRATION_CONTRACT.md governs system integration behavior across the ISP Billing & AAA Infrastructure.

It governs how systems connect, exchange messages, execute external interactions, preserve trust, recover from failures, prevent duplicate side effects, and maintain audit-safe connectivity.

It does not own subscriber truth.

It does not own billing truth.

It does not own payment truth.

It does not own suspension truth.

It does not own restoration truth.

It does not own workflow truth.

It does not own job truth.

It does not own event truth.

It does not own API truth.

It does not own portal truth.

Integrations transport authority.

Integrations preserve authority.

Integrations must never replace authority.
