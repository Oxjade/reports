# NectarFi: Complete Security Assessment (13–27 Aug 2026)

**Source:** `nectarfi_security_report.pdf` (10 pp) · Analyst: 0xRobotnick

## Scope

Pan-African fintech (virtual NGN accounts, USDC off-ramp, business banking).
Next.js frontends over **two independently reachable API backends** (Express on
Railway, Rust on Fly.io), plus a documented developer platform.

Consumer app · business platform · mobile API · admin panel · developer API.

Method: black-box, unauthenticated external assessment: infrastructure enumeration,
HTTP probing, static analysis of shipped client bundles, known-CVE framework sweep,
CVE-2025-55182 deep-dive. Exfiltration policy honored: no third-party callbacks.

**11 confirmed findings.**

## Severity

| ID | Finding | Severity |
|---|---|---|
| F-08 | Next.js 15.3.2 (react 19.0.0) inside exploited range for **CVE-2025-55182**: pre-auth RSC deserialization RCE (upstream CRITICAL, CVSS 10.0) | CRITICAL (upstream) |
| F-01 | Production financial APIs **and** frontend planes reachable without Cloudflare edge protection; origin hostnames hardcoded in public JS bundles; Railway anycast edge terminates TLS for custom domains by SNI | HIGH |
| F-10 | Edge-plane bypass of Cloudflare WAF mitigation for unpatched Next.js CVEs (compounds F-01 + F-08) | HIGH |
| F-02 | Unauthenticated operational information disclosure | MEDIUM |
| F-03 | No rate limiting or lockout on admin authentication endpoint | MEDIUM |
| F-05 | Entire business-facing stack degraded (developer API unreachable, admin 502, all business server actions 500) | MEDIUM |
| F-11 | Unauthenticated Server Action ID disclosure (CVE-2026-64643): IDs extracted from bundles and invoked pre-auth | MEDIUM |

## The critical chain

The deserializer demonstrably processes attacker-controlled Flight bytes **pre-auth**,
on a directly reachable plane with **no WAF**. Full weaponization chain was
reverse-engineered and reference-validated (Part 4 of the report).

## Controls verified sound

Bearer tokens cryptographically validated, with JWT signature and expiry enforced,
alg-confusion rejected. Admin login error uniformity (no user enumeration). Admin
middleware **not** bypassable via the 29927 vector.