# Security Assessment Report Archive

Consolidated archive of security assessment deliverables and accompanying analyst notes.

Every PDF report in this repository has a matching short-form note in [`notes/`](notes/)
summarizing scope, findings, severity, and what held up. The notes are the fastest way in;
the PDFs are the authoritative record.

- **Analyst:** 0xRobotnick
- **Date compiled:** 2026-10-02
- **Repository:** `Oxjade/reports` (private)

---

## Contents

```
reports/          Assessment deliverables (PDF). See Engagement Inventory.
notes/            Short-form analyst notes, one per report / report group
```

---

## Engagement Inventory

### Commercial and third-party assessments

| # | Target | Sector | Date | Findings | Highest severity | Report | Note |
|---|---|---|---|---|---|---|---|
| 1 | **RIBH Finance** | Fintech, Nigeria | Aug 2026 | 6 critical + 4 drain-class + webhook + squat | **CRITICAL** ×6 | `reports/ribhfinance.com/web/RIBH_Security_Assessment_2026-08-12.pdf` | [note](notes/ribhfinance.com.md) |
| 2 | **Slush** (Sui Wallet) | Consumer wallet | Aug 2026 | 2 critical confirmed, 2 high (1 theoretical), 2+ medium | **CRITICAL** ×2 | `reports/slush.app/Security-Assessment-Slush-Detailed-2026-08-24.pdf` | [note](notes/slush.app.md) |
| 3 | **Keystone** (ecosystem) | Hardware wallet + SDK + firmware | Sep 2026 | 1 systemic + 3 high + 2 med-high + 4 med + 4 low + 3 theoretical | **CRITICAL**-candidate | `reports/keyst.one/web/keyst.one-security-assessment-2026.pdf` | [note](notes/keyst.one.md) |
| 4 | **NectarFi** | Fintech, pan-African | Aug 2026 | 11 confirmed | **CRITICAL** ×1 (upstream) | `reports/nectarfi.finance/web/nectarfi_security_report.pdf` | [note](notes/nectarfi.finance.md) |
| 5 | **Zynta** | Stablecoin payment rails | Sep 2026 | 8 confirmed | **CRITICAL** ×1 | `reports/zynta.com/web/FINAL_REPORT_zynta_2026-09-01.pdf` | [note](notes/zynta.com.md) |
| 6 | **Perpsplexity.app** | Sui Move DeFi | 2026 | 2 launch-blocking + 2 confirmed | Severity 7 ×2 | `reports/perpsplexity.app/contracts/findings_report.pdf` | [note](notes/perpsplexity.app.md) |
| 7 | **Spenda** | Crypto-to-fiat, Africa | Aug 2026 | 1 critical destructive endpoint + exposure set | **CRITICAL** ×1 | `reports/spenda.africa/web/Spenda_Security_Assessment_2026-08-13.pdf` | [note](notes/spenda.africa.md) |

### Public-sector and government work

| # | Target | Sector | Date | Findings | Highest severity | Report | Note |
|---|---|---|---|---|---|---|---|
| 8 | **NYSC** (National Youth Service Corps) | Federal government, Nigeria | Aug 2026 | 3 critical data-exposure + 3 medium + lower | **CRITICAL** ×3 | `reports/public-sector/nysc.org.ng/NYSC_0xRobotnick_Findings.pdf` | [note](notes/nysc.org.ng.md) |
| 9 | **fctevreg.cam** (FRSC phishing campaign) | Threat investigation / forensics | Sep 2026 | Campaign analysis, infrastructure, cluster | n/a (forensic) | `reports/public-sector/fctevreg.cam_FRSC-phishing-forensic.pdf` | [note](notes/fctevreg.cam.md) |
| 10 | **7 Nigerian federal portals** | Government, Nigeria | Nov 2025 | 2,293 alerts across 7 targets | **HIGH** ×1 | `reports/public-sector/federal-gov-portals-2025/*.html` | [note](notes/federal-gov-portals-2025.md) |

### Third-party reference material

| File | Description | Note |
|---|---|---|
| `reports/cronos.com/repo/*/docs/audit/report_ethermint_1.2_final_public.pdf` | Public Ethermint v1.2 audit (2 identical copies) | [note](notes/cronos.com.md) |
| `reports/keyst.one/repos/Keystone-developer-hub/audit-report/cobo_audit_report_2020_09_en_1_0.pdf` | Cobo developer-hub audit (2020) | [note](notes/keyst.one.md) |
| `reports/keyst.one/repos/keystone3-firmware/hardware/v3.1/*`, `v3.2/*` | Keystone3 schematics, port views, BOMs | [note](notes/keyst.one.md) |
| `reports/keyst.one/repos/keystone3-firmware/external/cryptoauthlib/cryptoauthlib-manual.pdf` | CryptoAuthLib reference manual (1,212 pp) | [note](notes/keyst.one.md) |

---

## Coverage by assessment type

| Type | Engagements |
|---|---|
| **Web / API application assessment** | RIBH, Slush, Spenda, NectarFi, Zynta, Keystone (web + API), NYSC |
| **Blockchain / smart contract audit** | Perpsplexity.app (Sui Move, bytecode level), Cronos (Ethermint, reference) |
| **Hardware & firmware** | Keystone (Keystone3 firmware + schematics + SDK) |
| **Mobile application** | Slush (Chrome extension + Android APK), Mobile Wallet Adapter (work in progress) |
| **Threat investigation / forensics** | fctevreg.cam FRSC phishing campaign, cluster pivot |
| **Government portal assessment** | NYSC (deep, manual), 7 federal portals (automated baseline) |

## Coverage by attack surface

| Surface | Depth reached |
|---|---|
| **Authentication / session management** | Deep: token validation, alg-confusion, social-login JWT verification, GUID-based flows, OAuth origin binding |
| **Authorization / access control** | Deep: IDOR, cross-user data access, capability models (Sui), admin middleware bypass vectors |
| **Input validation & injection** | Deep: SQLi (via third-party BI), server-action replay, webhook forgery, parameter fuzzing, differential testing |
| **API surface enumeration** | Deep: OpenAPI/Swagger extraction (222 routes at NYSC SAED), route discovery, method matrices |
| **Known-vulnerability management** | Deep: version fingerprinting against advisory ranges, live verification of affected builds |
| **Data exposure** | Deep: unauthenticated dataset extraction, sequential-ID document retrieval, secret discovery in shipped bundles |
| **Third-party supply chain** | Present: embedded admin secrets in JS bundles, vulnerable JS libraries, dependency CVE sweeps |
| **Transport & header hardening** | Systematic across all engagements |
| **Infrastructure enumeration** | Systematic: DNS, certificate transparency, subdomain discovery, IP pivot and cluster mapping |
| **Mobile binary analysis** | Present: shipped-client secret extraction, manifest deep-link enumeration, backup rules |

## Coverage by severity discipline

Findings carry severity **only** where a proof-of-concept reproduced the condition.
Items that could not be verified are recorded as **THEORETICAL** with no severity, and
the blocking condition is documented: for example the Slush H-02 WebView bridge finding,
blocked by an incomplete App-Bundle base split.

Negative results are recorded as actively tested outcomes, not assumptions. Each report
carries an explicit "verified sound" or "controls verified" section.

## What held up

Recorded across engagements, worth stating plainly:

- **Keystone device firmware**: every transaction parser reviewed fails closed; firmware
  update chain enforces dual signature verification; entropy path chains three
  independent hardware RNG sources.
- **Perpsplexity core financial machinery**: integer overflow, share-rounding inflation,
  capability forgery, reentrancy, dividend over-claim, over-withdrawal, and authority
  confusion were each specifically verified and not found.
- **NectarFi authentication**: bearer tokens cryptographically validated, signature and
  expiry enforced, alg-confusion rejected, no user enumeration on admin login.
- **Slush API backend**: persisted-query-only GraphQL, Vercel-protected staging,
  client-only feature-flag key.
- **Zynta custody layer**: credential-based compromise not achievable externally; every
  entry point auth-gated. Several webhooks correctly HMAC-protected.
- **NYSC negative results**: `portall.nysc.org.ng` has no A record and was not
  provisioned, so no takeover path.
- **Spenda**: controlled functional verification on development and staging
  environments confirmed the write paths that were reachable there.

---

## Methodology

Engagements combine:

- Passive infrastructure enumeration: DNS record sets, certificate-transparency
  authorities, automated subdomain discovery
- Non-destructive, unauthenticated HTTP probing
- Static analysis of shipped client bundles, including secret extraction
- API schema extraction and route enumeration
- Known-CVE component fingerprinting against advisory ranges, verified live where
  reachable
- Manual review of authentication, session handling, and authorization boundaries
- Blockchain bytecode-level review for smart contract targets
- Shipped-binary analysis for mobile targets: manifests, backup rules, embedded secrets

Testing used fabricated identities, non-existent identifiers, zero-value or
read-only operations, and controlled audit accounts where available. Where a finding
required a destructive action to confirm, the destructive leg was not executed and the
finding is recorded with the confirmation step left for the client to run.

## Ethics and handling notes

- No denial-of-service techniques were used against any target.
- No real funds were moved. No real customer data was exported.
- No third-party callback infrastructure was used to exfiltrate findings; output was
  surfaced only into the assessment session's own HTTP responses.
- Credentials were only ever tested where the client supplied them, or against
  controlled audit accounts and non-existent identifiers.
- One engagement required inserting an inert, clearly-labelled probe record into a
  production database to confirm that a write path was unauthenticated. It is documented
  in that report with the exact record values and marked for DB-admin cleanup.

## Disclaimer

Provided for internal review and remediation tracking. Distribution is restricted to
parties covered by the originating engagement agreement. Reports marked *Confidential* or
*Private / client distribution only* retain their original classification and must not be
redistributed further.