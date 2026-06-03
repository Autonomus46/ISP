QUERY_CONTRACT.md
1. QUERY DOCTRINE
1.1 Query Purpose

Queries exist solely to retrieve visibility from authoritative Read Models.

Queries do not:

create truth
modify truth
own truth
generate authority
establish business decisions
create lifecycle transitions
generate events
mutate state

Queries expose already-established visibility.

Authority establishes truth.

Queries retrieve visibility.

1.2 Query Position in Architecture
Authority
    ↓
Event
    ↓
Projection
    ↓
Read Model
    ↓
Query
    ↓
API
    ↓
Dashboard
    ↓
Reporting

Queries operate exclusively on Read Models.

Queries never bypass Read Models.

Queries never access authority directly.

1.3 Query Determinism

Identical query inputs against identical read-model state must always produce identical outputs.

Query execution must be deterministic.

Non-deterministic query behavior is forbidden.

2. QUERY OWNERSHIP MODEL
2.1 Query Ownership

Queries own retrieval behavior.

Queries do not own:

business rules
lifecycle rules
state transitions
financial decisions
enforcement decisions
reconciliation decisions
2.2 Authority Separation

Authority determines:

what is true

Queries determine:

what visibility is returned

Authority and query ownership must remain permanently separated.

3. QUERY IDENTITY DOCTRINE

Every query execution must possess:

Query ID
Query Type
Query Owner
Query Timestamp
Request Context
Authorization Context
Visibility Scope
Read Model Version

Query executions must be uniquely identifiable.

Anonymous query execution is forbidden.

4. QUERY LIFECYCLE MODEL
Query Lifecycle
Requested
    ↓
Authorized
    ↓
Validated
    ↓
Executed
    ↓
Returned
    ↓
Audited
    ↓
Retained

Queries never transition business entities.

Queries only transition through retrieval states.

5. AUTHORITATIVE QUERY CATALOG
Subscriber Queries

Retrieve:

subscriber profile visibility
subscriber lifecycle visibility
subscriber service visibility
Service Queries

Retrieve:

service assignments
service states
service history
Billing Queries

Retrieve:

billing visibility
debt visibility
eligibility visibility
Invoice Queries

Retrieve:

invoice visibility
invoice status visibility
invoice history
Payment Queries

Retrieve:

payment visibility
payment evidence visibility
payment history
Settlement Queries

Retrieve:

settlement visibility
settlement audit visibility
Accounting Queries

Retrieve:

accounting evidence
reconciliation visibility
Suspension Queries

Retrieve:

suspension visibility
suspension history
Restoration Queries

Retrieve:

restoration visibility
restoration history
NAS Queries

Retrieve:

NAS visibility
placement visibility
ownership visibility
Migration Queries

Retrieve:

migration visibility
migration history
Operator Queries

Retrieve:

operator actions
operator visibility
Audit Queries

Retrieve:

forensic evidence
audit evidence
investigation visibility
Notification Queries

Retrieve:

notification history
delivery visibility
Dashboard Queries

Retrieve:

dashboard projections
operational summaries
Reporting Queries

Retrieve:

reporting datasets
reporting snapshots
Operational Queries

Retrieve:

infrastructure visibility
runtime visibility
operational evidence
6. QUERY-TO-READ RELATIONSHIP MODEL

Queries may consume:

Read Model

Queries may not consume:

Event Store
Authority Aggregate
Projection Engine
Write Database
Command Models

All query outputs must originate from authoritative Read Models.

7. QUERY AUTHORIZATION MODEL

Every query execution requires authorization.

Authorization determines:

who may execute
what may be viewed
visibility boundaries
scope restrictions

Authorization does not modify query results.

Authorization restricts visibility only.

8. QUERY VISIBILITY MODEL

Visibility must be explicitly owned.

Visibility inheritance must follow:

Authority
    ↓
Projection
    ↓
Read Model
    ↓
Query

Visibility may never originate inside queries.

Queries expose visibility.

Queries do not create visibility.

9. QUERY CONSISTENCY MODEL

Query outputs must reflect:

authoritative projections
authoritative read models
authoritative reconstruction state

Queries must never fabricate missing information.

Unknown must remain unknown.

Consistency Doctrine
Incorrect Visibility
is worse than
Unavailable Visibility
10. QUERY RECONSTRUCTION MODEL

Query outputs must remain reconstructable.

Every query result must be reproducible from:

Authority
→ Events
→ Projection
→ Read Model
→ Query

Reconstruction must produce identical visibility.

11. QUERY AUDIT MODEL

Every query execution must be auditable.

Audit records must include:

query identity
requester identity
authorization context
read-model version
execution timestamp
returned visibility scope

Audit records must remain immutable.

12. QUERY RETENTION MODEL

Query audit history must remain retained according to audit retention policy.

Retention must preserve:

forensic reconstruction
compliance reconstruction
incident investigation

Retention must never alter historical truth.

13. QUERY FAILURE RECOVERY MODEL

Failures must be recoverable.

Supported failures include:

read-model unavailable
authorization unavailable
projection lag
infrastructure failure
visibility reconstruction failure

Recovery must never invent data.

Recovery must preserve deterministic behavior.

14. FORBIDDEN QUERY PATTERNS

The following patterns are permanently forbidden.

Authority Violations
query-generated authority
query-generated truth
query-generated state
Mutation Violations
query-side mutations
query-side writes
query-side corrections
Business Violations
query-owned billing decisions
query-owned payment decisions
query-owned suspension decisions
query-owned restoration decisions
query-owned lifecycle decisions
Visibility Violations
visibility without ownership
hidden queries
undocumented queries
unauthorized visibility
Audit Violations
audit-invisible queries
untraceable queries
anonymous queries
Event Violations
query-generated events
query-generated aggregates
query-generated projections
Operational Violations
manual query corrections
operator-created truth through queries
15. REPLAY-SAFE QUERY DOCTRINE

Queries must remain replay-safe.

Replay execution against reconstructed Read Models must produce identical outputs.

Replay safety requires:

deterministic query definitions
deterministic visibility boundaries
deterministic authorization evaluation
deterministic reconstruction behavior

Queries must remain invariant under replay.

16. FINAL QUERY DOCTRINE
Foundational Rule
Authority establishes truth.

Events propagate truth.

Projections transform truth.

Read Models expose truth.

Queries retrieve visibility.

APIs distribute visibility.

Dashboards visualize visibility.

Reports summarize visibility.
Absolute Rule
Queries never create truth.

Queries never own truth.

Queries never modify truth.

Queries never replace authority.

Queries only retrieve visibility
from authoritative Read Models.

This contract becomes the authoritative foundation governing all future:

API architecture
dashboard architecture
reporting architecture
search architecture
operational visibility architecture
external integrations
analytics systems
administrative portals

across the entire ISP Billing & AAA Infrastructure without requiring future architectural redesign.
