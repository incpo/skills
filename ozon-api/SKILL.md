---
name: ozon-api
description: "Ozon Seller API reference for building FBS fulfillment features — order listing, packing/shipping, label generation, exemplar marking, acts, and more. Use this skill whenever the user works with Ozon marketplace integration, mentions Ozon API endpoints, FBS/FBO posting workflows, package labels, Honest Sign (Честный Знак) marking, shipping acts, or any code that calls api-seller.ozon.ru. Also trigger when you see imports or calls referencing Ozon posting numbers, Client-Id/Api-Key headers for Ozon, or routes like /v3/posting/fbs/ or /v4/posting/fbs/. Even if the user just says 'pack order', 'get label', or 'ship posting' in the context of this project, use this skill."
---

# Ozon Seller API

## Quick Reference

- **Base URL**: `https://api-seller.ozon.ru`
- **Auth**: Two headers on every request — `Client-Id` (numeric) + `Api-Key`
- **All endpoints are POST** (even reads)
- **Rate limit**: 50 req/sec per seller
- **Content-Type**: `application/json`

## Schema Files

These files live in the project's `docs/` directory. Read them when you need detailed request/response schemas:

| File | When to read | Content |
|------|-------------|---------|
| `docs/ozon-api-fbs.json` | Building FBS features (packing, shipping, labels) | OpenAPI 3.0, 45 FBS endpoints with full req/res schemas |
| `docs/ozon-api-full.json` | Any Ozon API work beyond FBS | OpenAPI 3.0, 412 endpoints across 51 domain tags |
| `docs/ozon-api-instructions.md` | Quick lookup of workflows, statuses, error codes | Markdown reference with tables and examples |

**Start with `docs/ozon-api-instructions.md`** for a quick overview, then dive into the JSON schemas when you need exact field names and types.

## FBS Fulfillment Workflow

This is the core workflow used in this application:

```
1. LIST ORDERS       POST /v3/posting/fbs/list        → filter by awaiting_packaging
2. GET DETAILS       POST /v3/posting/fbs/get          → check requirements object
3. CHECK MARKING     POST /v5/.../exemplar/status      → only if marking required
4. SET EXEMPLARS     POST /v6/.../exemplar/set         → Honest Sign codes
5. VALIDATE          POST /v5/.../exemplar/validate    → validate before ship
6. PACK & SHIP       POST /v4/posting/fbs/ship         → moves to awaiting_deliver
7. GET LABEL         POST /v2/posting/fbs/package-label → returns PDF binary
8. VERIFY            POST /v3/posting/fbs/get           → confirm new status
```

Steps 3-5 only apply to goods requiring mandatory marking (Честный Знак).

## Key Patterns

### Ship request
```json
{
  "posting_number": "12345678-0001-1",
  "packages": [{
    "products": [{ "product_id": 123456, "quantity": 2 }]
  }]
}
```

### Label request (returns raw PDF binary)
```json
{ "posting_number": ["12345678-0001-1"] }
```

### Pagination (max 100)
```json
{ "limit": 100, "offset": 0 }
```

## Posting Statuses

| Status | Meaning |
|--------|---------|
| `awaiting_packaging` | Ready to pack |
| `awaiting_deliver` | Packed, awaiting pickup |
| `delivering` | In transit |
| `delivered` | Complete |
| `cancelled` | Cancelled |
| `arbitration` | Dispute |

## Common Errors

| Code | HTTP | Meaning |
|------|------|---------|
| `POSTING_NOT_FOUND` | 404 | Bad posting number |
| `POSTING_ALREADY_SHIPPED` | 409 | Already packed |
| `POSTING_ALREADY_CANCELLED` | 409 | Was cancelled |
| `INCORRECT_OVH_FOR_POSTING` | 400 | Missing marking codes |
| Rate limit | 429 | Over 50 req/sec |

## Important Constraints

- Ship endpoint: posting must be in `awaiting_packaging` status
- 200 response from ship does NOT guarantee success — always verify with `/v3/posting/fbs/get`
- Exemplar codes max 9 characters, only for marked goods
- Label endpoint returns raw PDF, not JSON
- Posting number format: `XXXXXXXXX-XXXX-X`

## When Building New Features

1. Read `docs/ozon-api-instructions.md` for the workflow overview
2. Look up the specific endpoint in `docs/ozon-api-fbs.json` (FBS) or `docs/ozon-api-full.json` (other domains)
3. Check the OpenAPI schema for exact field names, types, and required flags
4. Handle errors — always check for the common error codes above
5. Respect rate limits (50 req/sec)
