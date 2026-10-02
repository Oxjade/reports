# Security Assessment Reports

Consolidated archive of security assessment deliverables, organized by engagement target.

Each top-level directory corresponds to one engagement. Files preserve the original
assessment output, including executive summary and detailed variants where both exist.

## Contents

| Target | Reports | Notes |
|---|---|---|
| `cronos.com` | 2 | Third-party Ethermint audit (Coinbase) — reference material |
| `fctevreg.cam` | 1 | Forensic report |
| `keyst.one` | 10 | Web/app assessment plus hardware firmware schematics and reference audits |
| `nectarfi.finance` | 1 | Security report |
| `perpsplexity.app` | 1 | Findings report |
| `ribhfinance.com` | 1 | Security assessment |
| `slush.app` | 2 | Executive + detailed assessment |
| `spenda.africa` | 2 | v1 and v2 assessments |
| `zynta.com` | 1 | Final report |

## Methodology

Engagements combine passive infrastructure enumeration (DNS records, certificate
transparency, automated subdomain discovery) with non-destructive, unauthenticated
application probing, static and dynamic analysis of submitted source code, and manual
review of authentication, session handling, and authorization boundaries.

All testing was performed against assets with explicit engagement authorization.
No denial-of-service, data destruction, or persistence techniques were employed.

## Severity

Findings use CRITICAL / HIGH / MEDIUM / LOW / INFORMATIONAL. Severity reflects
demonstrated exploitability and business impact under the stated preconditions.

## Disclaimer

Provided for internal review and remediation tracking. Distribution is restricted to
parties covered by the originating engagement agreement.