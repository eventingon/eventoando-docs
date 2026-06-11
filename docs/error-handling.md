# Error Handling

## Error Format

All errors return a consistent JSON body:

```json
{
  "statusCode": 422,
  "message": "Validation failed",
  "errors": [
    { "field": "email", "message": "Email deve ser válido" }
  ],
  "timestamp": "2026-06-11T12:00:00.000Z",
  "path": "/api/v1/bff/auth/register"
}
```

## HTTP Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| `200` | OK | — |
| `201` | Created | — |
| `400` | Bad Request | Fix request payload |
| `401` | Unauthorized | Refresh token or re-login |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Duplicate resource (e.g., email already registered) |
| `422` | Unprocessable Entity | Validation errors — check `errors` array |
| `429` | Too Many Requests | Back off and retry after `Retry-After` header |
| `500` | Internal Server Error | Retry with exponential backoff |

## Retry Strategy

```
Retryable: 429, 500, 502, 503, 504
Not retryable: 400, 401, 403, 404, 409, 422

Backoff: 1s → 2s → 4s → 8s (max 3 retries)
```

## Common Errors

### 401 — Token expired
```json
{ "statusCode": 401, "message": "Unauthorized" }
```
Action: `POST /bff/auth/refresh` with `refreshToken`.

### 422 — Validation
```json
{
  "statusCode": 422,
  "message": "Validation failed",
  "errors": [{ "field": "password", "message": "Senha deve ter pelo menos 6 caracteres" }]
}
```
Action: Fix the flagged fields in the request body.

### 429 — Rate limit
```json
{ "statusCode": 429, "message": "Too Many Requests" }
```
Action: Check the `Retry-After` response header and wait before retrying.
