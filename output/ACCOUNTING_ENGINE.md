# ACCOUNTING_ENGINE.md

## 1. PURPOSE

This document defines the deterministic accounting architecture contract for the ISP AAA Infrastructure.

The accounting subsystem is NOT treated as:

* simple logging
* bandwidth statistics
* auxiliary observability
* billing metadata

The accounting subsystem IS treated as:

* operational evidence infrastructure
* session authority evidence
* replay-safe lifecycle recorder
* reconciliation source of truth
* suspend enforcement validator
* infrastructure integrity mechanism

This document establishes:

* accounting authority
* accounting lifecycle contracts
* reconciliation doctrine
* stale session doctrine
* replay-safe accounting behavior
* operational invariants
* failure containment rules

This document intentionally excludes:

* implementation
* SQL schema
* FreeRADIUS configuration
* MikroTik commands
* Docker deployment
* API design

---

# 2. ACCOUNTING DOCTRINE

## 2.1 Core Principle

Accounting packets are operational evidence.

Accounting data must never be interpreted as:

* approximate telemetry
* best-effort metrics
* advisory information

Accounting evidence is authoritative infrastructure state evidence.

---

## 2.2 Accounting Authority Hierarchy

Accounting authority must follow deterministic hierarchy:

1. NAS Runtime Reality
2. Accounting Evidence Stream
3. Reconciliation Engine
4. Session Authority Layer
5. Billing Layer

Billing is NEVER the authority for session existence.

Billing consumes validated accounting evidence.

Billing must never mutate accounting evidence.

---

## 2.3 Accounting Evidence Doctrine

Accounting evidence must be:

* append-oriented
* immutable after validation
* replay-safe
* timestamp-preserving
* duplicate-detectable
* reconciliation-compatible

Accounting evidence represents:

* session creation
* session continuity
* session termination
* runtime drift
* reconnect behavior
* suspend inconsistencies
* operational anomalies

Accounting evidence must survive:

* NAS reboot
* Radius restart
* orchestrator restart
* reconciliation replay
* delayed packets
* duplicate packets

---

# 3. ACCOUNTING RUNTIME MODEL

## 3.1 Runtime Components

Accounting runtime consists of:

### A. NAS Runtime

Produces accounting evidence.

### B. Radius Accounting Ingestion

Receives accounting packets.

### C. Accounting Evidence Store

Stores immutable accounting events.

### D. Session Authority Engine

Builds deterministic session state.

### E. Reconciliation Engine

Continuously validates operational integrity.

### F. Billing Layer

Consumes validated accounting outcomes only.

---

## 3.2 Accounting Ownership

Radius accounting ingestion owns:

* accounting packet acceptance
* evidence preservation

Session authority engine owns:

* session state interpretation
* lifecycle continuity
* duplicate suppression logic
* stale classification

Reconciliation engine owns:

* operational correction
* drift validation
* stale cleanup authority
* replay integrity validation

Billing owns:

* financial interpretation only

---

# 4. ACCOUNTING LIFECYCLE CONTRACT

## 4.1 Deterministic Session Lifecycle

Canonical lifecycle:

1. AUTH_ACCEPT
2. SESSION_CREATED
3. ACCOUNTING_START
4. INTERIM_UPDATES
5. SESSION_ACTIVE
6. ACCOUNTING_STOP
7. SESSION_TERMINATED
8. RECONCILIATION_FINALIZED

Accounting lifecycle must remain replay-reconstructable.

---

# 5. ACCOUNTING PACKET CONTRACTS

## 5.1 Accounting Start Doctrine

Accounting Start establishes:

* operational session existence
* authoritative runtime activation

Accounting Start must NOT:

* immediately imply billing validity
* imply infrastructure stability
* imply reconciliation success

Start packet only establishes:
"session activation evidence received."

---

## 5.2 Interim Update Doctrine

Interim updates establish:

* session continuity
* bandwidth accumulation evidence
* runtime heartbeat
* disconnect absence evidence

Interim updates are continuity evidence only.

Interim updates must never:

* independently terminate sessions
* overwrite lifecycle history
* erase previous evidence

---

## 5.3 Stop Packet Doctrine

Stop packets establish:

* session termination evidence
* final usage accumulation
* disconnect completion

Stop packets finalize lifecycle evidence.

Stop packets do NOT erase:

* stale history
* reconnect anomalies
* duplicate evidence
* reconciliation flags

---

# 6. DUPLICATE ACCOUNTING DOCTRINE

## 6.1 Duplicate Packet Reality

Duplicate accounting packets are expected infrastructure behavior.

The system must assume duplicates WILL occur due to:

* NAS retries
* packet retransmission
* restart storms
* timeout recovery
* Radius restart recovery
* network instability

Duplicates are not exceptional behavior.

---

## 6.2 Duplicate Handling Contract

Duplicate packets must:

* preserve evidence
* remain detectable
* never corrupt lifecycle ordering
* never create duplicate active sessions

Duplicate suppression must occur at:

* session authority interpretation layer
  NOT at raw evidence layer.

Raw evidence must remain preserved.

---

# 7. DELAYED PACKET DOCTRINE

## 7.1 Delayed Accounting Reality

Accounting packets may arrive:

* out-of-order
* late
* after reconnect
* after Radius restart
* after reconciliation execution

The system must never assume realtime ordering.

---

## 7.2 Ordering Authority

Canonical ordering authority:

1. session lifecycle ordering
2. accounting event sequence
3. reconciliation validation
4. packet arrival time

Arrival time is NOT authoritative lifecycle ordering.

---

# 8. SESSION INTEGRITY MODEL

## 8.1 Session Authority

Active session state is derived from:

* accounting evidence
* reconciliation validation
* stale detection rules

NOT from:

* single packets
* NAS assumptions alone
* billing state
* cache state

---

## 8.2 Session Integrity States

Canonical states:

* ACTIVE
* TERMINATING
* TERMINATED
* STALE
* ORPHANED
* RECONCILING
* INVALID
* DUPLICATE_CONFLICT

These states are operational states only.

Billing must never directly control session state.

---

# 9. STALE SESSION DOCTRINE

## 9.1 Stale Session Definition

A stale session is:
a session believed active by infrastructure state,
but lacking operational continuity evidence.

Examples:

* missing stop packet
* NAS reboot
* Radius restart
* reconnect without closure
* timeout collapse

---

## 9.2 Stale Detection Authority

Stale classification authority belongs exclusively to:
Reconciliation Engine.

Neither:

* NAS
* Radius
* Billing
  may independently finalize stale cleanup.

---

## 9.3 Stale Cleanup Doctrine

Stale cleanup must:

* preserve historical evidence
* preserve anomaly traceability
* avoid destructive deletion
* remain replay-safe

Cleanup is state correction,
NOT evidence destruction.

---

# 10. RECONNECT LIFECYCLE CONTRACT

## 10.1 Reconnect Reality

Reconnects are expected operational behavior.

Reconnects may occur due to:

* PPPoE instability
* router reboot
* power failure
* Radius timeout
* user reconnect
* network flapping

Reconnect handling must be deterministic.

---

## 10.2 Reconnect Integrity

Reconnects must:

* preserve prior evidence
* prevent duplicate active authority
* preserve continuity lineage
* remain reconciliation-compatible

Reconnects must never:

* silently overwrite active sessions
* erase stale evidence
* collapse lifecycle traceability

---

# 11. ORPHAN SESSION DOCTRINE

## 11.1 Orphan Definition

An orphan session exists when:

* accounting evidence exists
  BUT
* authoritative lifecycle completion cannot be proven

Examples:

* start without stop
* interim without authoritative start
* reconnect collision
* replay inconsistency

---

## 11.2 Orphan Handling

Orphan sessions must:

* remain visible
* remain auditable
* enter reconciliation lifecycle
* never silently disappear

Operational ambiguity must remain observable.

---

# 12. RECONCILIATION ENGINE

## 12.1 Reconciliation Purpose

Reconciliation exists to:

* stabilize operational truth
* repair drift
* validate continuity
* classify stale sessions
* detect accounting anomalies
* restore deterministic state

Reconciliation is infrastructure stabilization,
NOT financial processing.

---

## 12.2 Reconciliation Authority

Reconciliation engine owns:

* stale classification
* duplicate conflict resolution
* orphan handling
* session closure validation
* replay reconstruction
* drift detection

---

## 12.3 Reconciliation Replay Safety

Reconciliation execution must be:

* deterministic
* idempotent
* replay-safe
* restart-safe

Repeated reconciliation execution must produce:
identical operational outcomes.

---

# 13. DRIFT DETECTION DOCTRINE

## 13.1 Drift Definition

Drift occurs when:
infrastructure runtime state diverges from accounting authority state.

Examples:

* active session without interim updates
* NAS session exists but accounting absent
* accounting active but NAS disconnected
* duplicate concurrent authority
* suspend mismatch

---

## 13.2 Drift Classification

Drift severity classes:

### LOW

Minor timing inconsistency.

### MEDIUM

Potential lifecycle ambiguity.

### HIGH

Operational authority conflict.

### CRITICAL

Billing or suspend integrity risk.

---

# 14. BILLING SAFETY DOCTRINE

## 14.1 Billing Separation Principle

Billing must NEVER directly consume:

* raw accounting packets
* unstable session state
* unreconciled evidence

Billing consumes:
validated accounting outcomes only.

---

## 14.2 Financial Safety Principle

Accounting integrity is higher priority than:

* invoice speed
* realtime usage display
* customer dashboard freshness

Financial systems must tolerate delayed accounting finalization.

Operational integrity takes precedence.

---

# 15. FAILURE MODEL

## 15.1 NAS Reboot

Expected consequences:

* missing stop packets
* reconnect storms
* stale sessions
* duplicate starts

System must:

* preserve evidence
* enter reconciliation mode
* prevent destructive cleanup

---

## 15.2 Radius Restart

Expected consequences:

* packet replay
* delayed processing
* duplicate ingestion
* interim timing collapse

System must remain:

* replay-safe
* idempotent
* duplicate-tolerant

---

## 15.3 PostgreSQL Downtime

Expected consequences:

* ingestion interruption
* delayed persistence
* reconciliation backlog

System doctrine:
evidence preservation has higher priority than realtime visibility.

---

## 15.4 Reconnect Storm

Expected consequences:

* duplicate active claims
* lifecycle overlap
* stale collisions
* reconciliation pressure

System must:

* prioritize authority stabilization
* avoid destructive correction
* maintain replay-safe evidence

---

# 16. OPERATIONAL INVARIANTS

## 16.1 Immutable Evidence Invariant

Raw accounting evidence must never be destroyed.

---

## 16.2 Replay Safety Invariant

Replay execution must reproduce identical session interpretation.

---

## 16.3 Session Authority Invariant

Only reconciliation-validated state may become authoritative.

---

## 16.4 Billing Isolation Invariant

Billing must never mutate accounting evidence.

---

## 16.5 Duplicate Tolerance Invariant

Duplicates must never create:

* multiple authoritative sessions
* irreversible corruption
* lifecycle collapse

---

## 16.6 Stale Preservation Invariant

Stale evidence must remain observable.

---

# 17. DETERMINISTIC ACCOUNTING CONTRACTS

## 17.1 Canonical Infrastructure Contracts

The accounting engine must guarantee:

### A. Replay-Safe Lifecycle Reconstruction

Operational history can be deterministically reconstructed.

### B. Evidence Preservation

Operational evidence survives failures.

### C. Duplicate Safety

Duplicates never corrupt authority state.

### D. Drift Visibility

Infrastructure inconsistencies remain observable.

### E. Reconciliation Authority

Operational truth is reconciliation-derived.

### F. Billing Isolation

Financial systems never become accounting authority.

---

# 18. FINAL DOCTRINE

The accounting engine is not a logging subsystem.

The accounting engine is:

* operational evidence infrastructure
* deterministic session authority layer
* reconciliation foundation
* suspend integrity validator
* billing safety boundary

Implementation must only begin AFTER:

* accounting authority stabilizes
* reconciliation doctrine finalizes
* replay-safe lifecycle guarantees are accepted
* operational invariants are locked

No billing implementation should proceed before accounting determinism is established.

