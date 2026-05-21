# Architecture — Service Connect App

## Architecture Style

Start with a modular monolith using Clean Architecture. Split into microservices later only when product-market fit and operational load justify it.

## Monolith Module Boundaries

```text
auth
customer
provider
lead
assignment
credit
review
notification
payment
admin
shared
```

## Clean Architecture Layers

```text
interfaces
  REST controllers, request/response DTOs, authentication filters

application
  use cases, commands, queries, ports, transaction boundaries

domain
  aggregates, entities, value objects, domain services, domain events

infrastructure
  JPA entities, Spring Data repositories, MySQL adapters, email/SMS adapters, OAuth2 adapters
```

## Backend Package Convention

```text
com.yourcompany.serviceconnect.lead
├── domain
│   ├── Lead.java
│   ├── LeadStatus.java
│   └── LeadPolicy.java
├── application
│   ├── VerifyLeadUseCase.java
│   ├── AssignLeadUseCase.java
│   ├── UnlockLeadUseCase.java
│   └── port
├── infrastructure
│   ├── LeadJpaEntity.java
│   ├── LeadJpaRepository.java
│   └── LeadPersistenceAdapter.java
└── interfaces
    ├── AdminLeadController.java
    ├── ProviderLeadController.java
    └── CustomerLeadController.java
```

## Frontend Structure

Recommended Next.js App Router structure:

```text
app/
├── customer/
│   ├── requests/
│   └── profile/
├── provider/
│   ├── leads/
│   ├── credits/
│   └── profile/
├── admin/
│   ├── leads/
│   ├── providers/
│   ├── credits/
│   └── dashboard/
├── login/
└── api-client/
```

## Auth and Authorization

- JWT for API access.
- OAuth2 optional for social login.
- Access token should include user id and role.
- Backend must verify role on every protected endpoint.
- Frontend route guards are only UX, not security.

## Domain Events

Use in-process domain events in the monolith first:

- `LeadCreated`
- `LeadVerified`
- `LeadAssigned`
- `LeadUnlocked`
- `CreditDeducted`
- `ProviderCreditLow`
- `LeadFullyAccepted`

Later, these events can become Kafka/RabbitMQ messages when splitting services.

## Future Microservice Candidates

Do not split early. Candidate boundaries for version 1.1+:

- Identity/Auth Service
- Lead Service
- Provider Service
- Credit/Payment Service
- Notification Service
- Review/Trust Service
- Matching Service

## Technical Principles

1. Domain rules live in domain/application, not controllers.
2. Controllers only map HTTP to use cases.
3. Infrastructure depends on application ports, not vice versa.
4. Database transactions belong to application use cases.
5. Cross-module communication should go through use cases or domain events.
6. No direct repository access from controllers.
7. Avoid premature distributed transactions.
