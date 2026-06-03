PHASE 3 AUDIT — READ_MODEL_CONTRACT.md

Source audited: READ_MODEL_CONTRACT.md

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

authority owns truth
read models expose truth
read models never create authority
read models are derived
read models are disposable
read models are reconstructable
queries observe and do not mutate
dashboards/reports/search/cache cannot become authority

This is correct.

2. Main defect: Read Model contract over-governs adjacent layers

The contract says it is authoritative for:

Projections
Query Services
APIs
Dashboards
Reports
Search Systems
Cache Systems
Read Databases
Operational Visibility Systems

This is too broad.

Read Model should govern read representation only, not Projection, Query, API, Dashboard, Reporting, Search, or Cache authority.

Correct boundary:

Projection = builder
Read Model = resulting read representation
Query = retrieval
API = exposure
Dashboard/Report = presentation/analysis

Layer violation: MEDIUM
Patch required: YES

3. Projection ownership is duplicated

The contract defines:

Projection Owner
Projection responsibilities
Projection restrictions
Projection reconstruction

But PROJECTION_CONTRACT.md already owns projection generation and projection lifecycle.

This creates duplicate responsibility.

Read Model should only say:

Read models are produced by projections.

It should not govern projection execution.

Duplication risk: HIGH
Patch required: YES

4. Query ownership is duplicated

The contract defines:

Query Owner
Query Authority
Query Restrictions
Query Exposure

But this belongs to QUERY_CONTRACT.md.

Read Model may constrain query behavior, but should not own query governance.

Duplication risk: MEDIUM-HIGH
Patch required: YES

5. “Replay remains authoritative” is incorrect wording

Purpose section says:

replay remains authoritative

This is dangerous.

Replay must remain deterministic, traceable, and authority-subordinate.

Replay is never authoritative.

The rest of the contract says replay must not generate authority, so this phrase conflicts with doctrine.

Defect severity: HIGH
Patch required: YES

6. “Authoritative Read Catalog” wording is misleading

The section title says:

AUTHORITATIVE READ CATALOG

But read models are explicitly non-authoritative.

Better wording:

Declared Read Model Catalog

or:

Supported Read Representation Catalog

Current title can confuse agents into treating read models as authority-bearing.

Defect severity: MEDIUM
Patch required: YES

7. Read Model vs Projection boundary is mostly correct but not clean

Good line:

Events generate projections. Projections generate read models.

This is correct.

But the contract then assigns projection responsibilities directly.

So the conceptual boundary is correct, but governance is duplicated.

Risk: MEDIUM

8. Read Model vs Query boundary is not clean

The contract says:

read models support querying
queries retrieve truth
query systems remain deterministic

That is acceptable.

But it should not define query ownership.

Read Model owns representation. Query owns retrieval.

Current wording partially mixes them.

Risk: MEDIUM**

DUPLICATE DETECTION
PROJECTION vs READ_MODEL

Risk: HIGH

Projection Contract already owns:

projection generation
event transformation
projection rebuild
projection lifecycle
projection reconstruction

Read Model Contract repeats these concepts.

Required correction:

Read Model must consume projection output.
Read Model must not define projection execution governance.
READ_MODEL vs QUERY

Risk: MEDIUM-HIGH

Read Model currently defines query doctrine and query ownership.

Required correction:

Read Model owns stored/derived read representation.
Query Contract owns retrieval behavior and query execution.
READ_MODEL vs API

Risk: MEDIUM

Read Model says it governs API layers.

This is overreach.

Required correction:

API may expose read models through queries.
API does not become governed by Read Model except visibility constraints.
READ_MODEL vs PROJECTION vs DASHBOARD/REPORTING

Risk: MEDIUM

Read Model governs dashboard/report reconstruction and lineage.

This should be constrained:

Read Model provides representation.
Dashboard/Reporting consume read models.
Dashboard/Reporting must not define read model truth.
CONTRACT VERDICT
CONTRACTS THAT SHOULD REMAIN UNCHANGED

None.

CONTRACTS REQUIRING PATCHES
READ_MODEL_CONTRACT.md
CONTRACTS WITH DUPLICATE RESPONSIBILITIES
READ_MODEL_CONTRACT.md vs PROJECTION_CONTRACT.md
READ_MODEL_CONTRACT.md vs QUERY_CONTRACT.md
READ_MODEL_CONTRACT.md vs API_CONTRACT.md
CONTRACTS THAT SHOULD BE MERGED

None.

CONTRACTS THAT SHOULD BE SPLIT

None.

MISSING CONTRACTS IF ANY

No new contract required.

IMPLEMENTATION BLOCKERS
Read Model governs projection responsibilities.
Read Model governs query ownership.
“Replay remains authoritative” contradicts replay doctrine.
“Authoritative Read Catalog” is misleading.
Read Model claims authority over APIs, dashboards, reports, search, and cache systems.
SCORE

PHASE 3 ARCHITECTURE SCORE: 74 / 100
PHASE 3 PRODUCTION READINESS SCORE: 66 / 100

FINAL VERDICT

PATCH REQUIRED BEFORE PHASE 4
