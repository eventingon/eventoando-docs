# Getting Started

This guide walks you through your first authenticated request to the Eventoando API.

## Prerequisites

- HTTP client (curl, Insomnia, Postman)
- Base URL: `https://www.eventoando.com.br/api/v1`

---

## Step 1 — Register

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "João Silva",
    "email": "joao@example.com",
    "password": "secret123",
    "termsAccepted": true
  }'
```

**Response:**
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

## Step 2 — Login (if already registered)

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "joao@example.com", "password": "secret123"}'
```

Save the `accessToken` — you'll need it for all authenticated requests.

## Step 3 — Make an authenticated request

```bash
# Get your profile
curl https://www.eventoando.com.br/api/v1/bff/auth/me \
  -H "Authorization: Bearer <accessToken>"

# List active regions (no auth needed)
curl https://www.eventoando.com.br/api/v1/bff/regions/active

# List public providers in a region
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/public?regionId=<uuid>"
```

## Step 4 — Create your first event

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/events \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Meu Evento",
    "description": "Descrição do evento",
    "eventType": "private",
    "eventDate": "2026-12-25T18:00:00.000Z",
    "guestCount": 50
  }'
```

---

## Next Steps

- [Authentication deep-dive](authentication.md) — token refresh, OTP, social login
- [Organizer flow](guides/organizer-flow.md) — full journey from event creation to payment
- [Advertiser flow](guides/advertiser-flow.md) — create a listing and receive requests
