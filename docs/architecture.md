# Architecture

## Overview

Eventoando uses a **BFF (Backend for Frontend)** pattern. Client apps communicate exclusively with the BFF, which orchestrates calls to internal services.

```
┌─────────────────────────────────────┐
│   Client Apps                       │
│   Mobile (Flutter) · Admin (Web)    │
└──────────────┬──────────────────────┘
               │ HTTPS · REST · JSON
               ▼
┌─────────────────────────────────────┐
│   BFF API   :3000                   │
│   /api/v1/bff/*                     │
│   NestJS · JWT guard · Rate limit   │
└────────────┬──────────┬─────────────┘
             │          │
    ┌────────▼──┐  ┌────▼──────────┐
    │ Auth API  │  │ Content API   │
    │ :3001     │  │ :3002         │
    │ NestJS    │  │ NestJS        │
    └────────┬──┘  └────┬──────────┘
             │          │
    ┌─────────▼──────────▼──────────┐
    │     MySQL · Redis              │
    └───────────────────────────────┘
```

## Key Rules

1. **Client apps never call Auth API or Content API directly** — only the BFF.
2. All BFF responses use the envelope: `{ data, total?, metadata? }`.
3. Public endpoints are decorated with `@Public()` — no token required.
4. New entities must be registered in `content.module.ts`.

## Services

### BFF API (`/api/v1/bff/*`)
- Entry point for all client traffic
- Handles authentication (JWT guard), rate limiting, request validation
- Orchestrates calls to Auth API and Content API
- Returns unified response format

### Auth API (internal)
- User registration, login, token issuance and refresh
- OTP management
- Social login (OAuth)

### Content API (internal)
- All domain logic: events, advertisements, negotiations, payments, ratings, etc.
- MySQL persistence via TypeORM
- Redis for caching and pub/sub

## Response Format

**Success:**
```json
{
  "data": { ... },
  "total": 42,
  "metadata": { "page": 1, "limit": 20, "totalPages": 3 }
}
```

**Error:**
```json
{
  "statusCode": 404,
  "message": "Event not found",
  "timestamp": "2026-06-11T12:00:00.000Z",
  "path": "/api/v1/bff/events/bad-uuid"
}
```

## Tech Stack

| Layer | Tech |
|-------|------|
| API Framework | NestJS (Node.js) |
| Database | MySQL 8.x, ~57 tables |
| Cache / Pub-Sub | Redis |
| Auth | JWT (short-lived) + refresh tokens |
| Payments | MercadoPago (PIX, credit card, etc.) |
| Mobile client | Flutter (iOS + Android) |
| Admin client | Flutter Web |
| Infrastructure | Docker, VPS (Hostinger) |
