# GET /bff/advertisements — List Advertisements

## Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/bff/advertisements/public` | ❌ | Public provider feed |
| GET | `/bff/advertisements/my-advertisements` | ✅ | My listings |
| GET | `/bff/advertisements` | ✅ | All listings (paginated) |
| GET | `/bff/advertisements/rankings` | ❌ | Top-ranked by region/category |

## GET /bff/advertisements/public

Public endpoint — no token required. Used to browse available service providers.

```bash
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/public?regionId=<uuid>&categoryId=<uuid>&page=1&limit=20"
```

**Query params:**

| Param | Type | Description |
|-------|------|-------------|
| `regionId` | UUID | Filter by region |
| `categoryId` | UUID | Filter by category |
| `page` | number | Page number (default: 1) |
| `limit` | number | Per page (default: 20) |
| `search` | string | Text search |

**Response `200`:**
```json
{
  "data": [
    {
      "id": "uuid-ad-789",
      "title": "Fotógrafo Profissional para Casamentos",
      "description": "Cobertura completa...",
      "categories": [{ "id": "uuid-cat", "name": "Fotografia" }],
      "regions": [{ "id": "uuid-reg", "name": "São Paulo Capital" }],
      "minPrice": 2500.00,
      "rating": 4.8,
      "reviewCount": 23
    }
  ],
  "total": 87,
  "metadata": { "page": 1, "limit": 20 }
}
```

## GET /bff/advertisements/rankings

Top-ranked advertisements, optionally filtered by category or region.

```bash
# Top overall
curl https://www.eventoando.com.br/api/v1/bff/advertisements/rankings

# Top in a category
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/rankings/category/<categoryId>"

# Top in a region
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/rankings/region/<regionId>"
```
