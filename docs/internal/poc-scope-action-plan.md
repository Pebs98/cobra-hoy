# Cobra HOY — POC Scope & Action Plan

**Project:** Cobra HOY (SEOGA x Laminar)
**Document type:** Internal — POC Execution Plan
**Author:** Laminar
**Date:** 2026-02-26
**Status:** Draft v0.1
**Parent document:** *Cobra HOY — Architecture & Components Overview* (master)

---

## 1. POC Objective

Prove that Cobra HOY works with real capital across 5–10 pilot hospitals. Specifically:

1. **Model validation** — SEOGA's credit scoring predictions match actual insurer payment behavior with sufficient accuracy to support lending decisions
2. **Structural validation** — The Double Lockbox (pledged account + tripartite control agreement + automated sweep) operates as designed
3. **Adoption signal** — Hospitals are willing to onboard and use the product

---

## 2. POC Scope

| Dimension | MVP Scope |
|-----------|-----------|
| Hospitals | 5–10 centers, all insurers SEOGA covers |
| Automation | Full: proactive offers + on-demand, automated disbursement and settlement via Double Lockbox |
| Structuring | Simple pool, no tranching (avoids EU Securitization Reg) |
| Capital | SPV funded by single investor or small syndicate (Spanish S.L. if single; Luxembourg SCSp when scaling) |
| Blockchain | None — architecture designed for future migration (event sourcing, immutable logs) |

**Explicitly out of scope for POC:**
- Tranching / multi-pool structuring
- Blockchain / tokenization
- Physician advance (master, Section 11.1)
- Secondary market
- Multi-investor SPV (unless needed from day one)
- CNMV registration

---

## 3. Timeline Overview

**Estimated: ~5–6 months from NDA to pilot ramp.**

```
Month 0          Month 1          Month 2          Month 3          Month 4        Month 5–6
───────────────────────────────────────────────────────────────────────────────────────────────

NDA signed       Data room        Integration      Platform MVP     1st hospital   Pilot ramp
                 access           model decided    complete         LIVE           (5–10 hospitals)

Legal counsel    Convenio         SPV formed       Bank API live    1st advances   Model accuracy
engaged          research done                                     disbursed      tracking begins

                 Bank partner     Legal templates   AML/KYC
                 shortlisted      drafted           complete
```

---

## 4. Workstreams & Dependencies

Five parallel workstreams, all gated by the NDA:

```
NDA (Month 0) ──────────────────────────────────────────────────────────────────►
    │
    ├──► LEGAL / STRUCTURAL
    │    ├── Legal counsel engagement (M0)
    │    ├── B2B lending opinion (M0–1)                ─── gates SPV formation
    │    ├── Convenio research via SEOGA (M0–1)        ─── gates pledge template design
    │    ├── SPV domicile decision (M1)                ─── blocked by investor profile
    │    ├── SPV formation (M1–2)                      ─── gates AML/KYC setup
    │    ├── Umbrella Pledge + Control Agreement
    │    │   template drafting (M1–2)                  ─── gates hospital onboarding
    │    └── RDL 5/2005 + clawback opinions (M1–2)
    │
    ├──► BANKING
    │    ├── Bank partner selection (M0–1)              ─── critical path
    │    └── Bank onboarding + API integration (M2–3)  ─── gates hospital onboarding
    │
    ├──► DATA / TECHNOLOGY
    │    ├── Data room access + schema review (M0–1)   ─── gates platform build
    │    ├── Integration model decision (M1)
    │    ├── Platform build (M1–3):
    │    │   ├── Data ingestion layer
    │    │   ├── Lending ledger + advance engine
    │    │   ├── Pledged account monitoring
    │    │   ├── Disbursement & settlement
    │    │   └── Investor dashboard (basic)
    │    └── Haircut methodology validation (M2–3)     ─── needs data room
    │
    ├──► REGULATORY / COMPLIANCE
    │    ├── GDPR DPIA (M0–2, joint)                   ─── gates data flows
    │    └── AML/KYC setup (M2–3, after SPV formed)    ─── gates hospital onboarding
    │
    └──► CAPITAL
         ├── Investor targeting + pitch (M0–2)
         ├── Compensation framework (M1–2)
         ├── Term sheet + commitment (M2–3)             ─── gates SPV funding
         └── SPV funded (M3)                            ─── gates first advance
```

---

## 5. Convergence Gate

Three items must converge before the first hospital can onboard:

1. **Platform MVP** complete (advance engine, ledger, monitoring, disbursement)
2. **Bank partner** onboarded with API integration live
3. **Legal templates** finalized (Umbrella Pledge, Control Agreement, lending agreement)

If any slips, the others wait.

**Highest-risk item:** Bank partner selection (Q9 in master, Section 13). Requires a willing Spanish commercial bank with API capabilities and appetite for tripartite control agreements. Start immediately after NDA.

---

## 6. Success Metrics

| Metric | What it measures | Target |
|--------|-----------------|--------|
| **Hospital activation** | Hospitals offered Cobra HOY that activate | Track (no target — pilot too small) |
| **Advance take-up** | % of eligible claims advanced by active hospitals | Track (no target — pilot too small) |
| **Amount accuracy** | SEOGA predicted settlement amount vs. actual | TBD — baseline from pilot data |
| **Timing accuracy** | SEOGA predicted settlement date vs. actual | TBD — baseline from pilot data |
| **Haircut adequacy** | % of advances where actual settlement ≥ advance amount | >99% |
| **Sweep execution** | Daily sweeps executed on time vs. scheduled | >99.5% |
| **Onboarding time** | Calendar days from hospital interest to first advance | <10 business days |

The first two metrics are adoption signals — the pilot establishes baselines, not targets. Amount and timing accuracy are the core POC deliverables that prove SEOGA's model is lendable. Haircut adequacy and sweep execution are operational proof points for investors.

---

## 7. Action Items

> Q-references (e.g., Q9) map to open questions in the master document, Section 13.

### 7.1 Pre-NDA (can start now)

| # | Action | Owner | Ref |
|---|--------|-------|-----|
| A1 | Identify and engage fintech-focused Spanish law firm (Finreg 360, Garrigues, Uría, or equivalent) | Laminar | Q10 |
| A2 | Define target investor profile — US private credit, EU credit fund, family office, institutional? | Laminar | Q7b |
| A3 | Begin bank partner research — shortlist Spanish commercial banks with API banking + BaaS appetite | Laminar | Q9 |
| A4 | Initiate compensation framework discussion with SEOGA | Both | Q12 |
| A5 | Prepare NDA for execution | Both | Q2 |

### 7.2 Post-NDA: Month 0–1

| # | Action | Owner | Ref | Dependency |
|---|--------|-------|-----|------------|
| B1 | Execute NDA | Both | Q2 | — |
| B2 | Request data room access — formats, granularity, available history | SEOGA | Q3 | B1 |
| B3 | Assess SEOGA tech team — size, stack, bandwidth for collaboration | SEOGA | Q5 | B1 |
| B4 | Agree integration model — API vs. batch vs. direct access | Both | Q4 | B2 |
| B5 | Commission B2B lending legal opinion | Laminar (legal) | Q6 | A1 |
| B6 | Begin convenio research — do any convenios restrict pledging? | SEOGA + Laminar (legal) | Q8 | B1 |
| B7 | Commission GDPR DPIA (joint) | Laminar + SEOGA | — | B1 |
| B8 | Finalize bank partner shortlist, begin outreach | Laminar | Q9 | A3 |
| B9 | Commission RDL 5/2005 financial collateral opinion | Laminar (legal) | Q15 | A1 |
| B10 | Commission insolvency clawback safe harbor opinion | Laminar (legal) | Q16 | A1 |

### 7.3 Post-NDA: Month 1–2

| # | Action | Owner | Ref | Dependency |
|---|--------|-------|-----|------------|
| C1 | Decide SPV domicile (S.L. vs. SCSp) | Laminar | Q7 | A2, B5 |
| C2 | Form SPV | Laminar | — | C1 |
| C3 | Draft Umbrella Pledge template | Laminar (legal) | Q10 | B5, B6 |
| C4 | Draft Tripartite Control Agreement template | Laminar (legal) | Q10 | B8 |
| C5 | Draft lending agreement template | Laminar (legal) | — | B5 |
| C6 | Begin platform build — data ingestion, ledger, advance engine | Laminar | — | B2, B4 |
| C7 | Begin haircut methodology validation with SEOGA data | SEOGA + Laminar | Q11 | B2 |
| C8 | Select bank partner | Laminar | Q9 | B8 |

### 7.4 Post-NDA: Month 2–3

| # | Action | Owner | Ref | Dependency |
|---|--------|-------|-----|------------|
| D1 | Bank onboarding + API integration | Laminar + Bank | — | C8 |
| D2 | AML/KYC setup (SPV as obliged entity) | SPV | — | C2 |
| D3 | Complete platform build — monitoring, disbursement, dashboard | Laminar | — | C6 |
| D4 | Complete GDPR DPIA | Laminar + SEOGA | — | B7 |
| D5 | Investor term sheet + capital commitment | Laminar | — | A2, C2 |
| D6 | Fund SPV | Laminar | — | D5 |

### 7.5 Post-NDA: Month 3–4

| # | Action | Owner | Ref | Dependency |
|---|--------|-------|-----|------------|
| E1 | Onboard first hospital (KYB, pledged account, notary session, activation) | All | — | D1, D2, D3, C3, C4, C5 |
| E2 | First advance disbursed | — | — | E1, D6 |
| E3 | First sweep executed | — | — | E2 |

### 7.6 Post-NDA: Month 4–6

| # | Action | Owner | Ref | Dependency |
|---|--------|-------|-----|------------|
| F1 | Ramp to 5–10 pilot hospitals | All | — | E1 |
| F2 | Model accuracy tracking begins (amount, timing, haircut adequacy) | Laminar + SEOGA | — | E3 |
| F3 | First investor report produced | Laminar | — | F2 |

---

## 8. Cost Summary

From master document, Section 8.3:

| Category | Cost | Timeline |
|----------|------|----------|
| SPV formation | €15–25K | 1–2 months |
| AML/KYC setup | €10–30K | 1–2 months |
| GDPR DPIA + DPO + legal opinions | €15–40K | 2–3 months |
| Bank partner onboarding + API integration | €5–15K setup | 1–2 months |
| Legal drafting (templates) | €10–25K | 1–2 months |
| Legal counsel (structure, contracts) | €20–50K | 2–3 months |
| **Total** | **€75–185K** | **2–3 months critical path** |

> Platform build costs are separate and depend on Laminar's internal allocation model.

---

*This document is a companion to the master architecture document. It will be updated as NDA is signed and execution begins.*
