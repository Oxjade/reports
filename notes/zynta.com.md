# Zynta — External Security Assessment (1 Sep 2026)

**Source:** `FINAL_REPORT_zynta_2026-09-01.pdf` (13 pp) · Analyst: 0xRobotnick

## Scope

Zynta stablecoin payment rails estate — API, dashboard, vault, staging, supporting infra.

Black-box / external, non-destructive except specified PoC requests.

## Findings summary

| ID | Finding | Severity | Status |
|---|---|---|---|
| F-01 | Unauthenticated webhook event injection (**Ledig**) — prod **and** staging | CRITICAL | CONFIRMED |
| F-02 | Transfers webhook HMAC verification | — | Verified **protected** (revised down) |
| F-03 | Unauthenticated file-upload edge function (Supabase) | — | Confirmed |
| F-04 | Open self-registration on merchant platform | — | Confirmed |
| F-05 | Internet-exposed password vault (Vaultwarden), open registration, reachable admin panel | — | Confirmed |
| F-06 | Exposed Coolify management console | — | Confirmed |
| F-07 | WordPress user enumeration | — | Confirmed |
| F-08 | Kubernetes NLB exposed with misconfigured ArgoCD vhost | — | Confirmed |

Supporting finding: a new **Nomba** webhook endpoint exists but is **not yet wired**
with verification.

## The critical webhook

`POST /api/v1/webhooks/ledig` accepts forged provider events with **no signature
verification**. Depending on downstream ledger validation, this can be weaponized to
signal deposits/transfers that never occurred. Same exposure confirmed on staging.

Webhooks for other providers — transfers, Busha, Fincra, compliance — **are correctly
HMAC-protected**.

## Route B blocked

Credential-based compromise of the crypto custody layer was **not** achievable from
outside: every entry point (Coolify session, DFNS JWT, custody API key, dashboard
session) is auth-gated.

The realistic attack surface is **Route A** — the forged-webhook path — which requires
an operator-run end-to-end test to confirm the money-movement leg.

## Weaponized Route A (operator-run)

1. Register merchant + personal KYC (static data)
2. Create on-ramp order → capture real `onRampId`
3. Forge `on_ramp.funded` / `transfer.completed` to Ledig → `200 {"ok":true}`
4. Ledger credits on forged signal (operator test pending)
5. Sell / withdraw released value

## Re-verification addendum (1 Sep 2026)

A later live probe produced material changes:

| Endpoint | Before | After |
|---|---|---|
| Ledig webhook (staging) | `200 {"ok":true}` | `200 {"ok":true}` — **still live** |
| Ledig webhook (prod) | — | `401 "Invalid ledig webhook signature"` — **fixed / hardened** |
| Transfer + compliance webhooks | `401 HMAC` | `401 HMAC` — unchanged, protected |
| Supabase upload | `200` | `200` — still live |
| Vaultwarden | `200` | `200` — still live (upgraded to 1.37.3) |
| Nomba webhook | `401 "not configured"` | `401 "Invalid nomba webhook signature"` — hardened |