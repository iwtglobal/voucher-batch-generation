# Architecture — Electronic Voucher Batch Generation

High-level reference for evaluating **electronic voucher batch generation** inside an electronic voucher management system (EVMS). Educational only; vendor designs vary.

## Logical Components

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Product catalog │────►│ Batch control    │────►│ Secure generator│
│ SKU / expiry    │     │ dual-control jobs│     │ / HSM vault     │
└─────────────────┘     └────────┬─────────┘     └────────┬────────┘
                                 │                        │
                                 ▼                        ▼
                        ┌──────────────────┐     ┌─────────────────┐
                        │ Inventory load   │     │ Checksum / audit│
                        │ opaque stock IDs │     │ seal & archive  │
                        └──────────────────┘     └─────────────────┘
```

## Suggested Batch Metadata

| Field | Meaning |
|-------|---------|
| `batch_id` | Unique pool identifier |
| `sku` / denomination | Product and face value |
| `quantity` | Units requested and accepted |
| `expiry_policy` | Batch-level or per-unit clock |
| `checksum` | Integrity seal before sell |
| `status` | `pending` → `generated` → `active` → `closed` |

## Audit Events Worth Keeping

- Batch request create / approve / reject  
- Generation start and complete with counts  
- Vault write confirmation  
- Inventory load and first-sale marker  
- Batch close / archive with final tallies  

Live product reading: [EVMS page](https://evdsystem.com/electronic-voucher-management-system/), [evdsystem.com](https://evdsystem.com/).
