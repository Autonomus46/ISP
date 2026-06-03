API_CONTRACT.md
1. API DOCTRINE
1.1 Purpose

The API Layer exists exclusively to distribute system visibility to authorized consumers.

The API Layer is not an authority system.

The API Layer is not a decision system.

The API Layer is not a business-state system.

The API Layer is not a financial-state system.

The API Layer is not an enforcement-state system.

The API Layer exists as a visibility distribution boundary.

1.2 Core Principle

Authority establishes truth.

Events propagate truth.

Projections generate visibility.

Read Models store visibility.

Queries retrieve visibility.

APIs distribute visibility.

Consumers consume visibility.

This ownership chain is immutable.

1.3 API Non-Authority Principle

APIs shall never:

create truth
modify truth
establish authority
replace authority
own business decisions
own financial decisions
own enforcement decisions
own state transitions
own reconciliation decisions

Truth originates only from authoritative domains.

1.4 Deterministic Visibility Principle

Identical query inputs must produce identical API responses when visibility state is unchanged.

API behavior must remain deterministic.

No consumer-specific truth generation is permitted.

2. API OWNERSHIP MODEL
2.1 Ownership Boundary

API ownership is limited to:

visibility distribution
consumer access boundaries
response shaping
authorization enforcement
visibility delivery consistency

APIs never own:

business truth
database truth
financial truth
accounting truth
operational truth
2.2 Visibility Ownership Chain

Authority Domain

↓

Event Store

↓

Projection Layer

↓

Read Models

↓

Queries

↓

APIs

↓

Consumers

API ownership begins after Query ownership ends.

3. API IDENTITY DOCTRINE

Every API must possess immutable identity.

Required attributes:

API Identifier
API Domain
API Owner
Visibility Scope
Authorization Scope
Version Identity
Audit Identity
Lifecycle Status

API identity must remain reconstructable.

4. API LIFECYCLE MODEL
States

Draft

↓

Approved

↓

Active

↓

Deprecated

↓

Retired

Lifecycle Rules

APIs must never become Active without:

ownership assignment
authorization assignment
audit registration
visibility registration
version registration
5. AUTHORITATIVE API CATALOG
Subscriber APIs

Visibility regarding:

subscribers
identities
subscriber lifecycle
subscriber visibility
Service APIs

Visibility regarding:

service allocations
plans
service ownership
service state
Billing APIs

Visibility regarding:

billing lifecycle
obligations
billing state
Invoice APIs

Visibility regarding:

invoices
balances
due status
Payment APIs

Visibility regarding:

payments
payment evidence
payment lifecycle
Settlement APIs

Visibility regarding:

settlements
settlement status
settlement history
Accounting APIs

Visibility regarding:

accounting evidence
session records
accounting state
Suspension APIs

Visibility regarding:

suspension lifecycle
suspension status
enforcement visibility
Restoration APIs

Visibility regarding:

restoration lifecycle
restoration status
NAS APIs

Visibility regarding:

NAS ownership
NAS state
NAS placement
Migration APIs

Visibility regarding:

subscriber migration
migration history
migration status
Operator APIs

Visibility regarding:

operator activities
operational actions
Audit APIs

Visibility regarding:

audit trails
forensic records
evidence history
Notification APIs

Visibility regarding:

notifications
delivery status
Dashboard APIs

Visibility regarding:

dashboard projections
operational metrics
Reporting APIs

Visibility regarding:

reports
historical reporting
Operational APIs

Visibility regarding:

operational projections
runtime visibility
Integration APIs

Visibility regarding:

external visibility distribution
partner visibility distribution
Telegram APIs

Visibility regarding:

Telegram operational visibility
Telegram notifications
Telegram command visibility
6. API-TO-QUERY RELATIONSHIP MODEL
Fundamental Rule

APIs never retrieve truth directly.

APIs consume Queries.

Queries consume Read Models.

Mandatory Flow

Authority

↓

Events

↓

Projections

↓

Read Models

↓

Queries

↓

APIs

↓

Consumers

No alternative path is permitted.

Query Ownership Preservation

APIs must not:

embed query logic
bypass query governance
duplicate query ownership

Queries remain the exclusive retrieval authority.

7. API AUTHORIZATION MODEL

Authorization ownership belongs to the Authorization Domain.

APIs enforce authorization.

APIs do not define authorization truth.

Authorization Scope

Examples:

Operator Visibility
Subscriber Visibility
Billing Visibility
Payment Visibility
Audit Visibility
Administrative Visibility
Integration Visibility
Authorization Principle

Unauthorized visibility must never be exposed.

Visibility leakage is considered a contract violation.

8. API VISIBILITY MODEL

Visibility exposed by APIs must be:

projection-backed
read-model-backed
query-backed
auditable
reconstructable

Visibility must always possess ownership lineage.

9. API CONSISTENCY MODEL

API consistency inherits from:

Event Consistency
Projection Consistency
Read Model Consistency
Query Consistency

APIs may never invent consistency guarantees.

Consistency Rule

APIs expose visibility exactly as generated by underlying visibility infrastructure.

10. API RECONSTRUCTION MODEL

Every API response must be reconstructable.

Reconstruction path:

API Response

↓

Query

↓

Read Model

↓

Projection

↓

Events

↓

Authority

Reconstruction Requirement

Investigators must be able to determine:

why response existed
what generated response
when response became visible
who consumed response
11. API AUDIT MODEL

Every API interaction must be auditable.

Audit records must include:

API identity
consumer identity
request identity
authorization identity
response identity
timestamp
correlation identity
Audit Visibility

No API operation may be audit-invisible.

12. API RETENTION MODEL

Retention policies must support:

forensic reconstruction
operational investigation
audit review
historical replay

Retention ownership belongs to Audit Governance.

APIs inherit retention requirements.

13. API FAILURE RECOVERY MODEL
Recoverable Failures
projection lag
query failures
read model unavailability
integration failures
consumer failures
Recovery Principle

Failures must never generate alternative truth.

Failures must never generate synthetic visibility.

Failures must never bypass ownership layers.

Recovery Path

Recover

↓

Reconstruct

↓

Revalidate

↓

Resume

Never:

Recover

↓

Invent

↓

Continue

14. API VERSIONING DOCTRINE
Version Identity

Every API version must possess:

unique version identity
activation timestamp
deprecation timestamp
retirement timestamp
Version Rule

Versions may change visibility structure.

Versions may never change truth ownership.

Backward Compatibility Principle

Visibility evolution must remain traceable across versions.

15. EXTERNAL INTEGRATION MODEL

External consumers include:

dashboards
mobile applications
web portals
partner systems
reporting systems
Telegram systems
public integrations
Integration Principle

External integrations consume APIs.

External integrations never consume:

authority directly
event store directly
projection infrastructure directly
read models directly
database tables directly
Integration Ownership Boundary

External integrations are consumers.

They are not authorities.

16. FORBIDDEN API PATTERNS
Forbidden Authority Violations

Prohibited:

API-generated authority
API-generated truth
API-owned business decisions
API-owned billing decisions
API-owned payment decisions
API-owned enforcement decisions
API-owned lifecycle transitions
Forbidden Visibility Violations

Prohibited:

API bypassing queries
API bypassing read models
API bypassing projections
API bypassing authority
Forbidden Infrastructure Violations

Prohibited:

direct database APIs
direct event-store APIs
direct projection APIs
direct authority APIs
Forbidden Governance Violations

Prohibited:

hidden APIs
undocumented APIs
audit-invisible APIs
manual API corrections
visibility without ownership
ownership without auditability
Forbidden Consumer Behavior

Prohibited:

consumer-specific truth
consumer-specific authority
consumer-specific state generation
17. REPLAY-SAFE API DOCTRINE
Replay Principle

API behavior must remain reproducible from historical truth.

Replay Path

Historical Authority

↓

Historical Events

↓

Historical Projections

↓

Historical Read Models

↓

Historical Queries

↓

Historical API Response

Replay Requirements

Replaying historical infrastructure must reproduce:

API visibility
API authorization outcomes
API audit records
API version ownership
Replay Prohibition

APIs must never generate visibility that cannot be reproduced from historical truth.

18. FINAL API DOCTRINE
Authority Doctrine

Authority owns truth.

APIs never own truth.

Visibility Doctrine

Queries retrieve visibility.

APIs distribute visibility.

Ownership Doctrine

APIs expose.

APIs do not decide.

APIs do not govern authority.

APIs do not create authority.

Determinism Doctrine

Identical truth must produce identical visibility.

Identical visibility must produce identical API responses.

Audit Doctrine

Every API action must be:

visible
traceable
reconstructable
replayable
auditable
Institutional Rule

No dashboard,

No report,

No mobile application,

No operator portal,

No Telegram integration,

No partner integration,

No public integration,

No external system,

shall obtain visibility except through APIs governed by this contract.

This contract is the sole authoritative governance layer for visibility distribution across the ISP Billing & AAA Infrastructure.
