# GET /bff/events — List Events

## Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| GET | `/bff/events/upcoming` | ❌ | Upcoming public events |
| GET | `/bff/events/my-events` | ✅ | Authenticated user's events |
| GET | `/bff/events` | ✅ | All events (paginated) |

## GET /bff/events/upcoming

Returns upcoming public events. No authentication required.

```bash
curl "https://www.eventoando.com.br/api/v1/bff/events/upcoming?page=1&limit=20"
```

**Query params:**

| Param | Type | Description |
|-------|------|-------------|
| `page` | number | Page number (default: 1) |
| `limit` | number | Items per page (default: 20, max: 100) |

**Response `200`:**
```json
{
  "data": [
    {
      "id": "uuid-event-123",
      "title": "Festival de Verão",
      "eventType": "public",
      "eventDate": "2026-12-31T21:00:00.000Z",
      "guestCount": 500
    }
  ],
  "total": 12,
  "metadata": { "page": 1, "limit": 20 }
}
```

## GET /bff/events/my-events

Returns events belonging to the authenticated user.

```bash
curl https://www.eventoando.com.br/api/v1/bff/events/my-events \
  -H "Authorization: Bearer <accessToken>"
```

## GET /bff/events/:id

Returns a single event by ID.

```bash
curl https://www.eventoando.com.br/api/v1/bff/events/<event-uuid> \
  -H "Authorization: Bearer <accessToken>"
```

**Response `200`:**
```json
{
  "data": {
    "id": "uuid-event-123",
    "title": "Casamento João e Maria",
    "description": "Recepção com 150 convidados",
    "eventType": "private",
    "status": "published",
    "eventDate": "2026-12-20T18:00:00.000Z",
    "guestCount": 150,
    "organizerId": "uuid-user-456",
    "createdAt": "2026-06-11T12:00:00.000Z"
  }
}
```

## Event Status Values

| Status | Description |
|--------|-------------|
| `draft` | Created but not published |
| `published` | Visible and accepting providers |
| `cancelled` | Cancelled by organizer |
| `completed` | Event finished |
