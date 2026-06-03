REPORTING_CONTRACT.md
1. REPORTING DOCTRINE
1.1 Reporting Purpose

Reporting Infrastructure exists solely to transform authorized visibility into reproducible reporting artifacts.

Reporting Infrastructure does not own:

business truth
operational truth
authority
permissions
state ownership
subscriber ownership
payment ownership
billing ownership
enforcement ownership

Reporting Infrastructure owns only:

report generation
report reconstruction
report presentation artifacts
report attribution
report retention governance
report traceability
report auditability
1.2 Core Reporting Doctrine

Authority owns truth.

Events preserve history.

Projections transform events.

Read Models provide visibility.

Queries retrieve visibility.

Search discovers visibility.

Reporting generates presentation-ready visibility artifacts.

Reports are derived artifacts only.

Reports never become authoritative records.

Reports never become business truth.

Reports never become operational truth.

Reports never become system authority.

All reporting outputs must be reproducible from authoritative sources.

2. REPORT OWNERSHIP MODEL

Defines ownership boundaries for:

report definitions
report generation workflows
report schedules
report versions
report metadata
report attribution records
report retention records

Reporting Infrastructure owns reporting artifacts.

Reporting Infrastructure does not own source data.

Source data ownership remains with authoritative domains.

3. REPORT AUTHORITY MODEL

Defines authoritative sources allowed to participate in reporting generation.

Allowed authorities:

Billing Core
Payment Lifecycle
Radius Policy Engine
Provisioning Lifecycle
Suspension Orchestration
Restoration Orchestration
Subscriber Domain
Session Domain
Audit Domain
Identity Domain
Authorization Domain

Reports may consume authority.

Reports never create authority.

4. REPORT VISIBILITY MODEL

Defines visibility inheritance chain.

Visibility Flow:

Authority
→ Events
→ Projections
→ Read Models
→ Queries
→ Search
→ Reporting

Reports may only expose visibility already granted by upstream authorization systems.

Reporting Infrastructure may never expand visibility.

5. REPORT GENERATION MODEL

Defines deterministic report generation behavior.

Report generation must always declare:

generation timestamp
source read models
query identity
authorization context
visibility scope
reconstruction references

Generation must be reproducible.

Equivalent inputs must produce equivalent reporting outputs.

6. REPORT RECONSTRUCTION MODEL

Defines reconstruction authority.

Reports must be reconstructable from:

Events
Projections
Read Models
Query Definitions
Search Visibility
Authorization State
Identity State

Historical report files are never required for reconstruction.

Historical report files are convenience artifacts only.

7. REPORT CONSISTENCY MODEL

Defines reporting consistency guarantees.

Consistency requirements:

source consistency
authorization consistency
visibility consistency
attribution consistency
reconstruction consistency

Reports generated from identical visibility states must remain identical.

8. REPORT ATTRIBUTION MODEL

Every report must attribute:

generating identity
generating service
generation time
visibility scope
authorization scope
report version
source dependencies

Anonymous reporting generation is forbidden.

9. REPORT AUTHORIZATION MODEL

Reporting authorization is delegated to:

Identity Infrastructure
Authorization Infrastructure

Reporting Infrastructure never manages permissions.

10. REPORT ACCESS CONTROL MODEL

Access control inheritance:

Identity
→ Authorization
→ Query
→ Reporting

Reports may never bypass authorization boundaries.

11. REPORT RETENTION MODEL

Defines retention governance.

Retention applies to:

generated reports
report metadata
report audit records
report attribution records

Retention does not replace event retention.

Events remain the authoritative historical record.

12. REPORT VERSIONING MODEL

Defines:

report definition versions
report generation versions
reconstruction versions
attribution versions

Versioning must be immutable.

Historical versions remain reconstructable.

13. REPORT LIFECYCLE MODEL

Lifecycle:

Draft Definition
→ Approved Definition
→ Active Generation
→ Distributed
→ Archived
→ Expired

Lifecycle never affects business state.

14. REPORT DISTRIBUTION MODEL

Defines controlled distribution.

Supported consumers:

operators
administrators
auditors
subscribers
integrations

Distribution inherits visibility boundaries.

15. REPORT EXPORT MODEL

Exports are derived artifacts.

Exports never become authoritative records.

Export formats are implementation concerns and outside this contract.

16. REPORT AUDIT MODEL

Every report action must be auditable.

Audit coverage:

generation
access
export
distribution
reconstruction
archival
deletion
17. REPORT TRACEABILITY MODEL

Every report must trace back to:

source queries
source read models
source projections
source events
authorization context

End-to-end lineage must exist.

18. REPORT INVESTIGATION MODEL

Defines forensic investigation behavior.

Investigators must be able to reconstruct:

visibility scope
authorization scope
source state
report output

for any historical report generation event.

19. REPORT OPERATOR MODEL

Defines operator reporting visibility.

Operators receive only authorized operational visibility.

Operator reports may never expose unauthorized subscriber information.

20. REPORT SUBSCRIBER MODEL

Defines subscriber-facing reports.

Subscribers may only access:

self-owned visibility
self-owned billing visibility
self-owned payment visibility
self-owned session visibility

Cross-subscriber visibility is forbidden.

21. REPORT TELEGRAM MODEL

Defines Telegram reporting behavior.

Telegram reports are notification artifacts.

Telegram messages never become authoritative records.

22. REPORT AUTOMATION MODEL

Automation may:

trigger report generation
trigger report distribution
trigger report archival

Automation may never alter authority.

23. REPORT INTEGRATION MODEL

External integrations consume reporting outputs.

Integrations never gain authority through reporting.

24. REPORT API CONSUMER MODEL

API consumers receive reporting visibility only.

Reporting APIs never become authority APIs.

25. REPORT SERVICE MODEL

Defines reporting services.

Services generate reports.

Services do not own truth.

26. REPORT INFRASTRUCTURE MODEL

Defines reporting infrastructure responsibilities.

Infrastructure provides:

generation
storage
distribution
reconstruction

Infrastructure does not define authority.

27. REPORT EVENT RECONSTRUCTION MODEL

Historical reports must be reproducible through:

Events
→ Projections
→ Read Models
→ Queries
→ Reporting

28. REPORT READ MODEL DEPENDENCY MODEL

Read Models remain authoritative visibility sources.

Reports consume Read Models.

Reports never replace Read Models.

29. REPORT QUERY DEPENDENCY MODEL

Reports depend on deterministic queries.

Queries remain authoritative retrieval mechanisms.

30. REPORT SEARCH DEPENDENCY MODEL

Search remains discovery infrastructure.

Reports may consume search visibility.

Search never consumes reports as authority.

31. REPORT FAILURE RECOVERY MODEL

Recovery must support:

generation failures
attribution failures
reconstruction failures
storage failures
distribution failures

Recovery must remain deterministic.

32. REPORT RETENTION GOVERNANCE MODEL

Defines governance authority over:

retention schedules
archival schedules
deletion policies

Governance never modifies authority records.

33. REPORT ARCHIVAL MODEL

Archived reports remain:

attributable
auditable
reconstructable

Archived reports never become authoritative history.

Events remain authoritative history.

34. REPORT VERSION GOVERNANCE MODEL

Defines:

version approval
version promotion
version retirement
version reconstruction

All versions remain auditable.

35. REPORT REPLAY GOVERNANCE MODEL

Replay must reproduce:

report visibility
report attribution
report authorization
report output

Replay may never depend on previously generated reports.

36. FORBIDDEN REPORTING PATTERNS

Explicitly forbidden:

reports as source of truth
report-owned business state
report-owned authority
report-owned permissions
report-owned billing state
report-owned payment state
report-owned enforcement state
report-owned subscriber state
report-owned session state
reports bypassing authorization
reports bypassing access control
reports bypassing visibility boundaries
hidden reports
undocumented reports
report-only records
report-generated truth
report-generated ownership
report-generated authority
cross-tenant report leakage
subscriber visibility escalation
operator visibility escalation
non-reconstructable reports
non-auditable reports
untraceable report generation
report-driven business truth
report-driven enforcement authority
37. REPLAY-SAFE REPORTING DOCTRINE

Equivalent historical system state must generate equivalent reporting outcomes.

Replay must remain:

deterministic
reproducible
attributable
auditable
authorization-safe
38. FORENSIC REPORT RECONSTRUCTION DOCTRINE

Forensic reconstruction must allow investigators to reproduce:

source visibility
authorization scope
report generation context
report output
attribution chain

without requiring historical report artifacts.

39. FINAL REPORTING DOCTRINE

The Reporting Infrastructure is the authoritative owner of reporting artifacts across the ISP Billing & AAA Infrastructure.

Authority remains owned by authoritative domains.

Visibility remains owned by Read Models.

Retrieval remains owned by Query Infrastructure.

Discovery remains owned by Search Infrastructure.

Reporting Infrastructure owns only the generation, attribution, reconstruction, retention, auditing, and presentation of reporting artifacts.

Reports are never truth.

Reports are never authority.

Reports are never ownership.

Reports are visibility products derived from authoritative sources and must remain deterministic, auditable, reconstructable, authorization-safe, replay-safe, and forensically reproducible across all operational domains of the platform.
