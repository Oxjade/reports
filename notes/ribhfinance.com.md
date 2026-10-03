# RIBH Finance: Security Assessment & Fund-Drain Analysis (12 Aug 2026)

**Source:** `RIBH_Security_Assessment_2026-08-12.pdf` (20 pp) · Analyst: 0xRobotnick

## Scope

`www.ribhfinance.com` · `api.ribhfinance.com` · `dashboard.ribhfinance.com`

Nigerian fintech offering USDC-backed payments: fiat rails (Nuvion, PalmPay, Fincra,
Brails), crypto rails (Circle + CCTP), and KYC via Didit/Smile.

Evidence window 2026-08-11T20:00Z → 2026-08-12T01:00Z. All testing used fabricated
identities, invalid bank details, zero/tiny amounts, read-only access. **No real funds
moved. No real customer data exported.**

## Critical findings

| ID | Finding |
|---|---|
| C1 | Social-login JWT accepted with **NO signature verification** → arbitrary account creation / takeover on any email |
| C2 | Email-keyed identity: forged Google token for **ANY** email mints a live session on that account (`support@ribhfinance.com` = role superadmin) |
| C3 | Email change without verification → permanent victim lockout |
| C4 | Live `sk_live_*` production API keys + `whsec_*` minted with **zero KYC** |
| C5 | Cross-user IDOR on `/transaction/{id}` → full transaction data disclosure |
| C6 | Merchant money rails reachable from forged identity: customers, orders, payouts, M-Pesa destinations |

## D-class (fund-drain pass)

| ID | Finding |
|---|---|
| D1 | Payout creation without balance/amount validation: negative **and** zero amounts accepted |
| D2 | Ledger integrity: `/public/order` accepts `amount=-1`; `/public/transaction` accepts `amount=0` |
| D5 | AWS Textract OCR cost-amplification: unbounded calls |

## Other

- **F10**: Unauthenticated provider webhook family (incl. PalmPay payout) accepts any body.
- **Squat**: create-on-login mass auto-registration (62 forged `@ribhfinance.com`
  identities), cleaned up via self-deletion.

## Attack chain demonstrated end to end

Forged Google JWT → session (C1) → mint `sk_live` key (C4) → create customer + wallet →
reach payout rails (C6) → payout validation failure (D1).

## Controls verified (negative results)

Section 9 documents controls that held: crypto-rail credential handling, KYC provider
boundary, and treasury pool authorization.