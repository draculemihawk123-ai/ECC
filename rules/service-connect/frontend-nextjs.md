# Frontend Rules — Next.js Service Connect App

## Stack

- Next.js App Router.
- TypeScript required.
- Role-based route groups for customer, provider, and admin.
- API client should centralize JWT handling and error mapping.

## Route Structure

```text
app/
├── customer/
│   └── requests/
├── provider/
│   ├── leads/
│   └── credits/
├── admin/
│   ├── leads/
│   ├── providers/
│   └── credits/
├── login/
└── register/
```

## UI Rules

- Keep role-specific navigation separate.
- Customer UI should focus on request creation and tracking.
- Provider UI should focus on assigned leads, unlock status, and credit balance.
- Admin UI should focus on verification queue, assignment, credit management, and operational dashboard.

## Data Exposure Rules

- Before provider unlock, show masked lead data only.
- Do not display customer phone/contact until backend confirms unlock.
- Frontend route guards are for UX only; backend remains the source of truth.

## API Client Rules

- All API calls go through a shared client.
- Automatically attach Authorization bearer token.
- Handle 401 by redirecting to login or refresh flow.
- Map backend error codes to user-friendly messages.
- Avoid duplicating API URLs across components.

## State Management

Start simple:

- Server components for read-heavy pages where appropriate.
- Client components for forms, unlock actions, and interactive admin assignment.
- Use query libraries only when data refresh/caching becomes complex.

## Forms

- Validate on frontend for UX.
- Backend validation remains mandatory.
- Use explicit form models per role and action.
- Avoid one giant generic form for all lead states.

## Critical Screens for Version 0.1

### Customer

- Register/login.
- Create service request.
- List my requests.
- View request status.

### Provider

- Register/login.
- View assigned leads.
- View credit balance.
- Unlock lead.
- View unlocked lead details.

### Admin

- Login.
- Pending lead verification queue.
- Lead detail/verification page.
- Set lead credit cost.
- Assign providers.
- Provider credit management.

## Anti-Patterns

- Do not hardcode role permissions only in frontend.
- Do not store sensitive lead/contact data in localStorage.
- Do not call backend from many ad-hoc fetch snippets.
- Do not mix admin/provider/customer components without clear boundaries.
