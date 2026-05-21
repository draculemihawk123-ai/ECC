# Domain Flow — Service Connect App

## Main Lead Assignment Flow

```mermaid
sequenceDiagram
    actor Customer
    participant FE as Next.js App
    participant API as Spring Boot API
    participant Lead as Lead Module
    participant Admin as Admin
    participant Provider as Provider
    participant Credit as Credit Module

    Customer->>FE: Submit service request
    FE->>API: POST /api/customer/service-requests
    API->>Lead: Create request with PENDING_VERIFICATION
    Lead-->>API: Request created
    API-->>FE: Return request id and status

    Admin->>API: Review request
    API->>Lead: Verify lead and set credit cost
    Lead-->>API: Lead status VERIFIED

    Admin->>API: Assign providers
    API->>Lead: Create provider lead assignments
    Lead-->>Provider: Lead visible as assigned summary

    Provider->>API: Unlock assigned lead
    API->>Credit: Deduct provider credit atomically
    Credit->>Lead: Confirm unlock allowed and accepted count < 3
    Lead-->>API: Lead details unlocked
    API-->>Provider: Return customer contact/details
```

## Lead Status Lifecycle

```text
DRAFT / SUBMITTED
  -> PENDING_VERIFICATION
  -> VERIFIED
  -> ASSIGNED
  -> PARTIALLY_ACCEPTED
  -> FULLY_ACCEPTED
  -> COMPLETED
  -> CANCELLED / REJECTED
```

## Assignment Status Lifecycle

```text
ASSIGNED
  -> VIEWED
  -> UNLOCKED
  -> CONTACTED
  -> WON / LOST / EXPIRED
```

## Provider Credit Flow

```text
TRIAL_CREDIT_GRANTED
  -> CREDIT_AVAILABLE
  -> CREDIT_RESERVED_OR_DEDUCTED_ON_UNLOCK
  -> CREDIT_LOW
  -> CONTACT_ADMIN_FOR_TOPUP
```

## Critical Invariants

1. A provider can only unlock assigned leads.
2. A provider can unlock the same lead once only.
3. Credit deduction and lead unlock must be in one transaction.
4. The number of unlocked providers per lead must never exceed 3.
5. Customer contact information must stay hidden until successful unlock.
6. Admin is the only actor allowed to verify leads and assign providers in early versions.

## Concurrency Risk

The highest-risk operation is provider lead unlock.

Required protection:

- Use database transaction.
- Lock lead row or use conditional update.
- Check provider assignment exists.
- Check provider has enough credit.
- Check provider has not unlocked the lead before.
- Check accepted provider count is below 3.
- Deduct credit.
- Create credit transaction ledger.
- Mark assignment as unlocked.

Recommended MySQL strategy for MVP:

- Use `@Transactional` at use-case/service level.
- Use `SELECT ... FOR UPDATE` on lead and provider credit wallet rows.
- Add unique constraint on `(lead_id, provider_id)` for assignment.
- Add unique constraint on credit transaction idempotency key if unlock API can retry.
