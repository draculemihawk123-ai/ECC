# Service Connect App — Agent Instructions

These instructions customize the ECC workflow for a customer-provider service connection platform.

## Mission

Build a reliable service lead assignment platform where customers submit service requests, admins verify and assign leads, and providers spend credits to unlock qualified leads.

## Product Roles

- `CUSTOMER`: submits service requests and tracks request status.
- `PROVIDER`: receives assigned leads, spends credit to unlock leads, and later earns trust/review score.
- `ADMIN`: verifies leads, sets lead credit cost, assigns providers, manages credit manually, and monitors quality.

## Core Business Rules

1. A customer-created request becomes a `LEAD` only after admin verification.
2. Admin must set `creditCost` before providers can accept/unlock the lead.
3. Providers cannot unlock a lead unless they have enough credit.
4. A lead can be unlocked by at most 3 providers.
5. Provider credit is deducted atomically when the provider unlocks the lead.
6. Initial trial can grant 10 free credits per provider.
7. In early versions, credit top-up is manual through admin.
8. Marketplace/newsfeed is not part of the MVP. Admin assignment is the source of lead visibility.

## Technical Stack

- Backend: Spring Boot
- Frontend: Next.js
- Database: MySQL
- Authentication: JWT and OAuth2
- Architecture: Clean Architecture inside a modular monolith
- Future scale direction: microservices by domain boundary

## Architecture Rules

Use Clean Architecture boundaries:

```text
Controller / REST Adapter
  -> Application Use Case
    -> Domain Model / Domain Service
      -> Port Interface
        -> Infrastructure Adapter / Repository / External Service
```

Backend packages should be organized by feature/domain where possible:

```text
com.example.serviceconnect
├── customer
├── provider
├── lead
├── credit
├── assignment
├── review
├── notification
├── payment
├── auth
└── shared
```

Each domain module should separate:

- `domain`: entities, value objects, domain services, domain events.
- `application`: use cases, commands, queries, DTOs, ports.
- `infrastructure`: JPA entities, repositories, external adapters.
- `interfaces`: REST controllers and request/response models.

## Version Discipline

Always check `docs/service-connect/version-plan.md` before adding a feature.

Do not implement later-version functionality in earlier versions unless it is a safe foundation.

Current roadmap:

- Version 0.1: Lead Assignment MVP
- Version 0.2: Credit & Lead Quality
- Version 0.3: Review, Trust & Provider Scoring
- Version 0.4: Notification & Automation
- Version 0.5: Manual Payment / Credit Package
- Version 0.6: Semi-Automated Matching
- Version 0.7: Provider Pro / Membership Packages
- Version 0.8: Customer Tracking & Advanced Review
- Version 1.0: Stable Marketplace
- Version 1.1+: Scale with multi-service, multi-location, online payment, mobile app

## Mandatory Checks Before Code

Before implementing any feature, identify:

1. Target version.
2. Impacted role: customer, provider, admin.
3. Domain aggregate affected.
4. API contract.
5. Database tables and indexes.
6. Permission requirements.
7. Race conditions, especially around credit deduction and max-provider-per-lead limit.
8. Tests required.

## Security Requirements

- Never expose customer contact details until provider successfully unlocks the lead.
- Provider can only see assigned leads.
- Customer can only see their own requests.
- Admin can see and manage all records.
- All state-changing APIs require authentication and role authorization.
- Validate all request payloads.
- Avoid leaking internal errors to the frontend.
- Credit deduction must be idempotent and concurrency-safe.

## Testing Strategy

For every use case, prefer tests at three levels:

1. Domain unit test: business rules.
2. Application use-case test: orchestration and permission logic.
3. Integration/API test: persistence, transaction, and endpoint contract.

Critical flows that must have tests:

- Customer creates service request.
- Admin verifies lead and sets credit cost.
- Admin assigns providers.
- Provider unlocks lead using credit.
- Provider cannot unlock without enough credit.
- More than 3 providers cannot unlock the same lead.
- Duplicate unlock request does not double charge.

## AI Agent Behavior

When asked to implement or modify features:

1. Restate the target version and role.
2. Check whether the feature belongs to the current roadmap.
3. Propose minimal changes first.
4. Keep the monolith modular.
5. Add tests for business rules.
6. Update docs when behavior changes.
7. Avoid premature microservices, online payment, marketplace feed, or auto-matching before the planned versions.
