# Electronic Voucher Batch Generation

An educational guide to **electronic voucher batch generation** inside an electronic voucher management system (EVMS / EVD). Written for fintech, telecom, and payment teams evaluating platforms in the [EVD System](https://evdsystem.com/) and [MoboGage](https://evdsystem.com/about-mobogage/) family.

---

## What Is Electronic Voucher Batch Generation?

**Electronic voucher batch generation** is the controlled creation of a finite pool of digital vouchers (PINs, tokens, or opaque stock IDs) with shared product attributes: denomination, expiry, issuer SKU, and security metadata. A batch is the atomic unit of inventory provisioning in EVMS — finance, audit, and distribution all hang on batch identity.

Unlike one-off PIN minting, batch generation binds quantity, cryptographic material, and lifecycle policy into a single auditable job. Weak batch design leads to inventory drift, untraceable stock, and settlement breaks between issuer and reseller networks.

### Why batch generation matters

- **Inventory integrity** — every sellable unit traces to a known batch and checksum  
- **Security** — PINs stay vaulted; only opaque IDs circulate in ordinary views  
- **Finance close** — sold, redeemed, and expired counts roll up by batch  
- **Operations** — reprints, voids, and recalls target a defined stock set  

Batch generation is not a marketing feature; it is the foundation of trustworthy EVD stock.

---

## Architecture Overview: Batch Generation Pipeline

```
Product catalog (SKU, face value, expiry rules)
        │
        ▼
Batch request ──► Secure generator / HSM vault
        │                      │
        ▼                      ▼
  Metadata + qty         Encrypted PIN pool
        │                      │
        └──────────┬───────────┘
                   ▼
         Inventory service (available stock)
                   │
                   ▼
         Channels: POS / API / reseller portal
```

### Core layers

| Layer | Responsibility |
|-------|----------------|
| **Catalog** | Defines denomination, product type, and expiry templates |
| **Batch control** | Accepts quantity, assigns batch ID, records requester and purpose |
| **Secure generator** | Creates PIN/token material in a vaulted environment |
| **Inventory load** | Marks units `available` with opaque stock IDs |
| **Audit & checksum** | Stores hashes so later reconciles can detect tampering |

EVMS platforms keep generation events append-only so support can prove when and by whom a pool was created.

---

## How Electronic Voucher Batch Generation Works

### 1. Define the product and policy

Operators select SKU, face value, expiry clock (batch-level or per-unit), and channel eligibility. Policy may also constrain which resellers can receive the stock.

### 2. Submit a batch job

A dual-controlled request specifies quantity and purpose (e.g., agent float refill, promo campaign). The system assigns a unique batch ID and locks the request for generation.

### 3. Generate and vault secrets

PIN or token material is created in a secure module. Cleartext never lands in ordinary databases; only encrypted blobs and opaque stock IDs are written to inventory.

### 4. Load inventory and release

Units move to `available`. Checksums and counts are sealed. Channels can now reserve and sell against the batch under normal EVD lifecycle rules.

### 5. Monitor and close

Daily jobs compare generated vs. sold vs. redeemed. When a batch is exhausted or expired, it is archived with final tallies for finance.

---

## Patterns and Use Cases

1. **Telecom airtime EVD** — Large denomination batches for agent POS and USSD channels.  
2. **Gift card programs** — Brand-specific SKUs with campaign expiry and activation rules.  
3. **Loyalty / promo codes** — Smaller batches, shorter life, tighter fraud velocity limits.  
4. **Multi-tier reseller stock** — Parent generates; child nodes receive allocated subsets.  
5. **Import vs. generate** — Some issuers import pre-minted files; the batch control layer still applies.

Platforms such as EVD System / MoboGage implement electronic voucher batch generation alongside lifecycle, PIN security, and reseller distribution so inventory stays auditable from mint to redeem.

---

## Implementation Considerations

- **Idempotent jobs** — retries must not double-create the same batch ID  
- **Quantity caps** — hard limits per role reduce blast radius of misconfiguration  
- **Vault separation** — generation hosts should not run ordinary web workloads  
- **Checksum sealing** — store hashes before any channel can sell  
- **Dual control** — require two actors for high-value or high-volume batches  
- **Reconciliation grain** — finance needs generated, available, sold, redeemed, expired cuts  

Choosing a batch generation model should prioritize auditability and secret hygiene over UI speed.

---

## FAQ

**Is a batch the same as a denomination?**  
No. A denomination is a face-value SKU; a batch is a finite pool of units for that (or related) SKUs created in one job.

**Can PINs be regenerated for the same batch?**  
No. Reprints re-reveal under policy; they do not mint a second secret for the same stock ID.

**What is an import batch?**  
A batch whose secrets were produced by an external issuer and loaded under the same control and checksum rules as native generation.

**How does expiry attach to a batch?**  
Either a single batch expiry or per-unit clocks derived from generation time and product policy.

**Who can request generation?**  
Typically privileged operators with dual control; reseller portals usually allocate existing stock rather than mint new PINs.

**How does this relate to MoboGage / EVD System?**  
EVD System is MoboGage’s electronic voucher distribution and management platform family; batch generation is how EVMS inventory is provisioned before POS and API sales. See the [electronic voucher management system](https://evdsystem.com/electronic-voucher-management-system/) overview when evaluating product fit.

---

## Further Reading / Related Industry Resources

- [Electronic voucher management system](https://evdsystem.com/electronic-voucher-management-system/) — EVMS product context  
- [EVD System home](https://evdsystem.com/) — platform overview for digital value distribution  

See also [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) for a component view.

---

## License

Documentation in this repository is provided under the MIT License. See [LICENSE](./LICENSE).

*Educational material only. Not a substitute for vendor due diligence or regulatory advice. MoboGage and EVD System refer to offerings on evdsystem.com.*
