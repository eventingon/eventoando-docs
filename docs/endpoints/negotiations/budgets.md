# Budgets & Negotiations

## Overview

The deal flow between organizer and provider:

```
Organizer creates Event
    ↓
Organizer requests Budget from an Advertisement
    ↓
Provider reviews and responds (accept / counter)
    ↓
Negotiation created → messages exchanged
    ↓
Price finalized → Payment generated
    ↓
Payment confirmed → Escrow released
```

---

## POST /bff/budgets — Request Budget

Request a quote from a specific advertisement for your event.

**Auth required:** Yes

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/budgets \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "<event-uuid>",
    "advertisementId": "<ad-uuid>",
    "message": "Preciso de cobertura fotográfica para casamento com 150 pessoas"
  }'
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `eventId` | UUID | ✅ | Your event |
| `advertisementId` | UUID | ✅ | Provider's advertisement |
| `message` | string | ❌ | Initial message to provider |

**Response `201`:**
```json
{
  "data": {
    "id": "uuid-budget-001",
    "status": "pending",
    "eventId": "<event-uuid>",
    "advertisementId": "<ad-uuid>",
    "createdAt": "2026-06-11T12:00:00.000Z"
  }
}
```

---

## PATCH /bff/budgets/:id/accept — Accept Budget

Organizer accepts a provider's budget response.

```bash
curl -X PATCH https://www.eventoando.com.br/api/v1/bff/budgets/<id>/accept \
  -H "Authorization: Bearer <accessToken>"
```

## PATCH /bff/budgets/:id/reject — Reject Budget

```bash
curl -X PATCH https://www.eventoando.com.br/api/v1/bff/budgets/<id>/reject \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"reason": "Orçamento fora do esperado"}'
```

---

## Negotiation Endpoints

Once a budget is accepted, a negotiation is created automatically.

| Method | Path | Description |
|--------|------|-------------|
| GET | `/bff/negotiations/my-negotiations` | My negotiations |
| GET | `/bff/negotiations/:id` | Get negotiation details |
| POST | `/bff/negotiations/:id/messages` | Send message in negotiation |
| POST | `/bff/negotiations/:id/accept` | Accept negotiation terms |
| POST | `/bff/negotiations/:id/decline` | Decline negotiation |
| PATCH | `/bff/negotiations/:id/finalize-price` | Lock final price |
| POST | `/bff/negotiations/:id/generate-payment` | Generate payment from negotiation |
| POST | `/bff/negotiations/:id/finalize` | Mark negotiation as finalized |
| POST | `/bff/negotiations/:id/cancel` | Cancel negotiation |
| POST | `/bff/negotiations/:id/extend` | Request deadline extension |

## Negotiation Status Values

| Status | Description |
|--------|-------------|
| `pending` | Awaiting provider response |
| `active` | In negotiation |
| `accepted` | Terms accepted by both parties |
| `declined` | Declined by one party |
| `cancelled` | Cancelled after acceptance |
| `finalized` | Service delivered, payment released |
