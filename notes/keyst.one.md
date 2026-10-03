# Keystone Ecosystem: Security Assessment (September 2026)

**Source:** `keyst.one-security-assessment-2026.pdf` (11 pp) · Analyst: 0xRobotnick

## Scope

Four angles covered: public web property (`keyst.one`), commerce API surface
(`api.keyst.one`, `api-staging.keyst.one`, `shop.keyst.one`), vendor public SDK
repositories, and the open-source device firmware.

## Severity

1 systemic HIGH (CRITICAL-candidate) · 3 HIGH · 2 MEDIUM-HIGH · 4 MEDIUM · 4 LOW · 3 THEORETICAL

## Headline finding

The QR-signing acceptance channel between host software and the hardware wallet is
**unauthenticated end to end**. Requests, results, and pairing payloads cross the
camera boundary with no cryptographic binding and no mandatory session comparison
in any SDK layer. Four independently executed proof-of-concept runs demonstrated:

1. Acceptance of a substituted signing result belonging to a **different session**
   (LTC/BCH/DASH/DOGE flow: no binding check exists at all).
2. Acceptance of a signature response whose **binding field is omitted**
   (ETH/SOL/COSMOS/APTOS keyrings: check present but optional).
3. Client-side conversion of ordinary transactions into **hash-only "blind signing"**
   via a remotely-controlled configuration object.
4. Valid signatures produced over **silently truncated (empty)** message data.

## What held up

Device firmware is well engineered. Every transaction parser reviewed **fails closed**.
The firmware update chain enforces **dual signature verification**. The entropy path
correctly chains **three independent hardware RNG sources**.

## Why it matters

The vulnerability class lives entirely in the host-side SDK, between the device screen
and the network broadcast. That is precisely the layer implicated in the industry's
largest hardware-wallet losses.

## Reference material in this folder

Third-party and hardware documentation held alongside the assessment for context:

| File | Description |
|---|---|
| `repos/Keystone-developer-hub/audit-report/cobo_audit_report_2020_09_en_1_0.pdf` | Cobo audit of the developer hub (2020), 52 pp |
| `repos/Keystone-developer-hub/hardware/Keystone_V1.02_schematic.pdf` | V1.02 hardware schematic, 9 pp |
| `repos/keystone3-firmware/hardware/v3.1/*` | Keystone3 v3.1 main board schematic, port view, BOM |
| `repos/keystone3-firmware/hardware/v3.2/*` | Keystone3 v3.2 main board schematic, port view, BOM |
| `repos/keystone3-firmware/external/cryptoauthlib/cryptoauthlib-manual.pdf` | CryptoAuthLib reference manual (1,212 pp) |