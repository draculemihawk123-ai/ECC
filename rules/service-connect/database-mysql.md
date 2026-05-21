# Database Rules — MySQL Service Connect App

## General Rules

- Use migration files for all schema changes.
- Use `BIGINT AUTO_INCREMENT` for primary keys in MVP unless there is a strong reason for UUID.
- Add `created_at` and `updated_at` to mutable business tables.
- Add indexes for list/filter pages before production.
- Do not delete important business records physically; prefer status/archive fields.

## Lead and Assignment Rules

- `leads.accepted_count` must never exceed `leads.max_accept_count`.
- `lead_assignments` must have unique `(lead_id, provider_id)`.
- Lead contact fields are sensitive.
- A lead should have clear status lifecycle.
- Assignment should have separate status lifecycle.

## Credit Rules

- Wallet balance must not be negative.
- Every balance change must have a ledger row in `credit_transactions`.
- Do not update ledger rows after creation.
- Use idempotency key for unlock or payment-related requests that may retry.
- Use transaction and row lock for wallet mutation.

## Locking Rules

For lead unlock:

```sql
SELECT * FROM leads WHERE id = ? FOR UPDATE;
SELECT * FROM provider_credit_wallets WHERE provider_id = ? FOR UPDATE;
```

Then validate and update inside the same transaction.

## Index Rules

Add indexes for:

- Lead status + created time.
- Lead customer id + created time.
- Assignment provider id + status.
- Assignment lead id + status.
- Credit transaction provider id + created time.

## Naming Rules

- Table names: snake_case plural.
- Column names: snake_case.
- FK names: `fk_<table>_<ref>`.
- Unique constraint names: `uq_<meaning>`.
- Index names: `idx_<table>_<columns>`.

## Anti-Patterns

- Do not store credit balance only in transactions without a wallet table for MVP operations.
- Do not modify provider credit without ledger.
- Do not depend on application-only checks for max 3 providers under concurrency.
- Do not use unbounded text fields for filter-heavy columns.
