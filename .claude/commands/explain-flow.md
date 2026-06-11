# /explain-flow

Explain a complete user flow in the Eventoando platform.

## Usage

```
/explain-flow <flow-name>
```

## Available flows

- `organizer` — event creation to payment confirmation
- `advertiser` — listing creation to payout
- `payment` — escrow model, PIX, confirmation
- `negotiation` — budget request to finalized deal
- `auth` — registration, login, token refresh

## Instructions for the agent

1. Load the relevant guide from `docs/guides/`
2. Summarize the flow in numbered steps with the key API calls
3. Highlight authentication requirements
4. Note any gotchas or common errors
