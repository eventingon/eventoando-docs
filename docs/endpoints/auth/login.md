# POST /bff/auth/login

Authenticate a user and receive JWT tokens.

**Auth required:** No

## Request

```
POST /api/v1/bff/auth/login
Content-Type: application/json
```

```json
{
  "email": "joao@example.com",
  "password": "secret123"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | ✅ | Valid email address |
| `password` | string | ✅ | Min 6 characters |

## Response `200`

```json
{
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "uuid-example-123",
      "name": "João Silva",
      "email": "joao@example.com",
      "role": "USER"
    }
  }
}
```

## Errors

| Code | Message | Cause |
|------|---------|-------|
| `401` | Invalid credentials | Wrong email or password |
| `422` | Validation failed | Malformed email or password too short |

## GET /bff/auth/me

Returns the current authenticated user.

**Auth required:** Yes

```bash
curl https://www.eventoando.com.br/api/v1/bff/auth/me \
  -H "Authorization: Bearer <accessToken>"
```

**Response `200`:**
```json
{
  "data": {
    "id": "uuid-example-123",
    "name": "João Silva",
    "email": "joao@example.com",
    "role": "USER",
    "createdAt": "2026-01-15T10:00:00.000Z"
  }
}
```
