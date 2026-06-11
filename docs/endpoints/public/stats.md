# Public Endpoints

These endpoints require no authentication and are safe to call from public-facing apps.

## GET /bff/public/stats

Returns aggregate platform statistics.

```bash
curl https://www.eventoando.com.br/api/v1/bff/public/stats
```

**Response `200`:**
```json
{
  "data": {
    "totalEvents": 1240,
    "totalProviders": 380,
    "totalCategories": 42,
    "totalRegions": 18
  }
}
```

## GET /bff/public/advertisements

Public advertisement feed without authentication.

```bash
curl "https://www.eventoando.com.br/api/v1/bff/public/advertisements?regionId=<uuid>"
```

## GET /bff/public/categories

Full list of service categories.

```bash
curl https://www.eventoando.com.br/api/v1/bff/public/categories
```

**Response:**
```json
{
  "data": [
    { "id": "uuid-cat-001", "name": "Fotografia", "slug": "fotografia", "icon": "camera" },
    { "id": "uuid-cat-002", "name": "Buffet", "slug": "buffet", "icon": "restaurant" },
    { "id": "uuid-cat-003", "name": "DJ", "slug": "dj", "icon": "music_note" }
  ]
}
```

## GET /bff/public/regions

List of active service regions.

```bash
curl https://www.eventoando.com.br/api/v1/bff/public/regions
```

## POST /bff/waitlist — Join Waitlist

Add an email to the waitlist (pre-launch flow).

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/waitlist \
  -H "Content-Type: application/json" \
  -d '{"email": "interessado@example.com", "name": "Maria"}'
```

## GET /health

Health check for monitoring.

```bash
curl https://www.eventoando.com.br/api/v1/health
# → { "status": "ok", "timestamp": "..." }
```
