# Nigerian Federal Government Portal Scans — November 2025

**Source:** 7 automated web-application scan reports (ZAP baseline + passive)
**Scan window:** 2025-11-11 → 2025-11-12 · Analyst: 0xRobotnick

Unauthenticated, non-destructive web application security scans across seven Nigerian
federal government portals. Automated passive analysis only — no exploitation, no
credential testing, no payload delivery against state systems.

## Results at a glance

| Target | Total | High | Medium | Low | Info |
|---|---:|---:|---:|---:|---:|
| `arc-p.gov.ng` | 1,108 | 0 | 263 | 639 | 206 |
| `advertcouncil.gov.ng` | 588 | **1** | 65 | 251 | 271 |
| `bictda.bo.gov.ng` | 386 | 0 | 118 | 220 | 48 |
| `cdcfib.gov.ng` | 106 | 0 | 14 | 72 | 20 |
| `account.surcon.gov.ng` | 47 | 0 | 6 | 4 | 37 |
| `data.energy.gov.ng` | 46 | 0 | 7 | 32 | 7 |
| `cct.gov.ng` | 12 | 0 | 6 | 4 | 2 |
| **Total** | **2,293** | **1** | **479** | **1,222** | **591** |

## The one High

`advertcouncil.gov.ng` — **Vulnerable JS Library** (1 instance High, 2 further Medium).
A third-party script version with a known vulnerability is served to the public.

## Cross-cutting systemic findings

The same classes repeat across almost every target. Individually low volume, collectively
the dominant pattern.

### Content Security Policy

| Class | Targets affected |
|---|---|
| CSP header not set | `advertcouncil`, `arc-p`, `cdcfib`, `data.energy` |
| `script-src 'unsafe-inline'` | all except `advertcouncil` |
| `style-src 'unsafe-inline'` | all except `advertcouncil` |
| Wildcard directive | all except `advertcouncil` |
| Directive with no fallback | all seven |

### Missing security headers

| Class | Targets affected | Highest volume |
|---|---|---|
| Strict-Transport-Security not set | 6 of 7 | `bictda` 96 · `arc-p` 272 · `cdcfib` 24 · `data.energy` 15 |
| X-Content-Type-Options missing | 6 of 7 | `bictda` 78 · `arc-p` 261 · `cdcfib` 22 · `advertcouncil` 251 · `data.energy` 14 |
| Missing anti-clickjacking header | 6 of 7 | `advertcouncil` 30 · `arc-p` 29 · `bictda` 20 |

### Transport and cookie hygiene

| Class | Targets affected |
|---|---|
| Mixed content on secure pages | `cct`, `arc-p`, `cdcfib`, `data.energy` |
| HTTPS → HTTP insecure transition in form post | `arc-p` 31 · `data.energy` 1 |
| Cookie without `HttpOnly` | `bictda`, `surcon`, `arc-p`, `cdcfib`, `data.energy` |
| Cookie without `Secure` | `bictda` 2 · `arc-p` 1 · `cdcfib` 2 · `data.energy` 1 |
| Cookie without `SameSite` | `surcon` 1 · `arc-p` 1 |

### Notable individual items

| Target | Finding |
|---|---|
| `arc-p` | **User Controllable HTML Element Attribute (Potential XSS)** — 14 instances. Application Error Disclosure (3) |
| `arc-p` | Absence of Anti-CSRF Tokens — 39 instances |
| `cdcfib` | Absence of Anti-CSRF Tokens — 6 instances |
| `cct` | **Sensitive information in URL** patterns, Unix timestamp disclosure |
| `bictda` | Cross-domain JavaScript source file inclusion — 43 instances |
| `advertcouncil` | Cross-domain script inclusion; heaviest informational surface (205 user-agent fuzzer responses) |
| `data.energy` | Private IP disclosure |

## Read

Volume here is dominated by header hygiene and mixed content, not by exploitable
application logic. The two items worth engineering attention:

1. **`advertcouncil.gov.ng` — vulnerable JS library** (the single High).
2. **`arc-p.gov.ng` — user-controllable HTML attribute, potential XSS** (14 instances),
   paired with 39 Anti-CSRF gaps and 31 HTTPS→HTTP form-post downgrades. Downgrading a
   form post to HTTP is what makes the CSRF exposure materially worse, and both appear on
   the same target.

## Artifacts

Raw scan reports are archived alongside this note as `.html`, one per target, with the
supporting report resource directories.