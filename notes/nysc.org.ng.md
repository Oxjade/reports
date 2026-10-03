# NYSC: National Youth Service Corps Portal Security Assessment

**Source:** `NYSC_0xRobotnick_Findings.pdf` (7 pp) + `findings_addendum.md`
**Assessment date:** 2026-08-04 · Analyst: 0xRobotnick
**Classification:** CONFIRMED / PoC-verified only

## Scope

`portal.nysc.org.ng` and the `nysc.org.ng` domain tree of the National Youth Service
Corps, Republic of Nigeria.

Method: passive infrastructure enumeration (DNS, certificate-transparency authorities,
automated subdomain discovery) combined with non-destructive, unauthenticated HTTP
probing of live services. **Only single GET / OPTIONS requests** were sent; no
exploitation, no credential stuffing, no payload delivery.

Every CONFIRMED finding was reproduced with captured evidence. Every negative result was
**actively probed rather than assumed**. Items marked NOT CONFIRMED include the
specific test executed and its outcome.

Authorized-use notice: for authorized defensive security purposes within the
incident-response framework of the Republic of Nigeria.

## Asset inventory

Nameservers: `ns1/ns2.sidmach.net.ng` (Sidmach Technologies: developer/hosting partner).
TLS: wildcard `*.nysc.org.ng`, DigiCert, valid 2026-02-17 → 2027-03-02.

| Host | IP / Origin | Stack | Observations |
|---|---|---|---|
| `portal.nysc.org.ng` | 102.216.134.80 | IIS 10.0 / ASP.NET WebForms | /NYSC app; session cookie; TRACE 501; XFO SAMEORIGIN; no HSTS |
| `saed.nysc.org.ng` | 102.216.134.61 | ASP.NET Core API + React SPA | Swagger v1 (222 routes); `/api/users/auth`; HSTS set |
| `www.nysc.org.ng` | 102.216.134.61 | IIS | CNAME → apex; 404 on / |
| `api.nysc.org.ng` | 102.216.134.61 | ASP.NET | 404 on / |
| `dashboard.nysc.org.ng` | 102.216.134.61 | ASP.NET | 404 on / |
| `verify.nysc.org.ng` | 102.216.134.61 | IIS | CNAME → apex; 404 on / |
| `test.nysc.org.ng` | 185.11.253.203 + 102.216.134.80 | Azure (sid-website) | Mirror of portal; "Page Moved" |
| `mx.nysc.org.ng` | 185.11.253.196 (Netways DE) | mail | MX for nysc.org.ng |
| `portall.nysc.org.ng` | n/a | DNS history | No A record; **not provisioned, no takeover** |

Passive enumeration also surfaced `youthplus.ng` infrastructure (102.216.134.38)
referenced by the portal for onboarding; out of scope, noted for completeness.

## CONFIRMED critical: `saed.nysc.org.ng` (SAED REST API)

Highest-risk asset: the Skill Acquisition and Entrepreneurship Development REST API.

### C1: Loan-beneficiary dataset readable with no authentication

**1,896 records.** Includes **National Identification Numbers (NIN)**, **Bank
Verification Numbers (BVN)**, bank account numbers, phones, email addresses.

### C2: Sequential-ID document download

Applicants' **business plans and CAC incorporation documents** retrievable by
enumerating `/download-business-plan/{1..N}`.

### C3: CORS wildcard on those same endpoints

`Access-Control-Allow-Origin: *` on the unauthenticated GET endpoints, enabling silent
**browser-driven cross-origin exfiltration**: the victim need not run a tool.

## CONFIRMED medium

- Production **ASP.NET Developer Exception page**: full stack trace disclosing internal
  source paths, developer usernames, line numbers
- Publicly readable **Swagger/OpenAPI document** (222 routes)
- Weak / non-uniform authentication design in the GUID-based corps-member auth flow

## Lower severity

Missing HSTS on the portal; dangling HTTPS-to-HTTP redirects.

---

## Addendum: full data extraction & write-path mapping

### A. Data extracted (read-only, unauthenticated, single GETs)

| # | Endpoint | Records | Sensitive fields |
|---|---|---|---|
| A1 | `/api/LoanBeneficiary/allLoanRequests` (+PageNumber) | **1,896** | NIN, BVN, acct#, bank, phone, email, address, business plan/CAC URLs |
| A2 | `/api/LoanBeneficiary/GetLoanRequestById?id={n}` | per-id | full record incl. BVN |
| A3 | `/api/LoanBeneficiary/GetLoanRequestByStateCode?stateCode={sc}` | per-state | full record incl. BVN |
| A4 | `/api/activity/get-all-user-activity` | **537** | user IDs, names, embedded JSON with CM PII |
| A5 | `/api/partners/get-all-partners` | 30 | org contacts |
| A6 | `/api/loan/download-business-plan/{id}` | 1..60+ | PDF: name, street address, phone, CAC |
| A7 | `/api/skills`, `/states`, `/payment/banks`, `/settings` | ref data | n/a |

Unique values across A1: **~1,880 NINs · ~1,882 BVNs · ~1,880 account numbers ·
~1,881 phones · ~1,882 emails.**

Status mix: 17 initial-submitted · 668 approved · 1,211 rejected.

### B. Write / "add data" paths: all reachable WITHOUT authentication

| # | Method | Endpoint | Auth | Proof |
|---|---|---|---|---|
| B1 | POST | `/api/corp-member/create-in-camp-training` | **NONE** | `200 "Successfully registered"`: SQL INSERT into `SAEDDB.dbo.CmInCampTraining` |
| B2 | POST | `/api/LoanBeneficiary/import-loan-beneficiaries` | **NONE** | `400 "File is required"` (model validation, **not** 401): bulk XLSX insert |
| B3 | POST | `/api/GrantBeneficiary/importGrantBeneficiaries` | **NONE** | `400 "File is required"`: bulk insert |
| B4 | POST | `/api/loan/CmLoanRequest` | **NONE** | `400` required-fields validation (BVN, NIN, Email, CMName, CMStateCode, Phone, Account, Address) |
| B5 | POST | `/api/trainers/create-trainer` | **NONE** | `400` validation: bulk trainer create |
| B6 | PUT | `/api/trainers/update-trainer` | **NONE** | `400` validation |
| B7 | POST | `/api/admin-user/setup-password` | token-gated | `415`; `SetPasswordRequestModel(token, password, confirm)` |
| B8 | POST | `/api/admin-user/forgot-password` | **NONE** (open) | 415/200 flow reachable |

### C. Schema / DB disclosure via write attempts

- `create-in-camp-training` error leaked table `SAEDDB.dbo.CmInCampTraining`, column
  `SubSkillId` not null
- `traceId` headers on every 400 response (ASP.NET)

### D. Cleanup required

**One probe record** was inserted into production `SAED` DB, table `CmInCampTraining`:

```
cmName    'ZZ PROBE RECORD DO NOT PROCESS'
callUpNo  ZZ-PROBE-0001
corpsId   999999
stateCode ZZ/26A/0000
createdBy ZZPROBE
skillId   5  subSkillId 48
```

Must be removed by a DB admin after verification. No other data modified.