# POST /bff/auth/register

Create a new user account.

**Auth required:** No

## Request

```
POST /api/v1/bff/auth/register
Content-Type: application/json
```

```json
{
  "name": "João Silva",
  "email": "joao@example.com",
  "password": "secret123",
  "termsAccepted": true,
  "phoneNumber": "5511999999999"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Full name (min 2 chars) |
| `email` | string | ✅ | Valid email |
| `password` | string | ✅ | Min 6 characters |
| `termsAccepted` | boolean | ✅ | Must be `true` |
| `phoneNumber` | string | ❌ | E.164 format: `5511999999999` |
| `referralCode` | string | ❌ | Referral code from invite link |
| `utmSource` | string | ❌ | Marketing attribution |
| `utmMedium` | string | ❌ | Marketing attribution |
| `utmCampaign` | string | ❌ | Marketing attribution |

## Response `201`

```json
{
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "uuid-example-123",
      "name": "João Silva",
      "email": "joao@example.com"
    }
  }
}
```

## Errors

| Code | Message | Cause |
|------|---------|-------|
| `409` | Email already in use | Account with that email exists |
| `422` | Validation failed | Check `errors` array |
