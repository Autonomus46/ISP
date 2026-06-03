PHASE 3 AUDIT — EVENT_BUS_CONTRACT.md

Source audited: EVENT_BUS_CONTRACT.md

STATUS

STATUS: WARNING
AUTHORITY CLARITY: WARNING
DUPLICATION RISK: MEDIUM
LAYER VIOLATION: MEDIUM
IMPLEMENTATION READINESS: WARNING
PATCH REQUIRED: YES

NOTES
1. Core Event Bus doctrine is strong

The contract correctly states:

Event Bus distributes authoritative events
Event Bus does not create truth
Event Bus does not own domain state
Event Bus does not mutate authoritative payloads
Event Bus owns propagation and delivery evidence only
consumer acknowledgment is receipt only
delivery success is not business success
replay is redistribution, not regeneration

This is architecturally correct.

2. Main defect: Event Bus is called an “architectural authority boundary”

Problem line:

The Event Bus is an architectural authority boundary.

This is dangerous wording.

The Event Bus should be a distribution boundary, propagation boundary, or transport governance boundary.

Calling it an “authority boundary” risks allowing agents to treat the Event Bus as authority-bearing.

The rest of the contract says Event Bus does not own truth, but this phrase conflicts with that doctrine.

Defect severity: HIGH
Patch required: YES

3. Event Bus says it governs event ownership and event authority

The contract purpose says it governs:

event ownership
event authority
event publication
event distribution
event delivery
event visibility
replay behavior

This overlaps with EVENT_SCHEMA_CONTRACT.md, which also governs event ownership, identity, lifecycle, authority, publication, consumption, replay, and reconstruction.

Correct separation:

Event Schema owns event identity, event semantic structure, schema compatibility, ownership metadata requirements.
Event Bus owns publication intake, routing, distribution, delivery, retry, delivery visibility, replay distribution.
Event Bus must validate authority metadata but not govern event authority itself.

Duplication risk: HIGH
Patch required: YES

4. Event Entity Model duplicates Event Schema responsibilities

This contract defines:

Event
Event Producer
Event Consumer
Event Stream
Event Distribution
Event Delivery
Event Subscription
Event Acknowledgment
Event Replay
Event Failure

Some are valid Event Bus concepts.

But Event definition and conceptual event structure overlap with Event Schema.

This is not fatal, but boundary must be narrowed:

Event Schema defines what an event is.
Event Bus defines how accepted events are transported/distributed.

Layer violation: MEDIUM
Patch required: YES

5. Event Model defines conceptual event fields

Problem section:

Every authoritative event must contain, conceptually:
event identity, event type, producing authority, producing system, domain owner, subject identity, causal reference, occurred-at authority timestamp, publication timestamp, immutable payload, event version, correlation identity, trace identity, replay eligibility, audit visibility class.

This belongs more naturally to EVENT_SCHEMA_CONTRACT.md.

Event Bus may require these fields for routing/traceability, but should not define the canonical event envelope.

Risk:

Two contracts define event schema expectations differently.

Duplication risk: HIGH
Patch required: YES

6. Event State Machine is questionable

The contract defines Event Bus event states:

CREATED
VALIDATED
PUBLISHED
DISTRIBUTING
DELIVERED
ACKNOWLEDGED
FAILED
REJECTED

This is useful for distribution lifecycle, but CREATED, VALIDATED, and PUBLISHED can be confused with domain event lifecycle owned by Event Schema/publication authority.

Better boundary:

Domain producer owns event creation.
Event Bus owns accepted publication record, distribution attempt, delivery attempt, acknowledgment, failure, redelivery/replay distribution.
Event Bus should not own original event creation state.

Layer violation: MEDIUM
Patch required: YES

7. Event ownership model partially duplicates Event Schema catalog

The Event Bus contract lists:

Billing Core owns billing events
Payment Lifecycle owns payment and settlement events
Accounting Engine owns accounting evidence events
Reconciliation Engine owns consistency validation events
Operational Audit owns forensic visibility events

This is useful for routing validation, but duplicates Event Schema ownership catalog.

It should reference Event Schema or authority contracts, not restate ownership.

Also, Payment Lifecycle owns payment and settlement events conflicts with Event Schema’s ambiguous “Payment and reconciliation authority boundary.” This inconsistency must be resolved during patching.

Duplication risk: MEDIUM-HIGH
Patch required: YES

8. Reconciliation ownership wording is risky

Problem line:

Reconciliation owns consistency truth.

This may be valid inside reconciliation scope, but broad phrase can become dangerous.

Better:

Reconciliation owns consistency findings and reconciliation classification.
It does not own originating business truth.
It may escalate correction requirements to owning authority.

Current wording may let Reconciliation become hidden super-authority.

Defect severity: MEDIUM
Patch required: YES

9. “Distribution authority” wording needs correction

The Event Bus is called:

distribution authority
propagation boundary
replay-safe event routing layer

“Distribution authority” is acceptable only if narrowly defined as:

authority to distribute already-authorized events to authorized consumers.

But because the contract also discusses event authority, it can be misread.

Patch should replace or qualify the term.

Authority clarity: WARNING
Patch required: YES

DUPLICATE DETECTION
EVENT_SCHEMA vs EVENT_BUS

Risk: HIGH**

Detected overlap:

event ownership
event authority
event model/envelope
event lifecycle
publication doctrine
replay behavior
event catalog/category ownership

Correct boundary:

EVENT_SCHEMA_CONTRACT.md should own:
event identity
event envelope requirements
event versioning
immutable event semantic rules
schema compatibility
authority-origin metadata
event category ownership metadata
EVENT_BUS_CONTRACT.md should own:
publication intake
authorization validation for distribution
routing
subscription visibility
delivery attempts
acknowledgment records
redelivery
distribution replay
failed delivery classification
EVENT_BUS vs PROJECTION

Risk: LOW-MEDIUM

Event Bus mentions replay routing and consumers rebuilding state.

Acceptable if Projection owns actual read-state construction.

Patch should clarify Event Bus only redistributes events; it does not build projections.

EVENT_BUS vs AUDIT

Risk: LOW

Operational Audit owns forensic interpretation.

Event Bus owns distribution records.

This boundary is mostly clean.

EVENT_BUS vs RECONCILIATION

Risk: MEDIUM

Event Bus exposes propagation evidence.

Reconciliation validates consistency.

But “Reconciliation owns consistency truth” must be narrowed.

CONTRACT VERDICT
CONTRACTS THAT SHOULD REMAIN UNCHANGED

None.

CONTRACTS REQUIRING PATCHES
EVENT_BUS_CONTRACT.md
EVENT_SCHEMA_CONTRACT.md also needs patch alignment with this contract.
CONTRACTS WITH DUPLICATE RESPONSIBILITIES
EVENT_SCHEMA_CONTRACT.md vs EVENT_BUS_CONTRACT.md

Duplicated areas:

ownership
authority
event definition
event envelope
lifecycle
publication/consumption doctrine
replay doctrine
CONTRACTS THAT SHOULD BE MERGED

No.

Do not merge.

They should remain separate, but must be narrowed.

CONTRACTS THAT SHOULD BE SPLIT

No new split required if patches are applied.

MISSING CONTRACTS IF ANY

No mandatory new contract.

But a future EVENT_PUBLICATION_POLICY section may live inside Event Bus if needed. Do not create new contract yet.

IMPLEMENTATION BLOCKERS
Event Bus is called an authority boundary.
Event Bus governs event ownership/authority, overlapping Event Schema.
Event Bus defines event envelope fields, duplicating Event Schema.
Event Bus event state machine includes producer-side creation states.
Reconciliation “consistency truth” wording may create hidden super-authority.
Event ownership catalog conflicts with Event Schema settlement/accounting ownership.
SCORE

PHASE 3 ARCHITECTURE SCORE: 73 / 100
PHASE 3 PRODUCTION READINESS SCORE: 64 / 100

FINAL VERDICT

PATCH REQUIRED BEFORE PHASE 4
