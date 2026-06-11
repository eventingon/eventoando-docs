# Payment & Escrow

Eventoando supports two payment models and uses MercadoPago as the payment gateway.

---

## Payment Types

### `direct` — Provider handles payment

```
Organizer ─────────────────────────► Provider
           (outside Eventoando)
```

- Eventoando acts as a showcase only
- No transaction protection
- Provider sets up their own payment method

### `eventingon` — Escrow via Eventoando (recommended)

```
Organizer ──── 100% ────► Eventoando (escrow)
                               │
                          ─── 50% ──► Provider (on booking)
                               │
                   (after delivery confirmed)
                               │
                          ─── 50% ──► Provider (on delivery)
```

**Step-by-step:**
1. Organizer accepts negotiation and pays 100% to Eventoando
2. Eventoando releases 50% to provider immediately (booking confirmation)
3. Service is delivered at the event
4. Organizer confirms delivery via `POST /bff/payments/:id/confirm`
5. Eventoando releases remaining 50% to provider

---

## Supported Payment Methods

| Method | `paymentMethod` value | Description |
|--------|-----------------------|-------------|
| PIX | `pix` | Instant Brazilian payment |
| Credit Card | `credit_card` | Up to 12 installments |
| Debit Card | `debit_card` | Direct debit |
| Cash | `cash` | In-person only |
| Bank Transfer | `bank_transfer` | TED/DOC |

---

## Payment Lifecycle

```
pending
  ↓ (user pays)
processing
  ↓ (confirmed by gateway)
paid ──────────────────────────── escrow held
  ↓ (organizer confirms delivery)
released ─────────────────────── provider receives funds
```

Alternate paths:
- `paid → cancelled` (cancelled before delivery)
- `paid → refunded` (dispute resolved in organizer's favor)

---

## PIX Payment Example

```bash
# 1. Generate payment
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "negotiationId": "<neg-uuid>",
    "paymentMethod": "pix",
    "amount": 3200.00
  }'

# Response includes:
# {
#   "data": {
#     "id": "uuid-payment-001",
#     "status": "pending",
#     "pixQrCode": "00020126...",
#     "pixKey": "pagamentos@eventoando.com.br",
#     "expiresAt": "2026-06-11T13:00:00.000Z"
#   }
# }

# 2. After payment is confirmed (webhook or polling):
# Check status
curl https://www.eventoando.com.br/api/v1/bff/payments/<id> \
  -H "Authorization: Bearer <accessToken>"

# 3. After service delivery, confirm:
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments/<id>/confirm \
  -H "Authorization: Bearer <accessToken>"
```

---

## Refunds & Disputes

Refund requests go through:
```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/payments/<id>/refund \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"reason": "Service not delivered as agreed"}'
```

Refund decisions are processed by Eventoando admin. Outcomes:
- Full refund to organizer
- Full release to provider
- Partial split

---

## Provider Payouts

Providers withdraw available funds via:

```bash
# Check balance
GET /bff/payouts/balance

# Request withdrawal
POST /bff/payouts
Body: { "amount": 1750.00 }

# Track history
GET /bff/payouts/my
```
