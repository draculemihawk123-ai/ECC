# Backend Rules — Spring Boot Service Connect App

## Stack

- Java 21 or latest stable LTS preferred.
- Spring Boot 3.x preferred for new project.
- MySQL database.
- JWT and OAuth2 authentication.
- Clean Architecture inside modular monolith.

## Package Rules

Organize by business capability, not by technical layer only.

Good:

```text
lead/domain
lead/application
lead/infrastructure
lead/interfaces
```

Avoid:

```text
controller/
service/
repository/
dto/
```

The technical-layer style is acceptable only for very small prototypes, not for this product.

## Controller Rules

- Controllers must not contain business logic.
- Controllers must only:
  - validate request shape,
  - map request to command/query,
  - call use case,
  - map result to response.
- Do not inject JPA repositories into controllers.
- Separate customer/provider/admin controllers.

## Application Use Case Rules

- One use case per business action where practical.
- Use case names should be explicit:
  - `CreateServiceRequestUseCase`
  - `VerifyLeadUseCase`
  - `AssignProvidersToLeadUseCase`
  - `UnlockLeadUseCase`
  - `GrantProviderCreditUseCase`
- Transaction boundary should be at use-case level.
- Use command/query DTOs for input.
- Return application result DTOs, not JPA entities.

## Domain Rules

- Put business invariants in domain model or domain service.
- Lead max accepted provider count is a domain invariant.
- Credit balance cannot be negative.
- Provider cannot unlock unassigned lead.
- Contact data must be masked before unlock.

## Repository Rules

- Application layer depends on ports/interfaces.
- Infrastructure implements ports using Spring Data JPA.
- Do not leak JPA entities into domain/application layers.
- Use explicit query methods for locked operations.

## Transaction and Concurrency Rules

Critical operation: provider unlocks a lead.

Must be atomic:

1. Lock lead.
2. Lock provider wallet.
3. Validate assignment.
4. Validate provider has enough credit.
5. Validate lead accepted count is below max.
6. Deduct credit.
7. Insert credit transaction ledger.
8. Mark assignment as unlocked.
9. Increment lead accepted count.

Use one transaction. Prefer pessimistic locking for MVP.

## Error Handling

Use domain/application exceptions with stable error codes:

- `LEAD_NOT_FOUND`
- `LEAD_NOT_VERIFIED`
- `LEAD_NOT_ASSIGNED_TO_PROVIDER`
- `LEAD_ALREADY_UNLOCKED`
- `LEAD_ACCEPT_LIMIT_REACHED`
- `INSUFFICIENT_CREDIT`
- `FORBIDDEN`
- `VALIDATION_ERROR`

Map errors consistently in global exception handler.

## Testing Rules

Minimum tests for version 0.1:

- Lead creation.
- Lead verification.
- Provider assignment.
- Credit grant.
- Successful unlock.
- Unlock without enough credit.
- Unlock when max provider count reached.
- Duplicate unlock does not double charge.
- Provider cannot unlock non-assigned lead.

## Anti-Patterns

- Do not put all logic in `LeadService`.
- Do not return JPA entities from REST APIs.
- Do not expose customer phone before unlock.
- Do not update wallet balance without ledger entry.
- Do not rely on frontend checks for authorization.
- Do not split into microservices before version 1.1+.
