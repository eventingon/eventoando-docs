# Regions & Zones

Eventoando uses a geographic model to match organizers with nearby providers.

---

## Concepts

**Region** — A named service area (e.g., "Alphaville SP", "Barueri", "São Paulo Capital").  
**Zone** — Regions can have service relationships: a provider in a peripheral region can serve a central region.

---

## How it works

1. Providers select which `regionIds` their advertisement covers
2. Organizers create events associated with a region
3. When browsing providers, filter by `regionId` to see who serves that area
4. A provider in "Osasco" may also appear when searching in "Alphaville SP" if there is a zone relationship

---

## API

```bash
# List all active regions (no auth)
curl https://www.eventoando.com.br/api/v1/bff/regions/active

# List all regions (authenticated)
curl https://www.eventoando.com.br/api/v1/bff/regions \
  -H "Authorization: Bearer <accessToken>"

# Get a specific region
curl https://www.eventoando.com.br/api/v1/bff/regions/<region-id> \
  -H "Authorization: Bearer <accessToken>"
```

**Region object:**
```json
{
  "id": "uuid-region-001",
  "name": "Alphaville SP",
  "city": "Barueri",
  "state": "SP",
  "status": "active",
  "parentRegionId": null
}
```

---

## Finding providers in a region

```bash
# Public advertisement feed filtered by region
curl "https://www.eventoando.com.br/api/v1/bff/advertisements/public?regionId=<uuid>"

# Nearby providers (geolocation-based)
curl "https://www.eventoando.com.br/api/v1/bff/discovery/nearby?lat=-23.5&lng=-46.6" \
  -H "Authorization: Bearer <accessToken>"
```

---

## Geocoding

Convert an address to coordinates:

```bash
curl -X POST https://www.eventoando.com.br/api/v1/bff/discovery/geocode \
  -H "Authorization: Bearer <accessToken>" \
  -H "Content-Type: application/json" \
  -d '{"address": "Av. Paulista, 1000, São Paulo, SP"}'
```

Lookup by postal code (CEP):

```bash
curl https://www.eventoando.com.br/api/v1/bff/addresses/cep/01310100 \
  -H "Authorization: Bearer <accessToken>"
```
