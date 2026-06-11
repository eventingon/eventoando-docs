# POST /bff/events — Create Event

Creates a new event for the authenticated organizer.

**Auth required:** Yes

## Request

```
POST /api/v1/bff/events
Authorization: Bearer <accessToken>
Content-Type: application/json
```

```json
{
  "title": "Casamento João e Maria",
  "description": "Recepção com 150 convidados no salão do clube",
  "eventType": "private",
  "eventDate": "2026-12-20T18:00:00.000Z",
  "guestCount": 150
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | ✅ | Event title |
| `description` | string | ✅ | Event description |
| `eventType` | enum | ✅ | `private` or `public` |
| `eventDate` | ISO 8601 | ✅ | Event date and time |
| `startDate` | ISO 8601 | ❌ | For multi-day events |
| `endDate` | ISO 8601 | ❌ | For multi-day events |
| `guestCount` | number | ❌ | Expected number of guests |

`eventType` values:
- `private` — closed guest list, residential or rented venue
- `public` — open to the public, parks, public spaces

## Response `201`

```json
{
  "data": {
    "id": "uuid-event-123",
    "title": "Casamento João e Maria",
    "eventType": "private",
    "status": "draft",
    "eventDate": "2026-12-20T18:00:00.000Z",
    "guestCount": 150,
    "organizerId": "uuid-user-456",
    "createdAt": "2026-06-11T12:00:00.000Z"
  }
}
```

## Errors

| Code | Cause |
|------|-------|
| `401` | Not authenticated |
| `422` | Validation error — check `errors` array |

## PUT /bff/events/:id — Update Event

Same body, partial updates supported (only send changed fields).

```bash
curl -X PUT https://www.eventoando.com.br/api/v1/bff/events/<id> \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"guestCount": 200}'
```
