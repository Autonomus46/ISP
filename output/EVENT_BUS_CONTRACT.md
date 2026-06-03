# EVENT_BUS_CONTRACT.md

# Event Bus Contract

## ISP Billing & AAA Infrastructure

---

## 1. Contract Purpose

This contract defines the deterministic Event Bus architecture for the ISP Billing & AAA Infrastructure.

This contract governs:

* event ownership
* event authority
* event publication
* event distribution
* event delivery
* event visibility
* event traceability
* event replay behavior
* cross-system event coordination
* failed event handling
* audit-safe event reconstruction

This contract does not define implementation.

This contract does not define any message queue, broker, transport protocol, database schema, API, webhook, Telegram integration, Redis stream, Kafka topic, RabbitMQ exchange, NATS subject, or monitoring system.

The Event Bus is an architectural authority boundary.

---

## 2. Inherited Authority

This contract inherits all authority definitions from:

* `SYSTEM_AUTHORITY_CONTRACT.md`
* `INFRASTRUCTURE_RUNTIME.md`
* `ACCOUNTING_RECONCILIATION_CONTRACT.md`
* `BILLING_CORE_CONTRACT.md`
* `PAYMENT_LIFECYCLE_CONTRACT.md`
* `SUSPENSION_ORCHESTRATION_CONTRACT.md`
* `RESTORATION_ORCHESTRATION_CONTRACT.md`
* `OPERATIONAL_AUDIT_CONTRACT.md`

No definition in this contract may override inherited authority.

If conflict exists, inherited source-of-truth authority wins.

---

## 3. Event Bus Doctrine

The Event Bus is a deterministic event distribution system.

The Event Bus exists to distribute authoritative events produced by authoritative systems.

The Event Bus does not create operational truth.

The Event Bus does not create business truth.

The Event Bus does not create payment truth.

The Event Bus does not create billing truth.

The Event Bus does not create infrastructure truth.

The Event Bus does not create audit truth.

The Event Bus does not decide what happened.

The Event Bus only distributes what an authoritative system has declared happened.

---

## 4. What Event Bus Is

The Event Bus is:

* a distribution authority
* a propagation boundary
* a delivery traceability layer
* a replay-safe event routing layer
* a cross-system visibility mechanism
* a forensic propagation evidence system

The Event Bus owns:

* event publication intake
* event distribution records
* delivery attempts
* consumer delivery visibility
* event propagation trace
* event replay routing behavior
* failed delivery classification

---

## 5. What Event Bus Is Not

The Event Bus is not:

* a business state engine
* a payment state engine
* a billing state engine
* a provisioning engine
* a suspension engine
* a restoration engine
* a reconciliation engine
* an audit authority
* a monitoring system
* a notification system
* a message queue implementation
* a source of truth

The Event Bus must never mutate authoritative domain state.

---

## 6. Event Authority Model

Only authoritative systems may produce authoritative events.

Event production authority belongs to the domain owner that owns the truth represented by the event.

Event Bus may accept, validate for distribution, route, deliver, reject, or fail events.

Event Bus may not alter event meaning.

Event Bus may not infer missing events.

Event Bus may not synthesize authority.

Event Bus may not convert delivery success into business success.

Event Bus may not convert consumer acknowledgment into domain truth.

---

## 7. Event Entity Model

### Event

An event is an immutable declaration emitted by an authoritative system stating that a domain-owned fact or state transition occurred.

### Event Producer

An event producer is the authoritative system that owns the truth represented by the event.

### Event Consumer

An event consumer is a system authorized to receive the event for visibility, action, audit, reconciliation, notification, or coordination.

### Event Stream

An event stream is an ordered distribution lane for events of a defined authority domain.

### Event Distribution

Event distribution is the act of propagating an accepted event to authorized consumers.

### Event Delivery

Event delivery is a recorded attempt to make an event visible to a consumer.

### Event Subscription

A subscription defines which consumers may receive which event classes.

### Event Acknowledgment

An acknowledgment confirms consumer receipt only.

Acknowledgment does not prove consumer-side correctness.

### Event Replay

Replay is deterministic redistribution of historical events using preserved event and delivery evidence.

### Event Failure

Event failure is any invalid, incomplete, rejected, undelivered, duplicated, orphaned, stale, or untraceable event condition.

---

## 8. Event Ownership Model

### Billing Core

Owns billing events.

Examples:

* invoice created
* invoice finalized
* invoice overdue
* debt opened
* debt resolved
* service eligibility changed

### Payment Lifecycle

Owns payment and settlement events.

Examples:

* payment requested
* payment received
* payment validated
* settlement confirmed
* payment rejected
* payment expired

### Provisioning Lifecycle

Owns provisioning events.

Examples:

* subscriber provisioning requested
* provisioning validated
* provisioning completed
* provisioning failed
* provisioning rollback required

### Radius Policy Engine

Owns policy translation events only.

It does not own billing state.

It does not own service truth.

### Accounting Engine

Owns accounting evidence events.

Examples:

* session started
* interim update received
* session stopped
* stale session detected
* accounting packet rejected

### Suspension Orchestrator

Owns suspension lifecycle events.

Examples:

* suspension required
* suspension command issued
* suspension verified
* suspension failed

### Restoration Orchestrator

Owns restoration lifecycle events.

Examples:

* restoration required
* restoration command issued
* restoration verified
* restoration failed

### Reconciliation Engine

Owns consistency validation events.

Examples:

* drift detected
* orphan state detected
* correction required
* reconciliation completed

### Operational Audit

Owns forensic visibility events.

Operational Audit may consume all event evidence.

Operational Audit does not own domain truth.

### Event Bus

Owns distribution evidence only.

Event Bus owns:

* accepted publication
* rejected publication
* distribution attempt
* delivery status
* delivery failure
* replay distribution record

---

## 9. Event Model

Every authoritative event must contain, conceptually:

* event identity
* event type
* producing authority
* producing system
* domain owner
* subject identity
* causal reference
* occurred-at authority timestamp
* publication timestamp
* immutable payload
* event version
* correlation identity
* trace identity
* replay eligibility
* audit visibility class

Events must be immutable after publication.

Corrections require new events.

Mutation is forbidden.

---

## 10. Event Categories

The system recognizes these event categories:

* business events
* billing events
* payment events
* settlement events
* provisioning events
* authentication events
* accounting events
* suspension events
* restoration events
* reconciliation events
* audit events
* operational events

Each category must have a declared authoritative producer.

No anonymous event category is valid.

---

## 11. Event Propagation Model

Event propagation follows this deterministic sequence:

1. Authoritative producer creates event.
2. Event Bus validates publication authority.
3. Event Bus records accepted publication.
4. Event Bus determines authorized consumers.
5. Event Bus distributes event.
6. Event Bus records delivery attempt.
7. Consumer acknowledges receipt or failure.
8. Event Bus records delivery outcome.
9. Failed deliveries remain reconstructable.
10. Replay uses preserved event evidence.

Propagation must be traceable from producer to consumer.

---

## 12. Event Delivery Model

Delivery ownership belongs to Event Bus.

Consumer-side processing ownership belongs to the consumer.

Delivery success means the event was delivered.

Delivery success does not mean the consumer completed domain processing.

Acknowledgment means receipt confirmation only.

Consumer failure must not rewrite original event state.

Delivery records must preserve:

* event identity
* consumer identity
* delivery attempt identity
* delivery status
* delivery time
* failure reason if failed
* replay reference if replayed

---

## 13. Event Failure Model

The Event Bus must classify failures deterministically.

Failure classes include:

* producer authority failure
* invalid event structure
* unauthorized event publication
* unauthorized consumer
* delivery failure
* consumer failure
* duplicate event
* duplicate delivery
* lost delivery
* late delivery
* out-of-order delivery
* orphan event
* stale event
* conflicting event
* untraceable event

Failure classification must never mutate domain truth.

---

## 14. Event State Machine

Allowed event states:

* `CREATED`
* `VALIDATED`
* `PUBLISHED`
* `DISTRIBUTING`
* `DELIVERED`
* `ACKNOWLEDGED`
* `FAILED`
* `REJECTED`

### Legal Transitions

* `CREATED -> VALIDATED`
* `CREATED -> REJECTED`
* `VALIDATED -> PUBLISHED`
* `VALIDATED -> REJECTED`
* `PUBLISHED -> DISTRIBUTING`
* `DISTRIBUTING -> DELIVERED`
* `DISTRIBUTING -> FAILED`
* `DELIVERED -> ACKNOWLEDGED`
* `DELIVERED -> FAILED`
* `FAILED -> DISTRIBUTING` only during controlled replay
* `REJECTED` is terminal
* `ACKNOWLEDGED` is terminal for that delivery attempt

### Forbidden Transitions

* `REJECTED -> PUBLISHED`
* `ACKNOWLEDGED -> CREATED`
* `FAILED -> ACKNOWLEDGED` without redelivery
* `DELIVERED -> PUBLISHED`
* `PUBLISHED -> CREATED`
* any transition that changes domain meaning
* any transition that rewrites original event evidence

---

## 15. Event Chain Model

Every event must preserve lineage.

Event chains must support:

* causal tracing
* cross-system tracing
* state transition tracing
* delivery tracing
* replay tracing
* audit reconstruction

Event chains must be immutable.

Derived events must reference causal source events.

Circular event dependency is forbidden.

---

## 16. Event Visibility Model

The Event Bus must provide visibility into:

* publication status
* publication authority
* event producer
* event consumer
* delivery attempt
* delivery result
* failure reason
* replay history
* event lineage
* event chain continuity

Visibility does not mean monitoring implementation.

Visibility means forensic and operational reconstructability.

---

## 17. Failed Event Model

### Missing Events

Must be detected through reconciliation or audit comparison.

Event Bus must not invent missing events.

### Duplicate Events

Must be classified using deterministic identity and causality rules.

Duplicate events must not create duplicate domain actions.

### Orphan Events

Events without valid authority lineage must be rejected or quarantined.

### Invalid Events

Invalid events must be rejected before distribution.

### Stale Events

Stale events must be marked as stale and handled according to replay rules.

### Untraceable Events

Untraceable events are invalid for authoritative processing.

### Conflicting Events

Conflicting events must be escalated to the owning authority or reconciliation process.

Event Bus must not resolve business conflicts.

---

## 18. Multi-System Event Model

The Event Bus coordinates visibility between:

* Billing Core
* Payment Lifecycle
* Provisioning Lifecycle
* Radius Policy Engine
* Accounting Engine
* Suspension Orchestrator
* Restoration Orchestrator
* Reconciliation Engine
* Operational Audit

Cross-system event distribution must never create authority overlap.

Each consumer receives events according to declared operational need.

---

## 19. Replay-Safe Event Model

Replay must preserve:

* original event identity
* original producer authority
* original occurred-at time
* original payload
* original causal chain
* replay attempt identity
* replay reason
* replay consumer
* replay result

Replay must not create new business truth.

Replay must not duplicate domain transitions.

Replay is redistribution, not regeneration.

---

## 20. Reconciliation Integration

Reconciliation may inspect event history to validate consistency.

Reconciliation may detect:

* missing event evidence
* delivery drift
* stale propagation
* orphan delivery
* domain-state mismatch
* consumer-side processing drift

Reconciliation owns consistency truth.

Event Bus owns distribution evidence only.

Event Bus must expose enough event evidence for reconciliation to prove or disprove propagation integrity.

---

## 21. Audit Integration

Operational Audit may inspect all Event Bus evidence.

Audit visibility includes:

* producer identity
* authority identity
* event identity
* event payload evidence
* publication record
* distribution record
* delivery record
* acknowledgment record
* failure record
* replay record

Operational Audit owns forensic visibility.

Event Bus owns distribution records.

Audit may reconstruct events.

Audit may not rewrite events.

---

## 22. Operational Invariants

The following laws are immutable:

1. Event Bus never creates truth.
2. Event Bus never owns domain state.
3. Event Bus never mutates authoritative payloads.
4. Event Bus never decides business outcomes.
5. Event Bus never decides payment outcomes.
6. Event Bus never decides suspension outcomes.
7. Event Bus never decides restoration outcomes.
8. Event Bus never decides reconciliation outcomes.
9. Event Bus distributes only authoritative events.
10. Every event must have an authoritative producer.
11. Every event must have traceable ownership.
12. Every delivery must be reconstructable.
13. Every replay must be traceable.
14. Every failure must be classified.
15. Every consumer must be authorized.
16. Every publication must be authority-validated.
17. Duplicate delivery must not duplicate domain state.
18. Lost delivery must be visible.
19. Out-of-order delivery must be detectable.
20. Manual event manipulation is forbidden.
21. Hidden event streams are forbidden.
22. Circular event dependencies are forbidden.
23. Event conclusions without evidence are forbidden.
24. Event mutation is forbidden.
25. Correction requires new authoritative event.

---

## 23. Forbidden Event Patterns

The following patterns are forbidden:

* Event Bus creating truth
* Event Bus creating authority
* Event Bus modifying billing state
* Event Bus modifying payment state
* Event Bus modifying suspension state
* Event Bus modifying restoration state
* Event Bus modifying reconciliation state
* Event Bus becoming source of truth
* Event Bus becoming source of authority
* hidden event streams
* orphan events
* untraceable deliveries
* event delivery without ownership
* event publication without authority
* event replay without traceability
* manual event manipulation
* circular event dependencies
* event conclusions without evidence
* consumer acknowledgment treated as domain truth
* notification success treated as business success
* delivery success treated as infrastructure success
* replay treated as new event creation
* failed event silently ignored
* duplicate event silently accepted
* stale event treated as current truth
* invalid event distributed to consumers

---

## 24. Final Authority Statement

`EVENT_BUS_CONTRACT.md` defines the deterministic event distribution authority for the ISP Billing & AAA Infrastructure.

The Event Bus owns propagation.

The Event Bus owns delivery evidence.

The Event Bus owns distribution traceability.

The Event Bus does not own business truth.

The Event Bus does not own infrastructure truth.

The Event Bus does not own payment truth.

The Event Bus does not own billing truth.

The Event Bus does not own suspension truth.

The Event Bus does not own restoration truth.

The Event Bus does not own reconciliation truth.

The Event Bus does not own audit truth.

All authoritative truth remains owned by the originating domain authority.

This contract is final unless superseded by a higher authority contract.

