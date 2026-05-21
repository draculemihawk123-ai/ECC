# Service Connect Clean Architecture Skill

Use this skill when implementing backend features in the Spring Boot modular monolith.

## Architecture Goal

Keep the monolith modular and ready for later service extraction without introducing distributed complexity too early.

## Layering

```text
interfaces -> application -> domain
infrastructure -> application ports
```

## Implementation Checklist

For each use case:

1. Create command/query object in application layer.
2. Create use case class in application layer.
3. Put business rules in domain entity/domain service.
4. Define repository/output ports in application layer.
5. Implement persistence adapter in infrastructure layer.
6. Expose controller in interfaces layer.
7. Add tests for domain and use case behavior.

## Recommended Use Cases for Version 0.1

- `CreateServiceRequestUseCase`
- `VerifyLeadUseCase`
- `RejectLeadUseCase`
- `SetLeadCreditCostUseCase`
- `AssignProvidersToLeadUseCase`
- `GrantProviderCreditUseCase`
- `UnlockLeadUseCase`

## Domain Modules

- customer
- provider
- lead
- assignment
- credit
- auth
- shared

## Rules

- Controllers never call repositories directly.
- Use cases own transaction boundaries.
- Domain does not depend on Spring.
- Infrastructure does not leak JPA entities upward.
- Return DTOs to API clients.
- Keep feature boundaries explicit.

## Output Format for Implementation Advice

```md
## Module

## Use Case

## Domain Objects

## Ports

## Persistence Adapter

## Controller

## Transaction Boundary

## Tests
```
