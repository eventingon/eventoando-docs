# Changelog

All notable changes to the Eventoando API are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.0.0] — 2026-06-11

### Added
- Initial public documentation release
- BFF API endpoints: auth, events, advertisements, budgets, negotiations, payments, ratings, notifications, media, discovery, regions, categories
- OpenAPI 3.0 spec (`schema/openapi.yaml`)
- Guides: organizer flow, advertiser flow, payment/escrow, regions & zones
- SKILL.md for Claude Code / Cowork AI context
- context7.json for Context7 indexing

### Architecture
- BFF → Auth API + Content API pattern documented
- Escrow payment model (`eventingon` type) documented
- MercadoPago PIX + credit card support
