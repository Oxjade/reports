# Spenda — v1 Assessment (13 Aug 2026)

**Source:** `Spenda_Security_Assessment_2026-08-13.pdf` (21 pp) · Analyst: 0xRobotnick

## Scope

`spenda.africa` · `spendaafrica.com` · AWS estate (CloudFront, Route53, ALBs, EC2
origins in ap-south-1 and eu-west-3, S3, EKS).

25+ discovered subdomains spanning production, development, staging, and operator tooling.

Evidence window 2026-08-10 → 2026-08-13 (UTC). Every finding PoC-validated;
unverifiable hypotheses noted inline **without severity**.

## Headline

**One CRITICAL unauthenticated destructive endpoint on a public staging environment.**

Also confirmed:

- Extensive exposure of development / staging API documentation
- **Embedded administrative secrets in a publicly served JavaScript bundle**
- CORS misconfiguration on the **production** admin API
- Absent rate limiting on authentication endpoints
- Systemic hardening gaps — missing security headers, HTTPS downgrade, direct origin
  reachability

## Method

Passive infrastructure enumeration (DNS, certificate-transparency authorities,
automated subdomain discovery) + non-destructive unauthenticated HTTP probing,
JavaScript bundle analysis, API schema review, and controlled functional verification
against development/staging environments.

---

# Spenda v2 — Assessment (9 Sep 2026)

**Source:** `Spenda-v2-Assessment-2026-09-09.pdf` (7 pp) · Analyst: 0xRobotnick

## Scope

`service-api.spenda.africa` · `genesis-ark.spenda.africa` · `sbi.spenda.africa` (BI)

Metabase findings lead the report at the client's request — they carry Critical-rated,
actively-exploited CVE exposure.

## Findings, highest severity first

| Severity | Finding |
|---|---|
| **CRITICAL** | Metabase BI server (`sbi.spenda.africa`) — **GHSA-r495-55cx-fjh7** confirmed affected, with a **live-confirmed bypass** |
| **CRITICAL** | Cross-environment JWT signing-key sharing extends across environments |
| HIGH | Staging backend reachable through the production plane |
| HIGH | Internal topology disclosed unauthenticated (pod IPs, service map) |
| MEDIUM | Reproducible 500 crash on form-encoded authentication |
| MEDIUM | Debug endpoints exposed in production |
| MEDIUM | Admin portal acts as a **WAF-bypass proxy** |
| MEDIUM | Client-controlled environment switching in the admin portal |
| LOW | Assorted hardening items |

## GHSA-r495-55cx-fjh7 detail

Metabase v0.53.7.1 (OSS, build 2025-03-18) — six major versions behind current. A 2026
advisory series of **Critical** advisories; this build sits inside the affected range of
one of them and was verified live.

Published 2026-08-11. Affected range includes a standalone sweep line `< x.58.28` that
sweeps in v0.53.7.1.

The advisory bundles eight items, all unpatched on the target:

1. Query-input SQL injection
2. HoneySQL injection
3. Nested-card permission bypass
4. Login / password-reset flaws
5. Sandboxing / impersonation escapes
6. Loose API request validation
7. Network exposure — a legacy "HTTP action" path let the server issue requests to
   internal addresses (**SSRF**)
8. Row / download limit bypass

## Escaped only by version arithmetic

Some Critical CVEs are escaped only by version. Documented explicitly rather than
claimed as fixed: the endpoint exists, but the token parameter is strictly
schema-validated and the lookup is parameterized.

## Verified resilience

Tested and held — see §7 of the report for the attack-class result matrix.

## Unauthorized access achieved

Recorded in §8 — access achieved **with no credentials used**.