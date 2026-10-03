# Spenda: v1 Assessment (13 Aug 2026)

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
- Systemic hardening gaps: missing security headers, HTTPS downgrade, direct origin
  reachability

## Method

Passive infrastructure enumeration (DNS, certificate-transparency authorities,
automated subdomain discovery) + non-destructive unauthenticated HTTP probing,
JavaScript bundle analysis, API schema review, and controlled functional verification
against development/staging environments.
