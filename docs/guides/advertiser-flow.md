# Advertiser (Provider) Flow

End-to-end journey: from creating a service listing to receiving payment.

## Overview

```
Register / Login
    ↓
Create Advertisement (listing with pricing)
    ↓
Add Catalog Items (packages / prices)
    ↓
Publish Advertisement
    ↓
Receive Budget Requests from Organizers
    ↓
Review Event + Respond to Budget
    ↓
Negotiate terms (optional messages)
    ↓
Accept final price
    ↓
Deliver service at event
    ↓
Organizer confirms delivery
    ↓
Receive payout (escrow released)
```

---

## Step 1 — Create a listing

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/advertisements \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Buffet Completo para Festas",
    "description": "Buffet frio e quente com garçons, louças e decoração de mesa.",
    "categoryId": "<category-uuid>",
    "regionIds": ["<region-uuid>"],
    "items": [
      {
        "name": "Pacote 50 pessoas",
        "description": "Buffet completo para até 50 convidados",
        "price": 3500.00,
        "unit": "event"
      }
    ],
    "paymentMethods": ["pix", "credit_card"],
    "paymentType": "eventingon"
  }'
# Save the advertisement "id"
```

## Step 2 — Add/manage catalog items

```bash
# Add more items to the catalog
curl -X POST https://www.eventoando.com.br/api/v1/bff/catalog/<ad-id>/items \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Pacote 100 pessoas",
    "price": 6000.00,
    "unit": "event"
  }'
```

## Step 3 — Publish the listing

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/advertisements/<id>/publish \
  -H "Authorization: Bearer <accessToken>"

curl -X POST https://www.eventoando.com.br/api/v1/bff/advertisements/<id>/activate \
  -H "Authorization: Bearer <accessToken>"
```

> **Until published, the listing is `draft` and only visible to you (the owner) and admins.**
> `GET /bff/advertisements/:id` returns a non-ACTIVE listing only to its owner or an admin —
> everyone else gets `404`. Public listings (`/public`, `/explore-data`, `/recent`) only show
> `status = active`. Provider name/email/phone shown on the detail come from your **user profile**
> (resolved as `ownerName`/`ownerEmail`/`ownerPhone`), not from the listing itself — keep them filled.

## Step 4 — Receive and respond to budget requests

Check incoming budget requests:

```bash
curl https://www.eventoando.com.br/api/v1/bff/budgets \
  -H "Authorization: Bearer <accessToken>"
```

Respond to a budget (provider side — via negotiation):

```bash
# Once budget is accepted by organizer, a negotiation is created
# Check your negotiations
curl https://www.eventoando.com.br/api/v1/bff/negotiations/my-negotiations \
  -H "Authorization: Bearer <accessToken>"

# Send a message with your proposal
curl -X POST https://www.eventoando.com.br/api/v1/bff/negotiations/<neg-id>/messages \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"content": "Temos disponibilidade. Posso incluir mesa de frios por R$500 adicional."}'
```

## Step 5 — Finalize and confirm

```bash
# Confirm your availability
curl -X POST https://www.eventoando.com.br/api/v1/bff/negotiations/<neg-id>/provider-confirm \
  -H "Authorization: Bearer <accessToken>"
```

## Step 6 — Track payments and request payout

```bash
# Check your balance
curl https://www.eventoando.com.br/api/v1/bff/payouts/balance \
  -H "Authorization: Bearer <accessToken>"

# Request payout when funds are available
curl -X POST https://www.eventoando.com.br/api/v1/bff/payouts \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"amount": 1750.00}'
```

---

## Key Notes

- Advertisements can cover multiple regions simultaneously via `regionIds`.
- `paymentType: "eventingon"` = Eventoando escrow (recommended for trust). `"direct"` = you handle payment outside the platform.
- Provider receives 50% on booking confirmation + 50% after organizer confirms delivery.
- See [payment-flow.md](payment-flow.md) for escrow details.
