# Perpsplexity.app — Findings & Impact Report

**Source:** `findings_report.pdf` (5 pp) · Analyst: 0xRobotnick

Non-technical-facing companion to the full technical audit (`report.md`) and the
evidence files under `audit/`, which carry the instruction-level detail and
reproductions.

## Scope

All protocol-owned **Sui Move** packages on the target network — main package
(pool / curve / launchpad / composite / engine / lending layers) plus integrated
**Aftermath** and **Suilend** components. Reviewed at **bytecode level**.

Status: review complete. **Two findings block launch.**

## What the platform is

A "conviction economy" token launcher over Aftermath perps and Suilend lending. Meme
tokens launch on bonding curves, buyers add quote, pools "graduate" to real
constant-product AMMs, and composite pools run perpetual-style engines with funding and
winddown machinery.

## Deploy-blocking findings

### F-7 — The graduation gate is dead code (Severity 7)

The check meant to stop a pool graduating before its funding target is met compares a
value that is **always large enough** — so it **can never fail**. A pool's meme supply
can be burned to zero by anyone, before the raise completes. Enables fund extraction or
destruction of any live, unpublished pool in a single transaction.

### A21 — Launch-created liquidity is never locked (Severity 7)

The "creation liquidity" token granted at launch is **never locked or burned**. Once the
curve is retired — which anyone can force, per F-7 — the holder of that token can
withdraw essentially the **entire pool balance**, including all quote paid in by buyers.

Buyers can lose nearly **100%** of their deposit with no off-chain recourse, and payouts
are freely transferable (untraceable in practice across fresh wallets).

## Further confirmed

| ID | Finding | Severity |
|---|---|---|
| A20 | Funding-loop availability defect — can freeze the epoch/reinvest machinery | 6 |
| A22 | Permissionless market-adoption gap — enables counterfeit markets for any token | 4 |

Every remaining finding is **Severity ≤ 2**.

## What was verified safe

The classic failure classes were each **specifically verified and not found**:

- Integer overflow
- Share-rounding inflation
- Capability forgery
- Reentrancy
- Dividend over-claim
- Over-withdrawal
- Authority confusion

The core financial machinery is engineered carefully. The defects live in the gating and
lifecycle logic above it.