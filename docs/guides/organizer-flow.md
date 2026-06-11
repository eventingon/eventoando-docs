# Organizer Flow

End-to-end journey: from creating an event to confirming service delivery.

## Overview

```
Register / Login
    ↓
Create Event (draft)
    ↓
Browse Providers (advertisements)
    ↓
Request Budgets
    ↓
Review & Accept Budget → Negotiation starts
    ↓
Negotiate price + terms (optional messages)
    ↓
Finalize Price → Generate Payment
    ↓
Pay (PIX / credit card)
    ↓
Attend event
    ↓
Confirm Delivery → Funds released to provider
    ↓
Rate Provider
```

---

## Step 1 — Create an event

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/events \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Aniversário 40 anos",
    "description": "Festa para 80 pessoas em salão alugado",
    "eventType": "private",
    "eventDate": "2026-11-15T19:00:00.000Z",
    "guestCount": 80
  }'
# Save the event "id" from the response
```

## Step 2 — Browse providers

```bash
# Get active regions first
curl https://www.eventoando.com.br/api/v1/bff/regions/active

# Get categories
curl https://www.eventoando.com.br/api/v1/bff/public/categories

# Browse providers in region + category
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/public?regionId=<uuid>&categoryId=<uuid>"
```

## Step 3 — Request budgets

```bash
# Request budget from a provider
curl -X POST https://www.eventoando.com.br/api/v1/bff/budgets \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "<event-uuid>",
    "advertisementId": "<ad-uuid>",
    "message": "Gostaria de saber disponibilidade para 80 pessoas"
  }'
```

You can request budgets from multiple providers simultaneously.

## Step 4 — Accept a budget

Once the provider responds, accept the budget to start the negotiation:

```bash
curl -X PATCH https://www.eventoando.com.br/api/v1/bff/budgets/<budget-id>/accept \
  -H "Authorization: Bearer <accessToken>"
```

## Step 5 — Negotiate (optional)

Exchange messages and finalize price:

```bash
# Send message
curl -X POST https://www.eventoando.com.br/api/v1/bff/negotiations/<neg-id>/messages \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"content": "Poderia incluir álbum físico no pacote?"}'

# Lock final price
curl -X PATCH https://www.eventoando.com.br/api/v1/bff/negotiations/<neg-id>/finalize-price \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"finalPrice": 3200.00}'
```

## Step 6 — Pay

```bash
# Generate payment from negotiation
curl -X POST https://www.eventoando.com.br/api/v1/bff/negotiations/<neg-id>/generate-payment \
  -H "Authorization: Bearer <accessToken>"

# Pay via PIX
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"negotiationId": "<neg-uuid>", "paymentMethod": "pix", "amount": 3200.00}'
# Response includes PIX QR code and key
```

## Step 7 — Confirm delivery after the event

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments/<payment-id>/confirm \
  -H "Authorization: Bearer <accessToken>"
```

This releases the escrow to the provider.

## Step 8 — Rate the provider

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/ratings \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "negotiationId": "<neg-uuid>",
    "score": 5,
    "comment": "Excelente trabalho, muito profissional!"
  }'
```

---

## Key Notes

- Events start as `draft`. Publish with `POST /bff/events/:id/publish` to make them visible.
- You can request budgets from multiple providers at the same time.
- The escrow model (50% on booking + 50% on delivery) only applies to `paymentType: eventingon` advertisements.
- See [payment-flow.md](payment-flow.md) for full escrow details.
