# Slush (Sui Wallet): Security Assessment (24 Aug 2026)

**Source:** `Security-Assessment-Slush-2026-08-24.pdf` (5 pp, executive) and
`Security-Assessment-Slush-Detailed-2026-08-24.pdf` (17 pp, full) · Analyst: 0xRobotnick

Two CRITICAL findings reproduced **live against production**.

## Scope

`slush.app` web properties · Chrome extension v26.18.0 · Android app v2
(26.18.0 / versionCode 122, `com.mystenlabs.suiwallet`) · dependent services:
Enoki identity/OAuth, Statsig feature-flag gateway, Stripe on-ramp, OAuth providers,
relay/sync API.

## Findings

| ID | Finding | Severity | Status |
|---|---|---|---|
| C-01 | Production **Enoki API key embedded in shipped clients** (extension + Android), unrotated across versions | CRITICAL | CONFIRMED: live HTTP 200 |
| C-02 | Enoki OAuth app configured with `allowedOrigins: []`: **any** website origin may use the wallet's OAuth/zkLogin identity flow | CRITICAL | CONFIRMED: live config |
| H-01 | Android backup / data-extraction rules leave MMKV / AsyncStorage / Realm exposed | HIGH | CONFIRMED (static config) |
| H-02 | Mobile dApp WebView bridge lacks origin allow-list (approval origin-binding unverified) | HIGH | **THEORETICAL: blocked** |
| M-01 | Stripe on-ramp exports 5 deep-link activities; `stripe-connect://` has no host restriction | MEDIUM | CONFIRMED (manifest) |
| M-02 | AppsFlyer + expanded deep-link schemes/hosts (OneLink, `sui://`, `zksend`, etc.) | MEDIUM | CONFIRMED |

## Key detail

A benign API call with the leaked Enoki key returned **HTTP 200** and disclosed the
wallet's full OAuth configuration. C-02 is an identity-phishing enabler: any origin can
drive the wallet's identity flow.

## Documented blocker

H-02 dynamic confirmation was blocked because the supplied APK was an **incomplete
App-Bundle base split** with no native libraries. Recorded as THEORETICAL, with no severity
assigned beyond the static HIGH.

## What held up

Live API backend is otherwise well hardened, with persisted-query-only GraphQL,
Vercel-protected staging, client-only Statsig key.

The detailed report includes CVE correlation, a remediation roadmap, full PoC
transcripts, and an evidence inventory.