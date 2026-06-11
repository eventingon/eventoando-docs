# /find-endpoint

Given a feature description, find the relevant Eventoando BFF endpoint(s).

## Usage

```
/find-endpoint <description>
```

## Examples

- `/find-endpoint create event` → `POST /bff/events`
- `/find-endpoint list providers in region` → `GET /bff/advertisements/public?regionId=`
- `/find-endpoint accept budget` → `PATCH /bff/budgets/:id/accept`
- `/find-endpoint release escrow` → `POST /bff/payments/:id/release`

## Instructions for the agent

1. Search `README.md` endpoint table for the keyword
2. If not found, scan `docs/endpoints/` directory
3. Return: method, full path, auth requirement, and link to docs file
4. If multiple matches, list all
