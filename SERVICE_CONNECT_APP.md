# Service Connect App Customization

This fork customizes the core ECC concept into a project-specific AI workflow pack for a service connection platform.

## Product Context

The target product is an app connecting customers with service providers. It starts as a monolith using Clean Architecture and is designed to scale later into microservices.

## Main Roles

- `CUSTOMER`: creates service requests / leads.
- `PROVIDER`: receives assigned leads and spends credits to unlock qualified lead details.
- `ADMIN`: verifies leads, sets credit cost, assigns suitable providers, and manages provider credit manually.

## Confirmed Business Flow

1. Customer creates a service request.
2. Admin verifies the request.
3. Admin sets the lead credit cost.
4. Admin assigns the lead to suitable providers.
5. Provider sees assigned lead summary.
6. Provider spends credit to accept/unlock the lead.
7. A lead can be accepted by at most 3 providers.
8. When provider credit is depleted, provider contacts admin.

## Target Stack

- Backend: Spring Boot
- Frontend: Next.js
- Database: MySQL
- Auth: JWT and OAuth2
- Initial architecture: Modular monolith with Clean Architecture
- Future architecture: Microservices for scale

## Custom Content Added

- `AGENTS.service-connect.md`: agent instructions for this product.
- `docs/service-connect/`: product, domain, architecture, API, database, and version plan.
- `rules/service-connect/`: backend, frontend, database, and security rules.
- `skills/service-connect-*`: project-specific ECC-style skills for planning, architecture, and versioned delivery.

## How to Use With Claude Code / AI Coding Agents

Copy or reference these files in your product repository:

```text
AGENTS.service-connect.md
SERVICE_CONNECT_APP.md
docs/service-connect/
rules/service-connect/
skills/service-connect-*/
```

Recommended usage:

1. Start every new feature by reading `docs/service-connect/version-plan.md`.
2. For backend work, apply `rules/service-connect/backend-springboot.md`.
3. For frontend work, apply `rules/service-connect/frontend-nextjs.md`.
4. For database work, apply `rules/service-connect/database-mysql.md`.
5. For auth, permissions, and data exposure, apply `rules/service-connect/security.md`.
6. For business flow decisions, use `skills/service-connect-planning`.
7. For implementation structure, use `skills/service-connect-clean-architecture`.
8. For version scope control, use `skills/service-connect-versioning`.
