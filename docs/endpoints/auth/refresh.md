# POST /bff/auth/refresh

Exchange a refresh token for a new access token.

**Auth required:** No (refresh token in body)

## Request

```
POST /api/v1/bff/auth/refresh
Content-Type: application/json
```

```json
{ "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
```

## Response `200`

```json
{
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

## Errors

| Code | Message | Cause |
|------|---------|-------|
| `401` | Invalid refresh token | Token expired or revoked → redirect to login |
