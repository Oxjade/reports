# fctevreg.cam — Forensic Report: "FRSC Traffic Fine" Phishing Campaign

**Source:** `forensic_report.pdf` (6 pp) · Case ID: FRSC-2026-0918-01
**Report date:** 2026-09-18 · Classification: UNCLASSIFIED // TLP:AMBER · Analyst: 0xRobotnick

## Subjects

`fctevreg.cam` · `frsac-govt.cc` · `frsac-gov.cam` · `fctevregonline.click` + campaign family

## Method

Passive OSINT, live-abuse observation, capture analysis. **No unauthorized access. No
victim data harvested.**

## Executive summary

`fctevreg.cam` ran a **Nigerian traffic-fine payment phishing portal impersonating the
Federal Road Safety Corps (FRSC)**.

Flow: a victim entering any vehicle-plate number was shown a fabricated **N10,000 fine**
with a 50% "pay now" discount, then funnelled through a card-payment screen that
harvests full payment-card details and **live OTPs**.

## Platform

The site is a tenant of a Chinese **capability-as-a-service (AiTM)** phishing platform,
branded internally **"Kylin"**, using theme `theme-frsc-ng` v1.0.4.

- Data transfer is **WebSocket-driven** (`wss://<origin>/ws`)
- Token-gated by a signed `__Host-KYLIN_visitor_cap` JWT issued on page load
- A **human operator** relays victim card and OTP data in real time through an admin
  channel of the same protocol

## Infrastructure

| Node | IP | Hosting |
|---|---|---|
| `www.fctevreg.cam` / `/` | 43.165.173.182 | Tencent Cloud, Tokyo, AS132203 (2026-09-17) |
| `frsac-govt.cc`, `frsac-gov.cam` | 43.165.191.226 | Tencent Cloud, Tokyo, AS132203 |
| `fctevregonline.click` | — | Cloudflare-fronted |

## Cluster

IP-pivot of 43.165.173.182 exposes **19 additional phishing domains** — DHL, MTN,
courier, finance-branded — scanned on that node between 2026-08-08 and 2026-09-17.

**The FRSC operation is one brand of a rotating, shared crime-as-a-service platform.**

## Assessment

The capability-collection surface was verified live — collector responds 401/400 without
a valid token.

**Takedown does not require chaining any vulnerability.** It requires the cloud/registrar
abuse process.