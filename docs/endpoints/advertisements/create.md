# POST /bff/advertisements — Create Advertisement

Creates a new service listing (advertisement) for the authenticated provider.

**Auth required:** Yes

## Request

```
POST /api/v1/bff/advertisements
Authorization: Bearer <accessToken>
Content-Type: application/json
```

```json
{
  "title": "Fotógrafo Profissional para Casamentos",
  "description": "Cobertura completa com edição profissional, álbum digital e físico.",
  "categoryId": "uuid-category-fotografia",
  "regionIds": ["uuid-region-sp-capital", "uuid-region-abc"],
  "items": [
    {
      "name": "Pacote Básico",
      "description": "4h de cobertura, 200 fotos editadas",
      "price": 2500.00,
      "unit": "event"
    },
    {
      "name": "Pacote Completo",
      "description": "8h de cobertura, 500 fotos editadas + álbum",
      "price": 4500.00,
      "unit": "event"
    }
  ],
  "paymentMethods": ["pix", "credit_card"],
  "paymentType": "eventingon"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | ✅ | Listing title |
| `description` | string | ✅ | Detailed description |
| `categoryId` | UUID | ❌ | Primary category |
| `categoryIds` | UUID[] | ❌ | Multiple categories |
| `regionIds` | UUID[] | ✅ | Regions where service is offered |
| `items` | Item[] | ✅ | Priced service items (at least one) |
| `paymentMethods` | enum[] | ✅ | Accepted payment methods |
| `paymentType` | enum | ❌ | `direct` or `eventingon` (default: `eventingon`) |
| `invoiceType` | enum | ❌ | `provider` or `eventingon` |

**`paymentMethods`** values: `pix`, `credit_card`, `debit_card`, `cash`, `bank_transfer`

**`items` object:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Item/package name |
| `description` | string | ❌ | Item description |
| `price` | number | ✅ | Price in BRL |
| `unit` | string | ❌ | `event`, `hour`, `person`, etc. |

## Response `201`

```json
{
  "data": {
    "id": "uuid-ad-789",
    "title": "Fotógrafo Profissional para Casamentos",
    "status": "draft",
    "paymentType": "eventingon",
    "ownerId": "uuid-user-456",
    "createdAt": "2026-06-11T12:00:00.000Z"
  }
}
```

## Advertisement Lifecycle

```
draft → published → active
               ↓
           paused / archived
```

Use `POST /bff/advertisements/:id/publish` to publish, then `POST /:id/activate` to activate.
