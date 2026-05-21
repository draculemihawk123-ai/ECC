# Service Connect Planning Skill

Use this skill when planning product features for the customer-provider service connection platform.

## Inputs to Collect

- Target version.
- Target role: CUSTOMER, PROVIDER, ADMIN.
- Business objective.
- Lead lifecycle impact.
- Credit impact.
- Data visibility impact.
- Admin operation impact.

## Planning Steps

1. Confirm which roadmap version owns the feature.
2. Identify the user role and business workflow.
3. Identify impacted domain modules.
4. Define the API surface.
5. Define database changes.
6. Define authorization checks.
7. Define concurrency risks.
8. Define test cases.
9. Define documentation updates.

## Version Guardrail

Do not implement future features too early.

Examples:

- Do not add public marketplace feed in version 0.1.
- Do not add online payment before version 1.1+ unless the roadmap changes.
- Do not add full auto-matching before version 0.6.
- Do not split microservices before the monolith boundaries are stable.

## Output Format

For every feature plan, produce:

```md
## Feature

## Target Version

## Roles

## Business Flow

## Backend Changes

## Frontend Changes

## Database Changes

## Security and Permission Checks

## Tests

## Risks

## Out of Scope
```
