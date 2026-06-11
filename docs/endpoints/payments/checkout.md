# Payments & Checkout

## POST /bff/payments — Create Payment

Initiates a payment for a negotiation. Usually called after `generate-payment`.

**Auth required:** Yes

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "negotiationId": "<negotiation-uuid>",
    "paymentMethod": "pix",
    "amount": 3500.00
  }'
```

**Response `201`:**
```json
{
  "data": {
    "id": "uuid-payment-001",
    "status": "pending",
    "amount": 3500.00,
    "paymentMethod": "pix",
    "pixQrCode": "00020126...",
    "pixKey": "eventoando@mercadopago.com",
    "expiresAt": "2026-06-11T13:00:00.000Z"
  }
}
```

---

## POST /bff/events/:id/payments/checkout

Checkout directly from an event (shortcut for event-level payments).

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/events/<eventId>/payments/checkout \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "paymentMethod": "credit_card",
    "installments": 3
  }'
```

---

## Payment Lifecycle

```
pending → processing → paid → released
                   ↓
               cancelled / refunded
```

| Status | Description |
|--------|-------------|
| `pending` | Awaiting payment |
| `processing` | Payment being processed |
| `paid` | Payment confirmed, escrow held |
| `released` | Escrow released to provider |
| `cancelled` | Payment cancelled |
| `refunded` | Payment refunded to organizer |

---

## POST /bff/payments/:id/confirm — Confirm Delivery

Organizer confirms the service was delivered, triggering escrow release.

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments/<id>/confirm \
  -H "Authorization: Bearer <accessToken>"
```

## POST /bff/payments/:id/release — Release Escrow

Admin or system action to release held funds to the provider.

## POST /bff/payments/:id/refund — Request Refund

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments/<id>/refund \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"reason": "Serviço não entregue conforme combinado"}'
```

---

## Payouts (Provider Side)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/bff/payouts/balance` | Current available balance |
| POST | `/bff/payouts` | Request withdrawal |
| GET | `/bff/payouts/my` | Payout history |

```bash
# Check provider balance
curl https://www.eventoando.com.br/api/v1/bff/payouts/balance \
  -H "Authorization: Bearer <accessToken>"
```

**Response:**
```json
{
  "data": {
    "available": 1750.00,
    "pending": 875.00,
    "currency": "BRL"
  }
}
```
