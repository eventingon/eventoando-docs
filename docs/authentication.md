# Authentication

Eventoando uses **JWT Bearer tokens** for authentication. Tokens are short-lived; use the refresh endpoint to renew.

---

## Endpoints

| Method | Path | Description | Auth required |
|--------|------|-------------|---------------|
| POST | `/bff/auth/register` | Create account | ❌ |
| POST | `/bff/auth/login` | Email + password login | ❌ |
| POST | `/bff/auth/refresh` | Refresh access token | ❌ (refresh token in body) |
| GET  | `/bff/auth/me` | Current user | ✅ |
| POST | `/bff/auth/reset-password` | Send reset code | ❌ |
| POST | `/bff/auth/verify-reset-code` | Verify code + set new password | ❌ |
| POST | `/bff/auth/social-login` | Google / social OAuth | ❌ |
| POST | `/api/v1/auth/request-otp` | Request OTP via SMS/email | ❌ |
| POST | `/api/v1/auth/verify-otp` | Verify OTP code | ❌ |

---

## Token Flow

```
POST /bff/auth/login
  → { accessToken, refreshToken }

  Every request:
    Authorization: Bearer <accessToken>

  When accessToken expires (401):
    POST /bff/auth/refresh
    Body: { "refreshToken": "eyJ..." }
    → { accessToken, refreshToken }
```

---

## Register

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "João Silva",
    "email": "joao@example.com",
    "password": "secret123",
    "termsAccepted": true,
    "phoneNumber": "5511999999999"
  }'
```

Fields:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Full name (min 2 chars) |
| `email` | string | ✅ | Valid email |
| `password` | string | ✅ | Min 6 characters |
| `termsAccepted` | boolean | ✅ | Must be `true` |
| `phoneNumber` | string | ❌ | E.164 format: `5511999999999` |
| `referralCode` | string | ❌ | Referral code from invite link |

---

## Login

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "joao@example.com", "password": "secret123"}'
```

**Response:**
```json
{
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": { "id": "uuid-123", "name": "João Silva", "email": "joao@example.com" }
  }
}
```

---

## Refresh Token

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{"refreshToken": "eyJ..."}'
```

---

## OTP (One-Time Password)

Used for phone/email verification flows.

```bash
# 1. Request OTP
curl -X POST https://www.eventoando.com.br/api/v1/auth/request-otp \
  -H "Content-Type: application/json" \
  -d '{"channel": "sms", "phoneNumber": "5511999999999"}'

# 2. Verify OTP
curl -X POST https://www.eventoando.com.br/api/v1/auth/verify-otp \
  -H "Content-Type: application/json" \
  -d '{"code": "123456", "phoneNumber": "5511999999999"}'
```

Available OTP channels: `GET /api/v1/auth/otp/channels`

---

## Error Responses

| Code | Meaning |
|------|---------|
| `401` | Missing or invalid token |
| `403` | Valid token, insufficient permissions |

On `401`, refresh the token. If refresh also fails (expired), redirect user to login.
