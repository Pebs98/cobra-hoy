# Cobra HOY — Executive Summary

**Laminar | Confidential | February 2026**

---

## Opportunity

Cobra HOY is a secured lending facility that provides early payment to Spanish private healthcare centers against their expected insurance company payouts.

Spanish private hospitals perform medical procedures for insured patients under framework agreements (*convenios*) with insurers. Insurers owe money — but pay in batch cycles 15 to 60 days later. Hospitals need working capital today. No financial product exists to bridge this gap at the individual claim level.

Cobra HOY fills that gap using proprietary data from SEOGA, the dominant claims processing platform in Spain's private healthcare sector.

---

## Market

SEOGA processes **~€900M+ per year** in healthcare insurer payment flows across **1,000+ healthcare centers** and **60+ insurers**. Their platform handles the full claims lifecycle — submission, authorization, reconciliation, and settlement tracking — giving them claim-level visibility into what is owed, by whom, and when it will pay.

This data has never been used for lending. Until now, it has served purely administrative functions. Cobra HOY is the first product to monetize SEOGA's data advantage as a financial asset.

---

## Why No Competition Exists

This is not invoice factoring. Convenio-based claims are not invoices — they are conditional payment obligations under bilateral framework agreements. Traditional factoring requires discrete, assignable invoices and does not apply.

Bank credit lines are generic. They lend against the hospital's balance sheet, not against specific insurer payment flows. They are slow, rigid, consume the hospital's borrowing capacity (CIRBE), and lack domain-specific pricing intelligence.

**The only entity that can build this product is one with claim-level data mapping individual medical acts to insurer settlement behavior.** SEOGA is that entity. Replicating this data position would require processing millions of claims across 60+ insurers and 1,000+ hospitals — a multi-year, relationship-intensive effort that no competitor has undertaken.

---

## Structure

### How Capital Is Deployed

Investors participate through a single **Special Purpose Vehicle (SPV)** — the sole lender of record, sole pledgee, and sole control agreement counterparty. Investors come and go behind the SPV without disrupting any hospital relationship.

The SPV advances cash to hospitals. When the insurer pays, funds flow into a **pledged bank account in the hospital's name** — the insurer sees no change to its payment process. A tripartite control agreement (Hospital + SPV + Bank) governs daily automated sweeps: advance repayment flows to the SPV first, surplus returns to the hospital's operating account.

This "Double Lockbox" structure avoids triggering anti-assignment clauses in convenios, eliminates the need for insurer consent or notification, and provides the SPV with a bankruptcy-enforceable security interest via notarized pledge over credit rights (*prenda sobre derechos de crédito*).

### Key Structural Features

| Feature | Detail |
|---------|--------|
| **Security interest** | Notarized Umbrella Pledge covering all present and future receivables — one-time per hospital, scalable via telematic notary |
| **Cash flow control** | Tripartite control agreement with partner bank; automated daily sweeps via API |
| **No insurer disruption** | Pledged account is in the hospital's name; insurer payment process is completely unchanged |
| **No financial license required** | B2B lending in Spain is unregulated; bank partner handles all payment flows |
| **No securitization trigger** | Single pool, no tranching at MVP — avoids EU Securitization Regulation |

---

## Two Revenue Streams, One Capital Pool

### Stream 1: Hospital Advance

Hospitals pay **~0.65% per advance** (7.9% annualized on a 30-day cycle) to receive cash immediately rather than waiting 15–60 days. The fee is competitive because no alternative product exists, there is no CIRBE impact, and the advance is accessible in one click inside the platform hospitals already use daily.

### Stream 2: Physician Advance

SEOGA already calculates what each physician should be paid from each insurer settlement. Doctors wait even longer than hospitals — 45 to 120 days total. Their willingness to pay is higher.

Physicians pay **~1.25% per advance** (15.2% annualized). The structure avoids consumer credit regulation: the SPV lends to the hospital (B2B), the hospital pays the doctor early, and the fee is deducted from the doctor's honorarium. The SPV's counterparty is always the hospital, never the individual.

For hospitals, early physician payment becomes a **recruitment and retention tool** for specialists — the hospital doesn't pay the fee; the doctor does.

---

## Return Profile

### Base Case

| Parameter | Value |
|-----------|-------|
| Average advance duration | 30 days |
| Hospital fee | 0.65% per advance |
| Physician fee | 1.25% per advance |
| Physician volume (% of hospital flow) | 40% |
| Capital utilization | 88% |

| | Value |
|---|---|
| Blended gross yield | 10.8% |
| After utilization | 9.5% |
| Less costs, losses, and platform fees | (2.3%) |
| **Net to investor (unlevered)** | **7.25%** |

### With Leverage

| | Unlevered | 1.5x | 2x |
|---|---|---|---|
| Equity | €3M | €3M | €3M |
| **Return on equity** | **7.25%** | **9.0%** | **10.8%** |

### Sensitivity (Net to Investor, Unlevered)

| | Phys. 1.00% | Phys. 1.25% | Phys. 1.50% |
|---|---|---|---|
| **Hosp. 0.50%** | 5.4% | 6.2% | 6.9% |
| **Hosp. 0.65%** | 6.3% | 7.25% | 8.0% |
| **Hosp. 0.80%** | 7.2% | 8.1% | 8.9% |

Capital recycles every 15–60 days. A **€3M facility can deploy €20M+ annualized**.

---

## Comparable Positioning

| Product | Net Return | Duration | Obligor Quality |
|---------|-----------|----------|----------------|
| EUR money market | 2.5–3% | <90d | Sovereign/bank |
| Supply chain finance fund | 4–8% | 30–90d | Corporates |
| **Cobra HOY (unlevered)** | **7–8%** | **15–60d** | **Spanish insurers** |
| Trade finance fund | 9–12% | 30–90d | SME receivables |
| **Cobra HOY (2x levered)** | **9–12%** | **15–60d** | **Spanish insurers** |
| European private credit | 9–13% | 2–5y | Mid-market corporates |

**The positioning:** trade-finance-level returns with better obligor quality (regulated Spanish insurers, not SMEs) and shorter duration. The trade-off is no track record at launch — compensated by first-mover terms and optionality on a €900M+ annual market.

---

## Negative Scenarios — What Happens When Things Go Wrong

### Insurer payment delay or claim adjustment

**Trigger:** An insurer pays later than expected, or settles at a lower amount than billed.

**Consequence:** The advance stays outstanding longer than modeled, or the settlement shortfall reduces repayment. The haircut — sized using SEOGA's historical deviation data per insurer — absorbs the difference. If the adjustment exceeds the haircut, the hospital still owes the SPV the full advance amount under the lending agreement.

**Frequency:** Routine — this is the most common scenario. SEOGA's conciliation data quantifies deviation rates per insurer. The haircut methodology is validated against historical actuals.

**Recovery path:** SEOGA-calibrated haircuts per insurer; continuous back-testing; diversification across 60+ insurers.

### Insurer insolvency

**Trigger:** A health insurer fails entirely and cannot pay outstanding claims.

**Consequence:** The insurer's payment never arrives. However, **the hospital remains fully liable to the SPV.** The advance is a B2B loan — the insurer is not a party to the lending agreement. The SPV has dual recourse: (1) a contractual claim against the hospital as borrower, and (2) a notarized pledge over credit rights with special privilege status (*privilegio especial*) under Article 270 TRLC, enforceable in bankruptcy ahead of unsecured creditors. This dual-recourse structure is stronger than pure receivables factoring, where the buyer absorbs full insurer risk with no claim back against the hospital.

**Frequency:** Near-zero for the health insurers in Cobra HOY's universe. The DGSFP revoked authorization for roughly 12–15 entities between 2015 and 2025 — nearly all small *mutualidades*, not major health insurers. The most recent notable case (Hello Auto, July 2025) was a small auto insurer. The major health insurers Cobra HOY is exposed to — Sanitas (Bupa), SegurCaixa Adeslas (CaixaBank), Mapfre (207% SCR ratio), DKV (Munich Re), ASISA — are heavily capitalized under Solvency II.

**Recovery path:** Hospital repayment obligation; concentration limits per insurer; short duration (15–60 days) limits exposure window; SEOGA flags early-warning signals from insurer payment behavior.

### Hospital insolvency

**Trigger:** The hospital enters *concurso de acreedores* under the *Ley Concursal*.

**Consequence:** The SPV's notarized Umbrella Pledge grants *privilegio especial* under Article 270 TRLC — priority over unsecured creditors. The insolvency administrator (*administración concursal*) may impose an enforcement stay of up to one year on assets deemed necessary for operations. During this period, pledged account sweeps require administrator or judicial authorization. If the pledge qualifies as financial collateral under Royal Decree-Law 5/2005, it is ring-fenced from the stay entirely.

**Frequency:** Uncommon in Spain's private healthcare sector. Hospitals with active SEOGA relationships are established centers with diversified insurer contracts. The short advance duration (15–60 days) caps exposure at any moment.

**Recovery path:** Special privilege in insolvency; concentration limits per hospital; short duration limits maximum exposure; SEOGA provides early visibility into hospital financial stress (declining claim volumes, increasing disputes).

### Pledged account interference

**Trigger:** The hospital attempts to redirect funds from the pledged account before the daily sweep executes.

**Consequence:** The tripartite control agreement contractually binds the bank to reject hospital-only payment instructions. The account is configured with restricted access — view-only for the hospital, no unilateral outbound transfers. If the bank fails to enforce the restriction, the SPV has a contractual claim against the bank. Post-insolvency, sweeps require insolvency administrator authorization.

**Frequency:** Low — requires both bad faith by the hospital and an operational failure by the bank.

**Recovery path:** Bank partner due diligence on operational controls; real-time pledged account monitoring via bank API; contractual bank liability; RDL 5/2005 financial collateral structuring as potential enhancement.

### Systemic risk

**Trigger:** A macroeconomic event or sector-wide shock affecting multiple insurers or hospitals simultaneously.

**Consequence:** Portfolio-wide payment delays rather than isolated defaults. COVID-19 provides the most relevant precedent — private healthcare demand in Spain *increased* as public system waiting lists grew. Insurance sector stress under Solvency II would manifest as delays, not failures.

**Frequency:** Tail risk. The Solvency II framework and DGSFP supervision make coordinated insurer stress a low-probability scenario.

**Recovery path:** Portfolio diversification across 60+ insurers and multiple hospitals; short duration means the portfolio naturally de-risks within 60 days if origination is paused; SEOGA's real-time data enables early detection of sector-wide changes.

---

## MVP Scope

| Dimension | Detail |
|-----------|--------|
| **Hospitals** | 5–10 pilot centers |
| **Capital deployed** | €2–5M at any given time |
| **Structure** | Single SPV, single pool, no tranching |
| **Timeline** | 2–3 months to operational readiness |
| **Regulatory cost** | €75–185K (SPV formation, AML, GDPR, legal drafting) |

### Success Metrics
- Hospital adoption rate and percentage of eligible claims advanced
- Model accuracy: SEOGA credit score predictions vs. actual insurer payment outcomes
- Capital recycling velocity and realized yield

---

## Why Family Offices

This opportunity is best suited to family offices and principals who value:

- **Proprietary deal flow** — no intermediary, no auction, no competition for allocation
- **Data-driven underwriting** — SEOGA's claim-level intelligence provides transparency unavailable in traditional lending
- **Short duration** — capital returns in weeks, not years; natural liquidity without secondary market dependency
- **Institutional obligor quality** — economic exposure to regulated Spanish insurers, not SME or consumer credit
- **First-mover optionality** — seed-stage entry into a €900M+ annual market with no existing product competition
- **Structural simplicity** — passive SPV participation with full transparency via investor dashboard

### First Investor Terms

The first investor receives preferential economics in exchange for accepting the new-product risk. Terms are negotiable and structured to align incentives during the proof-of-concept phase.

---

## Parties

| Entity | Role |
|--------|------|
| **SEOGA** | Data, credit scoring, insurer liaison, hospital distribution (1,000+ centers, 60+ insurers) |
| **Laminar** | Financial structure design, technology platform, capital sourcing |
| **Bank partner** | Pledged accounts, daily sweep execution, payment rails (TBD — Spanish commercial bank) |
| **SPV** | Single lending vehicle; sole lender of record, pledgee, and control agreement counterparty |
| **Legal counsel** | Structure validation, template drafting, GDPR/DPIA (TBD — Spanish fintech-focused firm) |

---

## Next Steps

1. **NDA execution** with SEOGA to access data room and validate architecture assumptions
2. **Legal counsel engagement** — structural opinion, Umbrella Pledge and control agreement template drafting
3. **Bank partner identification** — Spanish bank willing to offer API-controlled pledged accounts at scale
4. **SPV formation** — Spanish S.L. for single-investor MVP; upgrade to Luxembourg SCSp for multi-investor scale
5. **Pilot hospital onboarding** — 5–10 centers, leveraging SEOGA's existing relationships

---

*This document is confidential and intended for qualified investors. It does not constitute an offer to sell or solicitation of an offer to buy any securities.*
