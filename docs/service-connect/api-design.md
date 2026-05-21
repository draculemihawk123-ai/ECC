# API Design — Service Connect App

## Common API Response

```json
{
  "success": true,
  "data": {},
  "error": null,
  "meta": {
    "requestId": "...",
    "timestamp": "2026-05-21T00:00:00Z"
  }
}
```

For errors:

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "INSUFFICIENT_CREDIT",
    "message": "Provider does not have enough credit"
  },
  "meta": {
    "requestId": "...",
    "timestamp": "2026-05-21T00:00:00Z"
  }
}
```

## Auth APIs

```http
POST /api/auth/register/customer
POST /api/auth/register/provider
POST /api/auth/login
POST /api/auth/oauth2/login
POST /api/auth/refresh-token
POST /api/auth/logout
GET  /api/auth/me
```

## Customer APIs

```http
POST /api/customer/service-requests
GET  /api/customer/service-requests
GET  /api/customer/service-requests/{id}
PUT  /api/customer/service-requests/{id}
DELETE /api/customer/service-requests/{id}
```

## Admin Lead APIs

```http
GET  /api/admin/leads?status=PENDING_VERIFICATION
GET  /api/admin/leads/{id}
POST /api/admin/leads/{id}/verify
POST /api/admin/leads/{id}/reject
POST /api/admin/leads/{id}/credit-cost
POST /api/admin/leads/{id}/assign-providers
GET  /api/admin/leads/{id}/assignments
```

## Provider Lead APIs

```http
GET  /api/provider/leads/assigned
GET  /api/provider/leads/{id}
POST /api/provider/leads/{id}/unlock
POST /api/provider/leads/{id}/contacted
POST /api/provider/leads/{id}/result
```

## Credit APIs

```http
GET  /api/provider/credits/wallet
GET  /api/provider/credits/transactions

GET  /api/admin/providers/{providerId}/credits/wallet
POST /api/admin/providers/{providerId}/credits/grant
POST /api/admin/providers/{providerId}/credits/deduct
GET  /api/admin/providers/{providerId}/credits/transactions
```

## Review APIs — Version 0.3+

```http
POST /api/customer/reviews
GET  /api/provider/reviews
GET  /api/admin/reviews
POST /api/admin/reviews/{id}/moderate
```

## Notification APIs — Version 0.4+

```http
GET  /api/notifications
POST /api/notifications/{id}/read
POST /api/admin/notifications/send
```

## Payment APIs — Version 0.5+

```http
GET  /api/provider/credit-packages
POST /api/provider/manual-payment-requests
GET  /api/admin/manual-payment-requests
POST /api/admin/manual-payment-requests/{id}/approve
POST /api/admin/manual-payment-requests/{id}/reject
```

## Permission Matrix

| API Group | CUSTOMER | PROVIDER | ADMIN |
|---|---:|---:|---:|
| Customer service requests | Own only | No | Read all |
| Admin lead verify/assign | No | No | Yes |
| Provider assigned leads | No | Own only | Read all |
| Lead unlock | No | Own assigned only | No |
| Provider credit wallet | No | Own only | Yes |
| Manual credit adjustment | No | No | Yes |
| Reviews | Own related | Own related | Moderate |

## API Design Rules

1. Use nouns for resources and verbs for domain actions when state transition matters.
2. Keep customer, provider, and admin API surfaces separate.
3. Never rely on frontend to filter protected data.
4. Return masked lead data before unlock.
5. Return full customer contact only after successful unlock.
6. Use idempotency key for risky operations such as lead unlock and manual payment confirmation.
