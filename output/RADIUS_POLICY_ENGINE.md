# RADIUS_POLICY_ENGINE.md

## 1. Document Purpose

This document defines the institutional architecture contract for the Radius Policy Engine in the ISP Billing & AAA Infrastructure.

The Radius Policy Engine is responsible for translating authoritative business state into deterministic Radius policy state.

It does not own subscriber business state.

It does not execute network enforcement directly.

It does not replace Billing Core, FreeRADIUS, MikroTik, or Accounting.

Its sole responsibility is deterministic policy translation.

---

## 2. Core Doctrine

Radius must never become a hidden source of truth.

The system authority model is:

```text
Billing Core
→ Radius Policy Engine
→ FreeRADIUS
→ MikroTik NAS
```

### Mandatory Doctrine

* Billing Core owns business state.
* Radius Policy Engine owns policy translation.
* FreeRADIUS executes authentication and authorization decisions.
* MikroTik executes network enforcement.
* Accounting observes runtime evidence.
* No billing logic may exist inside FreeRADIUS.
* No billing logic may exist inside MikroTik.
* No manual synchronization is allowed as a normal operating model.
* Policy generation must be deterministic.
* Policy decisions must be replay-safe.
* Radius policy records must be derived state, not primary state.

---

## 3. Policy Authority Matrix

| Domain                   | Authority Owner      | Allowed Responsibility                                     | Forbidden Responsibility                                   |
| ------------------------ | -------------------- | ---------------------------------------------------------- | ---------------------------------------------------------- |
| Subscriber lifecycle     | Billing Core         | Own active, suspended, terminated, pending, restored state | FreeRADIUS or MikroTik deciding subscriber business status |
| Invoice status           | Billing Core         | Determine paid, unpaid, overdue, settled                   | Radius interpreting invoice meaning                        |
| Policy translation       | Radius Policy Engine | Convert business state into Radius policy state            | Owning business truth                                      |
| Authentication execution | FreeRADIUS           | Validate credentials using generated policy                | Creating business policy                                   |
| Network enforcement      | MikroTik NAS         | Apply accepted Radius enforcement attributes               | Deciding billing status                                    |
| Runtime session evidence | Accounting Engine    | Observe sessions, stale state, disconnect evidence         | Owning subscriber lifecycle                                |
| Manual operator action   | Admin Orchestrator   | Request lifecycle change through controlled workflow       | Direct profile modification in Radius or MikroTik          |

---

## 4. Policy Ownership Model

### 4.1 Business State Ownership

Business state belongs only to Billing Core.

Examples:

* subscriber is active
* subscriber is overdue
* subscriber is suspended
* subscriber is restored
* subscriber is terminated
* subscriber is pending activation

Radius Policy Engine must consume this state but must not mutate its meaning.

### 4.2 Policy State Ownership

Radius Policy Engine owns derived policy state.

Examples:

* authentication allowed
* authentication denied
* bandwidth profile assigned
* isolation profile assigned
* service disabled
* session termination required
* restoration eligible

Policy state is always derived from business state.

### 4.3 Enforcement State Ownership

MikroTik owns runtime enforcement execution.

Examples:

* PPPoE session allowed
* PPPoE session rejected
* bandwidth profile applied
* isolation profile applied
* active session disconnected

MikroTik does not own the reason for enforcement.

---

## 5. State Translation Doctrine

Policy translation must follow a deterministic one-way model:

```text
Business State
→ Radius Policy State
→ Enforcement State
```

### 5.1 Canonical Business States

The Radius Policy Engine must recognize only explicit lifecycle states from Billing Core:

* `PENDING_ACTIVATION`
* `ACTIVE`
* `OVERDUE`
* `SOFT_SUSPENDED`
* `HARD_SUSPENDED`
* `RESTORE_PENDING`
* `TERMINATED`

No implicit state may be inferred from Radius records alone.

---

## 6. Deterministic State Mapping

| Business State     | Authentication Policy                     | Authorization Policy                         | Enforcement Intent            |
| ------------------ | ----------------------------------------- | -------------------------------------------- | ----------------------------- |
| Pending activation | Deny or limited activation policy         | No production service profile                | Prevent premature access      |
| Active             | Allow                                     | Assigned service profile                     | Normal internet access        |
| Overdue            | Allow unless suspension threshold reached | Existing profile or warning profile          | Grace-period handling         |
| Soft suspended     | Allow                                     | Isolation profile                            | Captive / limited access      |
| Hard suspended     | Deny                                      | No service profile                           | No access                     |
| Restore pending    | Controlled restore policy                 | Target active profile pending reconciliation | Prevent unsafe double restore |
| Terminated         | Deny                                      | No profile                                   | Permanent service removal     |

### 6.1 Invalid State Handling

Invalid state must never silently produce an active policy.

Invalid states include:

* subscriber has multiple lifecycle states
* missing subscriber identity
* missing service profile
* active state without valid credentials
* suspended state with active production profile
* terminated state with active credentials
* restore pending without prior suspension evidence

Invalid state must resolve to fail-safe policy.

---

## 7. State Conflict Resolution

Conflict resolution must be deterministic and conservative.

Priority order:

1. `TERMINATED`
2. `HARD_SUSPENDED`
3. `SOFT_SUSPENDED`
4. `RESTORE_PENDING`
5. `OVERDUE`
6. `PENDING_ACTIVATION`
7. `ACTIVE`

The most restrictive valid state wins.

No state conflict may be resolved manually inside FreeRADIUS or MikroTik.

---

## 8. Authentication Doctrine

### 8.1 Credential Ownership

Credentials are owned by Billing Core or the Identity component assigned by Billing Core.

Radius Policy Engine may generate authentication policy from credential state, but it must not become the credential authority.

### 8.2 Authentication Responsibility

FreeRADIUS executes authentication.

Radius Policy Engine determines whether credentials are currently eligible for authentication.

### 8.3 Credential Lifecycle

Credential lifecycle must follow subscriber lifecycle.

| Subscriber State   | Credential Behavior                   |
| ------------------ | ------------------------------------- |
| Pending activation | Not production-valid                  |
| Active             | Valid if not revoked                  |
| Overdue            | Valid unless suspension policy denies |
| Soft suspended     | Valid only for isolation policy       |
| Hard suspended     | Disabled                              |
| Restore pending    | Controlled reactivation               |
| Terminated         | Revoked                               |

### 8.4 Disabled Credential Handling

Disabled credentials must not authenticate into production service.

Any disabled credential that still produces access is a critical architecture violation.

---

## 9. Authorization Doctrine

Authorization is the process of assigning subscriber access characteristics after authentication eligibility is resolved.

Authorization must include:

* service profile
* bandwidth profile
* isolation profile when applicable
* access permission
* session behavior intent
* policy version

### 9.1 Bandwidth Profile Policy

Bandwidth profile must be derived from the subscriber’s active service package.

FreeRADIUS must not independently decide package speed.

MikroTik must not manually override package speed as normal operation.

### 9.2 Service Profile Policy

Service profile must be deterministic and traceable to Billing Core service package state.

Any profile assignment must be auditable.

### 9.3 Isolation Profile Policy

Isolation profile is used only for controlled limited access.

It must not be treated as active service.

Soft-suspended users must not retain production bandwidth profiles.

### 9.4 Policy Override Doctrine

Policy override is dangerous and must be exceptional.

Allowed only when:

* explicitly requested through authorized operations workflow
* recorded with operator identity
* bounded by reason
* bounded by expiry or rollback condition
* visible in audit trail

Forbidden:

* direct Radius table edits
* direct MikroTik profile edits
* silent package override
* undocumented whitelist
* hidden emergency bypass

---

## 10. Suspension Policy Doctrine

Suspension must be deterministic, reversible, and replay-safe.

### 10.1 Allowed Suspension States

* `OVERDUE`
* `SOFT_SUSPENDED`
* `HARD_SUSPENDED`
* `RESTORE_PENDING`

### 10.2 Soft Suspension

Soft suspension means:

* authentication may still succeed
* production service is removed
* isolation profile is assigned
* user may be redirected or limited
* accounting must still observe session evidence

### 10.3 Hard Suspension

Hard suspension means:

* authentication denied
* no production profile
* no isolation profile unless explicitly required
* existing sessions must be reconciled for termination

### 10.4 Restoration

Restoration must not blindly re-enable access.

Restoration requires:

* authoritative paid or cleared business state
* previous suspension state known
* target service profile known
* stale session check
* policy version update
* replay-safe transition record

### 10.5 Forbidden Transitions

Forbidden transitions include:

* `TERMINATED → ACTIVE` without reactivation workflow
* `HARD_SUSPENDED → ACTIVE` without restore reconciliation
* `SOFT_SUSPENDED → ACTIVE` without policy regeneration
* `PENDING_ACTIVATION → SOFT_SUSPENDED`
* `ACTIVE → RESTORE_PENDING`
* any state → production profile by manual Radius edit

---

## 11. Policy Synchronization Doctrine

Synchronization must be controlled by authoritative systems.

### 11.1 Billing Core to Radius Policy Engine

Billing Core emits or exposes authoritative state.

Radius Policy Engine consumes this state and generates derived policy.

Billing Core does not directly write enforcement behavior.

### 11.2 Radius Policy Engine to FreeRADIUS

FreeRADIUS consumes generated policy state.

FreeRADIUS must not generate business rules.

### 11.3 FreeRADIUS to MikroTik

MikroTik consumes Radius responses.

MikroTik must not invent billing meaning.

### 11.4 Stale Policy Detection

A policy is stale when:

* business state changed but policy version did not change
* policy version changed but FreeRADIUS serves old policy
* MikroTik active session no longer matches current policy
* suspended subscriber still has production session
* restored subscriber remains isolated after successful restore

Stale policy must be treated as operational risk.

---

## 12. Operational Invariants

The following invariants must always hold:

1. One subscriber must have one authoritative lifecycle state.
2. Radius policy must be derived from Billing Core state.
3. Radius must not own billing truth.
4. MikroTik must not own billing truth.
5. Active subscriber must map to one valid production service profile.
6. Soft-suspended subscriber must not receive production service profile.
7. Hard-suspended subscriber must not authenticate into service.
8. Terminated subscriber must not retain valid credentials.
9. Policy version must change when policy meaning changes.
10. Manual policy mutation must be detectable.
11. Replay of the same business state must produce the same policy state.
12. Invalid or conflicting state must fail safe.

---

## 13. Multi-NAS Future Readiness

The policy engine must be NAS-aware but not NAS-dependent.

### 13.1 Multi-NAS Doctrine

* Policy must remain consistent across multiple MikroTik NAS devices.
* Subscriber access must not depend on which NAS receives authentication.
* NAS-specific behavior must be explicit.
* NAS must not contain hidden subscriber exceptions.

### 13.2 Subscriber Roaming Assumption

Default assumption:

* subscriber belongs to a defined service area or NAS group
* roaming is forbidden unless explicitly modeled
* authentication from unexpected NAS must be treated as policy-relevant

### 13.3 Policy Distribution Doctrine

For future multi-NAS scale:

* one subscriber policy
* multiple NAS consumers
* deterministic NAS eligibility
* explicit NAS group mapping
* no per-NAS manual shadow state

---

## 14. Policy Failure Doctrine

### 14.1 Radius Unavailable

Fail behavior must be conservative.

No new unauthorized access should be granted when Radius is unavailable.

Existing sessions require separate accounting and NAS runtime doctrine.

### 14.2 Billing Core Unavailable

If Billing Core is unavailable, Radius Policy Engine must not invent new business state.

Allowed:

* continue serving last verified policy only if explicitly marked valid
* block policy mutation
* raise operational alert

Forbidden:

* auto-restore
* auto-activate
* manual active assumption

### 14.3 PostgreSQL Unavailable

If policy storage is unavailable:

* policy mutation must stop
* new policy generation must stop
* existing policy serving depends on verified cache doctrine
* no silent fallback to permissive access

### 14.4 Stale Policy Records

Stale policy records must be detected and reconciled.

Stale active policy for suspended or terminated subscriber is critical.

### 14.5 Duplicated Policy Records

Duplicated policy records are invalid.

The system must not randomly choose one.

Conflict must fail safe.

### 14.6 Profile Mismatch

Profile mismatch occurs when expected policy profile differs from runtime profile.

Examples:

* active user receives isolation profile
* suspended user receives production profile
* terminated user still has active session
* restored user remains denied

Profile mismatch must be auditable and reconciled.

### 14.7 Rollback Scenarios

Rollback must restore a previous known-valid policy state.

Rollback must not bypass business authority.

Rollback must be traceable by policy version.

---

## 15. Security Doctrine

### 15.1 Policy Tampering Risk

Policy tampering is a critical security risk.

Examples:

* unauthorized bandwidth upgrade
* suspended user manually reactivated
* terminated user credential restored
* isolation profile bypassed
* direct Radius mutation

### 15.2 Integrity Requirements

Every generated policy must be traceable to:

* subscriber identity
* authoritative business state
* source event or state version
* generated policy version
* generation timestamp
* actor or system source
* previous policy version
* transition reason

### 15.3 Manual Modification Risk

Manual modification must be treated as break-glass only.

Normal operations must not require direct modification of:

* Radius policy records
* MikroTik user profile
* NAS local secrets
* bandwidth enforcement profile

### 15.4 Privilege Escalation Risk

Privilege escalation may happen through:

* unauthorized service profile assignment
* direct NAS local user creation
* manual Radius accept rule
* stale credential reuse
* hidden fallback policy

All such paths must be blocked or detectable.

---

## 16. Audit Trail Requirements

The Radius Policy Engine must provide auditability for:

* policy generation
* policy update
* policy suspension
* policy restoration
* credential disablement
* credential restoration
* profile assignment
* policy override
* policy rollback
* policy conflict
* stale policy detection
* desynchronization event

Audit records must be immutable in meaning.

---

## 17. Ambiguity Findings

The following ambiguities must be resolved before implementation:

1. Whether overdue state immediately suspends or enters grace period.
2. Whether soft suspension uses authentication allow with isolation or authentication deny.
3. Whether hard suspension disconnects existing active sessions immediately.
4. Whether restore requires explicit accounting reconciliation before service returns.
5. Whether pending activation allows test login.
6. Whether terminated subscriber can be reactivated or must be recreated.
7. Whether multi-NAS roaming is allowed.
8. Whether Radius may serve last-known policy during Billing Core outage.
9. Whether emergency override is allowed and who can authorize it.
10. Whether subscriber package change requires forced reconnect.

---

## 18. Architecture Violations

The following are forbidden architecture violations:

1. Billing state stored only in Radius.
2. MikroTik local user used as hidden subscriber authority.
3. Manual profile changes outside policy engine.
4. FreeRADIUS rules containing billing logic.
5. MikroTik scripts containing billing logic.
6. Suspended user retaining production profile.
7. Terminated user retaining valid credentials.
8. Multiple active policies for one subscriber.
9. Radius fallback accepting unknown users.
10. Restore performed without policy regeneration.
11. Accounting used as primary business state.
12. Operator directly modifying Radius to solve billing issue.
13. NAS-specific hidden whitelist.
14. Policy conflict resolved by last-write-wins without authority check.

---

## 19. Rebuild Recommendations

### Recommendation 1: Separate Business State From Policy State

Billing Core must remain the sole business authority.

Radius Policy Engine must only translate.

### Recommendation 2: Introduce Explicit Policy Versioning

Every policy mutation must produce a new policy version.

Replay must produce the same versioned decision from the same input.

### Recommendation 3: Define Canonical Subscriber Lifecycle

Subscriber lifecycle must be finalized before implementation.

No implicit states.

### Recommendation 4: Define Suspension Semantics Before Coding

Soft suspension, hard suspension, overdue, and restore must be contractually defined.

### Recommendation 5: Treat Restore as Reconciliation, Not Simple Enable

Restore must check policy, credential, session, and accounting consistency.

### Recommendation 6: Make Manual Override Auditable and Exceptional

Manual override must never become daily operations.

### Recommendation 7: Prepare for Multi-NAS Early

Even if phase one has one MikroTik, the policy model must not assume one NAS forever.

### Recommendation 8: Define Fail-Safe Behavior

Failure conditions must not default to permissive access.

### Recommendation 9: Keep FreeRADIUS Dumb

FreeRADIUS should execute policy, not own business interpretation.

### Recommendation 10: Keep MikroTik Dumb

MikroTik should enforce Radius decisions, not contain billing logic.

---

## 20. Final Contract Statement

The Radius Policy Engine is the deterministic translation boundary between business authority and network enforcement.

Its purpose is not to authenticate users directly.

Its purpose is not to store billing truth.

Its purpose is not to replace FreeRADIUS.

Its purpose is to ensure that every subscriber business state becomes one explicit, auditable, replay-safe Radius policy decision.

Radius must remain a policy execution layer.

It must never become a hidden source of truth.

