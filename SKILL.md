# Eventoando API — SKILL.md

Use this file as context when developing integrations with the Eventoando platform, debugging API issues, or building features that interact with the BFF.

---

## What is Eventoando?

**Eventoando** (tech name: EventingOn) is a Brazilian marketplace connecting **event organizers** with **service providers** (buffets, photographers, DJs, decorators, security, etc.).

Two personas:
- **Organizer** — creates events, browses providers, requests budgets, pays via platform
- **Advertiser/Provider** — creates service listings, receives budget requests, negotiates, delivers service

---

## Key Concepts

| Entity | Description |
|--------|-------------|
| `Event` | Created by organizer. Has type (private/public), date, guest count, region. |
| `Advertisement` | Service listing by a provider. Belongs to categories and regions. |
| `Catalog Item` | Priced item inside an advertisement (e.g., "Wedding Package - R$3.500"). |
| `Budget` | Organizer's request for a quote from a specific advertisement. |
| `Negotiation` | Active deal between organizer and provider. Has messages, price, status. |
| `Payment` | Transaction tied to a negotiation. Supports escrow model. |
| `Region` | Geographic service area (e.g., "Alphaville SP", "Barueri"). |
| `Category` | Service category (e.g., "Fotografia", "Buffet", "DJ"). |
| `Profile` | A user can have multiple profiles (organizer / provider roles). |
| `Rating` | Post-event review of a provider, linked to negotiation. |

---

## API Architecture

```
Mobile App / Admin Web
        ↓ (only this layer is public)
   BFF API  :3000  →  /api/v1/bff/*
        ↓
  Auth API :3001   +   Content API :3002
        ↓
      MySQL + Redis
```

Base URL: `https://www.eventoando.com.br/api/v1`

---

## Auth Pattern

```bash
# All protected endpoints need:
Authorization: Bearer <accessToken>

# Refresh via: POST /bff/auth/refresh
# Body: { "refreshToken": "eyJ..." }
```

---

## Response Envelope

```json
{ "data": { ... }, "total": 42, "metadata": { "page": 1, "limit": 20 } }
```

Errors:
```json
{ "statusCode": 404, "message": "Event not found", "timestamp": "...", "path": "..." }
```

---

## Payment Model

- **`direct`** — provider handles payment outside platform (Eventoando = showcase only)
- **`eventingon`** — Eventoando escrow: organizer pays 100%, provider gets 50% on booking + 50% on delivery confirmation (via MercadoPago)

Payment methods: `pix`, `credit_card`, `debit_card`, `cash`, `bank_transfer`

---

## Common Questions This Context Answers

- "What endpoint creates an event?" → `POST /bff/events`
- "How does the organizer pay a provider?" → Budget → Negotiation → `POST /bff/payments` with escrow
- "What fields are required for an advertisement?" → `title`, `description`, `regionIds`, `items`, `paymentMethods`
- "How does escrow work?" → See [docs/guides/payment-flow.md](docs/guides/payment-flow.md)
- "How to list providers in a region?" → `GET /bff/advertisements/public?regionId=<uuid>`
- "What are negotiation statuses?" → `pending`, `active`, `accepted`, `declined`, `cancelled`, `finalized`

---

## Useful Files in This Repo

| File | Use when... |
|------|-------------|
| `schema/openapi.yaml` | Generating client SDKs, validating requests |
| `docs/getting-started.md` | Onboarding a new integrator |
| `docs/guides/organizer-flow.md` | Understanding the full organizer journey |
| `docs/guides/payment-flow.md` | Debugging payment or escrow issues |
| `docs/authentication.md` | JWT / OTP / refresh token flow |
| `docs/error-handling.md` | Mapping error codes to user messages |
