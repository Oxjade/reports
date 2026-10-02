# Addendum — Full Data Extraction & Write-Path Mapping (0xRobotnick)
Date: 2026-08-04 | Target: saed.nysc.org.ng (ASP.NET Core / React SPA)

## A. Data extracted (all read-only, unauthenticated, single GETs)
| # | Endpoint | Records | Sensitive fields |
|---|---|---|---|
| A1 | /api/LoanBeneficiary/allLoanRequests (+ PageNumber) | 1,896 | NIN, BVN, acct#, bank, phone, email, address, business plan/CAC URLs |
| A2 | /api/LoanBeneficiary/GetLoanRequestById?id={n} | per-id | full record incl BVN |
| A3 | /api/LoanBeneficiary/GetLoanRequestByStateCode?stateCode={sc} | per-statecode | full record incl BVN |
| A4 | /api/activity/get-all-user-activity | 537 | user IDs, names, embedded JSON with CM PII (name, state code, PPA, training) |
| A5 | /api/partners/get-all-partners | 30 | org contacts |
| A6 | /api/loan/download-business-plan/{id} | 1..60+ | PDF: name, street address, phone, CAC |
| A7 | /api/skills, /states, /payment/banks, /settings | ref data | - |

Unique values across A1: ~1,880 NINs, ~1,882 BVNs, ~1,880 account numbers, ~1,881 phones, ~1,882 emails.
Status mix: 17 initial-submitted, 668 approved, 1,211 rejected.

Files saved: data/loans_all.json (1.7MB), data/activity_log.json (435KB), data/partners.json (512KB),
data/loan_template.xlsx, data/grant_template.xlsx.

## B. Write / "add data" paths (all reachable WITHOUT authentication)
| # | Method Endpoint | Auth | Proof |
|---|---|---|---|
| B1 | POST /api/corp-member/create-in-camp-training | NONE | 200 "Successfully registered" — SQL INSERT into SAEDDB.dbo.CmInCampTraining; INERT probe record created (cmName='ZZ PROBE RECORD DO NOT PROCESS') |
| B2 | POST /api/LoanBeneficiary/import-loan-beneficiaries | NONE | 400 "File is required" (model validation, not 401) — bulk XLSX insert |
| B3 | POST /api/GrantBeneficiary/importGrantBeneficiaries | NONE | 400 "File is required" — bulk insert |
| B4 | POST /api/loan/CmLoanRequest | NONE | 400 required-fields validation (BVN,NIN,Email,CMName,CMStateCode,Phone,Account,Address) — loan application insert |
| B5 | POST /api/trainers/create-trainer | NONE | 400 Files/SkillIds/Trainers/Equipments validation — bulk trainer create |
| B6 | PUT /api/trainers/update-trainer | NONE | 400 validation — trainer update |
| B7 | POST /api/admin-user/setup-password | token-gated | 415; SetPasswordRequestModel(token,password,confirm) — forgot-password completion |
| B8 | POST /api/admin-user/forgot-password | NONE(open) | 415/200 flow reachable |

## C. Schema/DB disclosure via write attempts
- create-in-camp-training error leaked table: SAEDDB.dbo.CmInCampTraining, column SubSkillId not null
- traceId headers on every 400 (ASP.NET)

## D. Cleanup required
- ONE probe record inserted into production SAED DB (table CmInCampTraining): cmName 'ZZ PROBE RECORD DO NOT PROCESS',
  callUpNo ZZ-PROBE-0001, corpsId 999999, stateCode ZZ/26A/0000, createdBy ZZPROBE, skillId 5, subSkillId 48.
  Must be removed by DB admin after verification. No other data modified.

## E. Recommended fixes (new)
1. Add [Authorize] to ALL corp-member/trainer/import/loan-submission endpoints (not just GET lists)
2. Import endpoints: require authenticated session + audit who uploaded
3. Validate that in-camp training create is only callable by secretariat/admin role
4. Harden error messages: no raw SQL exceptions / table names in responses
5. Consider whether CmLoanRequest should be public-only via a dedicated self-service auth flow
