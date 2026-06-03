# EVENT_SCHEMA_CONTRACT.md

## 1. Purpose

This document defines the authoritative event schema contract for the ISP Billing + AAA Infrastructure.

This contract governs event ownership, event identity, event lifecycle, event authority, event publication doctrine, event consumption doctrine, replay-safe behavior, deterministic reconstruction, historical preservation, and cross-domain truth propagation.

This contract does not define implementation schemas, database tables, event payloads, queue subjects, broker configuration, API endpoints, or code.

---

## 2. Contract Inheritance

This contract inherits all authority definitions from `SYSTEM_AUTHORITY_CONTRACT.md`.

This contract inherits all runtime definitions from `INFRASTRUCTURE_RUNTIME.md`.

This contract inherits all reconciliation definitions from `ACCOUNTING_RECONCILIATION_CONTRACT.md`.

This contract inherits all billing definitions from `BILLING_CORE_CONTRACT.md`.

This contract inherits all payment definitions from `PAYMENT_LIFECYCLE_CONTRACT.md`.

This contract inherits all suspension definitions from `SUSPENSION_ORCHESTRATION_CONTRACT.md`.

This contract inherits all restoration definitions from `RESTORATION_ORCHESTRATION_CONTRACT.md`.

This contract inherits all audit definitions from `OPERATIONAL_AUDIT_CONTRACT.md`.

This contract inherits all event definitions from `EVENT_BUS_CONTRACT.md`.

This contract inherits all Telegram operations definitions from `TELEGRAM_OPERATIONS_CONTRACT.md`.

This contract inherits all Multi-NAS definitions from `MULTI_NAS_SCALING_CONTRACT.md`.

This contract inherits all domain model definitions from `DOMAIN_MODEL_CONTRACT.md`.

This contract inherits all entity state machine definitions from `ENTITY_STATE_MACHINE_CONTRACT.md`.

This contract inherits all data model definitions from `DATA_MODEL_CONTRACT.md`.

---

# 3. Event Doctrine

## 3.1 Event Definition

An event is an immutable historical fact emitted after an authoritative domain decision has already occurred.

An event communicates truth.

An event does not create truth.

An event records that an authority-owned state transition, lifecycle decision, evidence recognition, enforcement action, restoration action, operational command, or audit-visible fact has occurred.

## 3.2 Event Non-Definition

An event is not:

* an authority source
* a command
* a mutable record
* a business decision engine
* a substitute for aggregate ownership
* a replacement for domain state
* a projection-owned fact
* a consumer-created authority
* an infrastructure-created business truth

## 3.3 Authority Doctrine

Authority exists before events exist.

Events may only be emitted by systems that already possess publication authority for the domain fact being communicated.

No event consumer gains decision authority by receiving an event.

No projection gains authority by replaying events.

No infrastructure component may create business authority through event publication.

## 3.4 Immutability Doctrine

Published events are immutable.

A published event may never be modified, rewritten, replaced, partially edited, silently corrected, or destructively upgraded.

Correction must occur through new authoritative events emitted by the proper authority owner.

## 3.5 Replay Doctrine

Replay reconstructs historical truth.

Replay does not create new authority.

Replay does not approve payments, suspend subscribers, restore services, migrate NAS placement, mutate billing state, or create operational decisions.

Replay may only rebuild derived state from historically valid authoritative events.

---

# 4. Event Ownership Model

Every event must have defined ownership.

No event may exist without:

* event owner
* authority origin
* publication owner
* producer owner
* retention owner
* replay owner
* audit visibility owner

## 4.1 Event Owner

The event owner is the domain authority responsible for the fact represented by the event.

The event owner owns semantic correctness.

## 4.2 Publication Owner

The publication owner controls whether the event may be published.

Publication ownership belongs to the authority-bearing domain, not to the transport layer.

## 4.3 Producer Owner

The producer owner is the system component permitted to emit the event on behalf of the event owner.

A producer may emit only within its assigned authority boundary.

## 4.4 Retention Owner

The retention owner is responsible for ensuring the event remains available for audit, replay, reconciliation, historical reconstruction, and forensic investigation.

## 4.5 Replay Owner

The replay owner governs controlled reconstruction from historical events.

Replay ownership does not imply mutation authority over source domains.

## 4.6 Audit Owner

The audit owner governs visibility, traceability, investigation, and forensic interpretation of events.

Audit ownership does not grant authority to alter events.

---

# 5. Event Identity Doctrine

## 5.1 Event Identity

Every event must be uniquely identifiable.

Event identity must remain stable for the lifetime of the system.

Event identity must support traceability, replay, audit, deduplication, ordering, reconstruction, and forensic investigation.

## 5.2 Uniqueness

No two historical facts may share the same event identity.

No single historical fact may be represented by multiple competing authority events.

## 5.3 Persistence

Event identity must persist across:

* publication
* storage
* replay
* archival
* audit inspection
* projection reconstruction
* disaster recovery

## 5.4 Traceability

Every event identity must be traceable to:

* authority origin
* owning domain
* producing component
* affected aggregate
* causal operation
* publication time
* audit context

## 5.5 Reconstruction Identity

Replay and reconstruction must preserve original event identity.

Replay-generated records must never receive new authority identity that implies a new decision occurred.

---

# 6. Event Lifecycle Model

## 6.1 Event Creation

Event creation occurs only after the authoritative decision or factual recognition has been completed by the proper authority owner.

Events must not be pre-created as pending authority.

## 6.2 Event Publication

Event publication communicates an already-authorized fact to other systems.

Publication must be deterministic, traceable, and authority-safe.

## 6.3 Event Persistence

Published events must be preserved as immutable historical records.

Persistence must support replay, audit, reconciliation, and forensic reconstruction.

## 6.4 Event Retention

Retention policies must protect authoritative historical events from premature deletion.

Events required for financial, service, audit, enforcement, payment, settlement, migration, or subscriber reconstruction must never be destroyed while the system remains operationally dependent on them.

## 6.5 Event Replay

Replay must consume preserved historical events in deterministic order.

Replay must rebuild derived state without creating new authority.

## 6.6 Event Archival

Archival must preserve identity, ordering, ownership, authority origin, and audit traceability.

Archived events remain authoritative historical evidence.

---

# 7. Authoritative Event Catalog

This catalog defines ownership doctrine only. It does not define payloads.

## 7.1 Subscriber Events

Owner: Subscriber authority domain.

Subscriber events represent authoritative subscriber lifecycle facts.

They may communicate subscriber creation, activation eligibility, identity association, status progression, and subscriber lifecycle closure.

Subscriber events must not be created by Radius, MikroTik, projections, reports, or notification systems.

## 7.2 Service Events

Owner: Service authority domain.

Service events represent authoritative service lifecycle facts.

They may communicate service creation, activation, restriction, restoration, termination, or lifecycle state progression.

Service events must remain consistent with subscriber, billing, suspension, restoration, and NAS placement authority.

## 7.3 Billing Events

Owner: Billing Core.

Billing events represent authoritative financial lifecycle facts.

They may communicate billing cycle creation, debt recognition, billing eligibility changes, billing state transitions, and financial obligation changes.

No infrastructure component may publish billing authority events.

## 7.4 Invoice Events

Owner: Billing Core.

Invoice events represent authoritative invoice lifecycle facts.

They may communicate invoice issuance, invoice state change, invoice cancellation, invoice settlement recognition, or invoice aging.

Invoice events must not be generated by payment gateway callbacks directly.

## 7.5 Payment Events

Owner: Payment Lifecycle authority.

Payment events represent authoritative payment recognition facts.

They may communicate payment initiation recognition, payment evidence validation, payment acceptance, payment rejection, or payment state progression.

Payment gateway evidence is not authority until validated by the payment authority.

## 7.6 Settlement Events

Owner: Payment and reconciliation authority boundary.

Settlement events represent authoritative settlement facts after validated financial reconciliation.

Settlement events must remain consistent with payment, invoice, and billing authority.

## 7.7 Provisioning Events

Owner: Provisioning Lifecycle authority.

Provisioning events represent authoritative provisioning lifecycle facts.

They may communicate subscriber/service provisioning decisions, provisioning completion, rollback recognition, or provisioning failure.

Provisioning events must not override billing or subscriber authority.

## 7.8 Accounting Events

Owner: Accounting Engine and reconciliation authority boundary.

Accounting events represent infrastructure evidence facts.

They may communicate session start evidence, session stop evidence, interim evidence, stale session recognition, ghost session recognition, duplicate evidence recognition, or reconciliation findings.

Accounting events are evidence events, not business authority events.

## 7.9 Suspension Events

Owner: Suspension Orchestration authority.

Suspension events represent authoritative restriction lifecycle facts.

They may communicate suspension decision, enforcement request, enforcement confirmation, enforcement failure, or restriction state progression.

MikroTik execution does not own suspension authority.

## 7.10 Restoration Events

Owner: Restoration Orchestration authority.

Restoration events represent authoritative reactivation lifecycle facts.

They may communicate restoration decision, enforcement removal, verification result, restoration completion, or restoration failure.

Restoration events must remain consistent with billing and service eligibility.

## 7.11 NAS Placement Events

Owner: Multi-NAS placement authority.

NAS placement events represent authoritative subscriber-to-NAS placement facts.

They may communicate placement assignment, placement change, migration intent, migration completion, or placement invalidation.

NAS placement events must not be inferred from live accounting alone.

## 7.12 Migration Events

Owner: Multi-NAS migration authority.

Migration events represent authoritative infrastructure movement facts.

They may communicate migration approval, migration execution, migration verification, rollback, or migration failure.

Migration events must remain consistent with accounting and service continuity.

## 7.13 Operator Events

Owner: Telegram Operations and operational authority boundary.

Operator events represent human-to-system operational facts.

They may communicate command receipt, approval, rejection, execution request, execution result, or operator-visible decision.

Operator events must always be audit-visible.

## 7.14 Audit Events

Owner: Operational Audit authority.

Audit events represent forensic, traceability, investigation, and accountability facts.

Audit events may describe what occurred, who initiated it, which authority allowed it, and which system executed it.

Audit events do not mutate business authority.

## 7.15 Notification Events

Owner: Notification authority boundary.

Notification events represent communication lifecycle facts.

They may communicate notification request, delivery attempt, delivery result, or failure.

Notification events must not become proof of business state.

---

# 8. Event Publication Model

## 8.1 Publication Authority

Only the authority owner of a domain fact may authorize event publication for that fact.

A producer may publish only if delegated by the authority owner.

## 8.2 Publication Validation

Before publication, the producing system must validate:

* authority origin exists
* event owner is defined
* aggregate boundary is respected
* state transition is already authorized
* event identity is unique
* publication does not create authority
* publication is audit-traceable

## 8.3 Publication Timing

Events must be published after authority decisions are committed.

Events must not be published as speculative future truth.

Events must not be published before the corresponding authoritative state exists.

## 8.4 Publication Guarantees

Publication must support:

* deterministic visibility
* traceable origin
* replay safety
* duplicate detection
* consumer idempotency
* audit reconstruction

## 8.5 Forbidden Publishers

The following must not publish authority events unless explicitly delegated by the owning authority contract:

* projections
* reports
* dashboards
* Telegram notification handlers
* MikroTik
* FreeRADIUS
* payment gateways
* external callbacks
* replay workers
* archival systems

---

# 9. Event Consumption Model

## 9.1 Consumer Ownership

Every consumer must have an owning domain or operational purpose.

No anonymous consumer may participate in event consumption.

## 9.2 Consumer Authority

Consumers may use events to:

* update derived projections
* trigger permitted workflows
* create audit visibility
* support reconciliation
* notify operators
* rebuild read models
* detect inconsistencies

Consumers may not use events to create authority outside their assigned domain.

## 9.3 Consumer Restrictions

Consumers must never:

* reinterpret event authority
* mutate source aggregate ownership
* create business truth from projection state
* generate circular authority
* silently ignore authority conflicts
* treat missing events as approval
* treat duplicate events as new facts
* overwrite immutable history

## 9.4 Consumer Responsibilities

Consumers are responsible for:

* idempotent handling
* order-aware processing
* duplicate detection
* stale event detection
* authority boundary validation
* failure visibility
* replay compatibility

---

# 10. Event Replay Model

## 10.1 Replay Ownership

Replay is owned by the reconstruction authority defined by the relevant domain and audit contracts.

Replay workers do not own business authority.

## 10.2 Replay Authority

Replay authority permits reconstruction only.

Replay authority does not permit new business decisions.

## 10.3 Replay Reconstruction

Replay must reconstruct derived state from historical event facts.

Replay must preserve historical ordering, identity, and authority origin.

## 10.4 Replay Ordering

Replay must respect deterministic ordering rules.

Ordering must be sufficient to reconstruct subscriber state, service state, billing state, invoice state, payment state, settlement state, accounting state, suspension state, restoration state, NAS placement state, migration state, and audit history.

## 10.5 Replay Validation

Replay must validate:

* event identity
* event immutability
* event ownership
* authority origin
* ordering requirements
* historical compatibility
* aggregate boundary consistency
* missing-event conditions
* duplicate-event conditions

## 10.6 Replay Recovery

Replay recovery may rebuild projections, reports, audit views, reconciliation views, and derived operational views.

Replay recovery must not generate new authority events unless a proper authority owner explicitly emits a new corrective event.

---

# 11. Event Versioning Doctrine

## 11.1 Event Evolution

Event definitions may evolve only through compatible extension.

Event evolution must not invalidate historical events.

## 11.2 Compatibility Doctrine

All future event schema evolution must preserve:

* historical readability
* replay compatibility
* audit interpretability
* reconstruction compatibility
* authority origin traceability
* ownership semantics

## 11.3 Historical Preservation

Historical events remain valid forever.

Old events must not be rewritten to match new definitions.

## 11.4 Replay Compatibility

Replay systems must understand historical event versions without destructive migration.

## 11.5 Reconstruction Compatibility

Reconstruction must remain deterministic across event versions.

Version evolution must not change the historical meaning of an already-published event.

---

# 12. Event Consistency Model

## 12.1 Subscriber ↔ Service

Service events must reference subscriber authority consistently.

A service lifecycle cannot exist without valid subscriber authority.

## 12.2 Service ↔ Billing

Billing eligibility must remain consistent with service lifecycle authority.

Billing events must not imply service activation unless service authority confirms activation.

## 12.3 Billing ↔ Invoice

Invoice events must be derived from Billing Core authority.

Invoices must not create independent billing authority.

## 12.4 Invoice ↔ Payment

Payment recognition must resolve against authoritative invoice state.

Payment events must not mutate invoices directly without Billing Core recognition.

## 12.5 Payment ↔ Settlement

Settlement events must be consistent with validated payment state and reconciliation authority.

Settlement must not exist as independent payment truth.

## 12.6 Suspension ↔ Service

Suspension events may restrict service state only through Suspension Orchestration authority.

Service restriction must remain reconstructable from suspension history.

## 12.7 Restoration ↔ Service

Restoration events may restore service only through Restoration Orchestration authority and service eligibility validation.

Restoration must not bypass billing authority.

## 12.8 Subscriber ↔ Accounting

Accounting events may describe subscriber session evidence.

Accounting events must not create subscriber lifecycle authority.

## 12.9 Subscriber ↔ NAS

NAS placement events define authoritative placement.

Live NAS evidence may support reconciliation but must not independently redefine placement.

## 12.10 Migration ↔ Accounting

Migration events must remain consistent with accounting continuity.

Accounting evidence may validate migration outcome but must not authorize migration.

## 12.11 Operator ↔ Audit

Operator events must be audit-visible.

Audit events must preserve operator identity, command context, approval path, execution result, and authority boundary.

---

# 13. Event Reconstruction Model

## 13.1 Subscriber Reconstruction

Subscriber reconstruction must use subscriber-owned lifecycle events and related audit evidence.

Infrastructure evidence must not recreate subscriber authority.

## 13.2 Service Reconstruction

Service reconstruction must use service lifecycle events, provisioning events, suspension events, restoration events, and authority-valid billing eligibility events.

## 13.3 Billing Reconstruction

Billing reconstruction must use Billing Core authority events only.

Payment and settlement events may inform billing outcomes only through valid authority integration.

## 13.4 Invoice Reconstruction

Invoice reconstruction must use invoice authority events owned by Billing Core.

Gateway evidence must not reconstruct invoice authority directly.

## 13.5 Payment Reconstruction

Payment reconstruction must use payment authority events, validated evidence events, settlement events, and reconciliation findings.

## 13.6 Accounting Reconstruction

Accounting reconstruction must use accounting evidence events, reconciliation events, and runtime evidence ordering.

Accounting reconstruction must remain evidence-based.

## 13.7 NAS Reconstruction

NAS reconstruction must use NAS placement events, migration events, provisioning events, and reconciliation evidence.

Live runtime state must not overwrite historical placement authority.

## 13.8 Audit Reconstruction

Audit reconstruction must use audit events, operator events, domain authority events, publication metadata, execution evidence, and historical lineage.

Audit reconstruction must support forensic explanation of why, when, by whom, and under which authority an event occurred.

---

# 14. Event Audit Model

## 14.1 Event Traceability

Every event must be traceable to its authority source, producer, aggregate, lifecycle context, and audit lineage.

## 14.2 Event Lineage

Event lineage must preserve causal relationship between:

* operator action
* system command
* authority decision
* publication
* infrastructure execution
* reconciliation evidence
* audit observation

## 14.3 Event Visibility

Audit systems must have visibility into all authority-bearing events and operationally significant evidence events.

## 14.4 Event Investigation

Event investigation must support reconstruction of:

* what happened
* when it happened
* which authority allowed it
* which system produced it
* which aggregate was affected
* which consumers observed it
* whether replay can reproduce it

## 14.5 Forensic Reconstruction

Forensic reconstruction must remain possible even after projections, read models, dashboards, or caches are lost.

Immutable events and audit records must remain the reconstruction foundation.

---

# 15. Event Retention Model

## 15.1 Retention Ownership

Retention ownership belongs to the authority domain and audit domain responsible for preserving historical truth.

## 15.2 Archival Ownership

Archival ownership must ensure events remain readable, traceable, replayable, and audit-valid.

## 15.3 Preservation Ownership

Preservation ownership applies to all events required for:

* subscriber reconstruction
* service reconstruction
* billing reconstruction
* invoice reconstruction
* payment reconstruction
* settlement reconstruction
* suspension reconstruction
* restoration reconstruction
* accounting reconstruction
* NAS placement reconstruction
* migration reconstruction
* operator accountability
* audit investigation

## 15.4 Events That May Never Be Destroyed

The following event classes must never be destroyed while the system requires historical reconstruction:

* subscriber lifecycle events
* service lifecycle events
* billing authority events
* invoice authority events
* payment authority events
* settlement authority events
* suspension authority events
* restoration authority events
* NAS placement events
* migration events
* operator command events
* audit events
* reconciliation findings
* authority correction events

---

# 16. Forbidden Event Patterns

The following patterns are prohibited:

* authority-less events
* ownership-less events
* mutable events
* replay-generated authority
* consumer-generated authority
* event-owned business truth
* duplicate authority events
* hidden events
* orphan events
* circular event dependencies
* destructive event versioning
* event mutation after publication
* infrastructure-created business authority
* projection-generated authority
* payment-gateway-created billing authority
* Radius-created subscriber authority
* MikroTik-created service authority
* notification-created operational truth
* dashboard-created authority
* replay-created payment settlement
* stale-event acceptance without validation
* missing-event assumptions as approval
* event publication before authority commit

---

# 17. Replay-Safe Event Doctrine

## 17.1 Deterministic Replay

Replay must produce the same reconstructed state when given the same valid historical event sequence.

## 17.2 Idempotent Replay

Reprocessing the same event must not create duplicate state, duplicate authority, duplicate financial recognition, duplicate suspension, duplicate restoration, or duplicate audit facts.

## 17.3 Ordering Guarantees

Ordering must be sufficient to preserve lifecycle causality.

Domain ordering must be stronger than transport ordering.

## 17.4 Recovery Guarantees

Recovery must rebuild derived state without relying on hidden runtime memory, manual correction, cache state, dashboard state, or external infrastructure assumptions.

## 17.5 Missing-Event Recovery

Missing-event detection must produce audit-visible inconsistency.

Missing events must not be silently inferred as valid authority.

## 17.6 Stale-Event Recovery

Stale events must be detected against current reconstruction context.

Stale events may be preserved historically but must not override newer valid authority.

## 17.7 Authority Validation During Replay

Replay must validate that each event was originally emitted by the proper authority owner or delegated producer.

Replay must reject authority reconstruction from invalid event origins.

---

# 18. Final Event Schema Doctrine

The ISP Billing + AAA Infrastructure shall treat events as immutable, owned, traceable, replayable historical facts.

Events shall communicate authority decisions.

Events shall never create authority.

Every event shall have a defined owner, producer, publication authority, consumption boundary, retention owner, replay owner, and audit visibility path.

Event publication shall occur only after the authoritative decision exists.

Event consumers shall remain subordinate to domain authority.

Event replay shall reconstruct truth without creating new decisions.

Historical events shall remain valid forever.

Event versioning shall preserve replay, reconstruction, audit, and semantic compatibility.

No projection, consumer, infrastructure component, notification handler, replay worker, or dashboard shall become an authority source through event processing.

This contract is the authoritative foundation for all future event stores, event schemas, event buses, messaging infrastructure, projections, reporting systems, replay systems, audit systems, and implementation layers of the ISP Billing + AAA Infrastructure.

