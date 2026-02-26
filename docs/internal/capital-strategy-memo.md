# Cobra HOY — Capital Strategy Memo

**Laminar internal | 2026-02-18 | Working draft**

---

## 1. What Capital Does

The SPV advances cash to hospitals against expected insurer payments. Hospitals perform medical acts, insurers owe money under convenio terms, and SEOGA has claim-level data on what's owed, by whom, and when it typically pays. The SPV uses this data to fund hospitals before the insurer settles, then collects when the insurer pays into a pledged account.

Capital recycles every 15–60 days. A €3M facility can deploy €20M+ annualized. The investor is passive — money goes into the SPV, the SPV does everything.

MVP scope: 5–10 hospitals, €2–5M deployed at any given time.

---

## 2. Why There's No Competition

This is the single most important point in the capital raise.

There is no product on the market that finances hospital claims against insurers under convenio arrangements. The reason is structural:

- **Factoring requires invoices.** Convenio-based claims aren't invoices. They're conditional payment obligations arising from bilateral framework agreements between hospitals and insurers. No invoice, no factoring.
- **Bank credit lines are generic.** Banks lend against the hospital's balance sheet, not against specific insurer payment flows. Slow, rigid, all-or-nothing, and they hit CIRBE (the hospital's credit bureau record).
- **Confirming is supplier-side.** It helps hospitals pay their vendors, not get paid by their insurers.

The only entity that can build this product is one with claim-level data mapping medical acts to insurer settlement behavior. SEOGA is that entity. They process €75–80M/month across 1,000+ centers and 60+ insurers.

**Pricing consequence:** The hospital isn't choosing between Cobra HOY and cheaper factoring. They're choosing between Cobra HOY and waiting 30–120 days with no alternative. This supports pricing above where traditional factoring sits.

---

## 3. Two Revenue Streams, One Capital Pool

### Stream 1: Hospital advance

The hospital gets paid now instead of waiting. Fee: ~0.65% per advance (7.9% annualized on a 30-day cycle). Defensible because:

- No competing product exists
- No CIRBE impact (doesn't consume their borrowing capacity)
- One click inside SEOGA — no application, no documents, no bank meetings
- Tax-deductible (effective after-tax cost: ~5.9%)

### Stream 2: Physician advance

SEOGA already calculates what each doctor should be paid from each insurer settlement. Doctors wait even longer than hospitals — insurer delay plus the hospital's own payment cycle to physicians. Total wait: 45–120 days.

Doctors have higher willingness to pay than hospitals. They're individuals, not companies. A 1% fee on a €5,000 honorario = €50. The doctor doesn't think in annualized terms. They think: "€50 to get my money two months early."

The structure avoids consumer credit regulation: the SPV lends to the hospital (B2B), the hospital pays the doctor early, the doctor's fee is deducted from the honorario. The SPV's counterparty is always the hospital, never the individual doctor.

For hospitals, offering early physician payment is a recruitment and retention tool for specialists. The hospital doesn't pay — the doctor pays. This gives hospitals two reasons to adopt Cobra HOY: their own working capital, and physician satisfaction.

**Physician fee: ~1.25% per advance** (15.2% annualized on a 30-day cycle).

---

## 4. Return Model

### Base case assumptions

| Parameter | Value |
|-----------|-------|
| Average advance duration | 30 days |
| Hospital fee | 0.65% per advance |
| Physician fee | 1.25% per advance |
| Physician volume (% of hospital flow) | 40% |
| Capital utilization | 88% |
| Credit losses (annualized, net of haircut) | 0.50% |
| SPV operating costs (annualized) | 0.40% |
| Bank/transaction costs (annualized) | 0.15% |
| Laminar fee (annualized, from SPV) | 0.75% |
| SEOGA fee (annualized, from SPV) | 0.50% |

### Waterfall

| | Value |
|---|---|
| Blended gross yield | 10.8% |
| After utilization (88%) | 9.5% |
| Less costs and losses | (1.05%) |
| Less Laminar + SEOGA fees | (1.25%) |
| **Net to investor (unlevered)** | **7.25%** |

### With leverage

| | Unlevered | 1.5x | 2x |
|---|---|---|---|
| Equity | €3M | €3M | €3M |
| Borrowed (@ 3.7%) | — | €1.5M | €3M |
| **Return on equity** | **7.25%** | **9.0%** | **10.8%** |

### Sensitivity (net to investor, unlevered)

| | Phys. 1.00% | Phys. 1.25% | Phys. 1.50% |
|---|---|---|---|
| **Hosp. 0.50%** | 5.4% | 6.2% | 6.9% |
| **Hosp. 0.65%** | 6.3% | 7.25% | 8.0% |
| **Hosp. 0.80%** | 7.2% | 8.1% | 8.9% |

At 2x leverage, the base case reaches **10.8%**. The aggressive case (0.80% hospital / 1.50% physician) reaches **14.1%**.

---

## 5. Comparables

| Product | Net return | Duration | Obligor quality |
|---------|-----------|----------|----------------|
| EUR money market | 2.5–3% | <90d | Sovereign/bank |
| Supply chain finance fund | 4–8% | 30–90d | Corporates |
| **Cobra HOY (unlevered)** | **7–8%** | **15–60d** | **Spanish insurers** |
| Trade finance fund (Fasanara-type) | 9–12% | 30–90d | SME receivables |
| **Cobra HOY (2x levered)** | **9–12%** | **15–60d** | **Spanish insurers** |
| European private credit | 9–13% | 2–5y | Mid-market corporates |
| Spain bridge lending | 8–15% | 6–18m | Property |

The positioning: trade-finance-level returns with better obligor quality and shorter duration. The trade-off is no track record and no liquidity at MVP — compensated by first-mover terms and optionality on a €900M+ annual market.

---

## 6. Investor Targeting

### What the first investor must accept

1. New product, no track record (SEOGA has data history; the lending product is new)
2. Small scale (€2–5M deployed)
3. Spain-only, EUR-denominated
4. No tranching, no rating at MVP
5. Illiquid (though capital naturally returns in 15–60 days)

### Ranking by friction

| Rank | Profile | Ticket | Timeline | Notes |
|------|---------|--------|----------|-------|
| 1 | Family office (Spanish, healthcare/insurance background) | €1–5M | 4–8 weeks | Fastest. Knows the market. No structural demands. |
| 2 | Mid-market European credit fund (Iberian/specialty) | €3–10M | 2–4 months | Right size. Comfortable with simple structures. Wants data — SEOGA delivers. |
| 3 | Trade finance / specialty finance fund | €2–10M | 1–3 months | Understands the model natively. May need Spain education. |
| 4 | Beka Healthcare (strategic) | €5–15M | 3–6 months | Sector expertise. But institutional process, and they may see us as adjacent to their own product. Frame as complementary: they finance suppliers TO hospitals, we finance hospitals directly. |
| 5 | Bank partner (dual role) | €5–10M | 4–6 months | Perfect alignment if the bank both holds the pledged accounts and invests. But complicates the bank partner negotiation. |
| 6 | Reinsurer investment arm | €5–20M | 6–12 months | Great signal. Too slow for MVP. |
| 7 | Large institutional credit fund | €25M+ | 6–12 months | Wrong stage. Phase 2 target. |

---

## 7. Open Questions for SEOGA

These determine whether the return model holds and whether the physician advance is viable at launch:

1. What percentage of SEOGA-connected hospitals pay physicians per-act (vs. salary)?
2. What is the typical delay from insurer settlement to physician payment?
3. Average honorario per medical act? Per physician per month?
4. Has SEOGA received requests from physicians for faster payment?
5. Would hospitals offer early physician payment as a feature? Do they see it as competitive advantage?
6. What is the actual distribution of payment timing by insurer? (% at 15d, 30d, 45d, 60d+)
7. What is the historical deviation rate between registered activity and actual settlement?

---

## 8. Negative Scenarios — What Happens When Things Go Wrong

The structure is designed so that an investor's capital is never dependent on a single point of failure. Below are the five risk vectors, what triggers them, how the structure responds, and what the realistic frequency looks like.

### 8.1 Insurer payment delay or claim adjustment

**Trigger:** An insurer pays later than expected, or settles a claim at a lower amount than billed (baremo deviation, session limits, normas de valoración).

**What happens:** The advance stays outstanding longer than modeled, or the settlement amount is less than the funded advance. The haircut — sized using SEOGA's historical deviation data per insurer — absorbs the shortfall. If the adjustment exceeds the haircut, the hospital still owes the SPV the full advance amount under the lending agreement. The SPV's claim is against the hospital, not the insurer.

**Frequency:** This is the most common scenario. SEOGA's conciliation data shows insurer adjustments are routine — the precise deviation rates per insurer are a key data-room deliverable. The haircut methodology must be validated against historical actuals before MVP launch.

**Mitigation:** SEOGA-calibrated haircuts per insurer; continuous back-testing of model predictions vs. actual outcomes; diversification across 60+ insurers reduces the impact of any single insurer's payment behavior.

### 8.2 Insurer insolvency

**Trigger:** A health insurer fails entirely and cannot pay outstanding claims.

**What happens:** The insurer's payment never arrives in the pledged account. However — and this is a critical structural point — **the hospital remains fully liable to the SPV.** The advance is a B2B loan from the SPV to the hospital. The insurer is not a party to the lending agreement. The hospital's obligation to repay does not disappear because its expected revenue source failed.

The SPV therefore has **dual recourse:**

1. **Contractual claim against the hospital** as borrower under the lending agreement — enforceable like any commercial debt
2. **Notarized pledge over credit rights** (*prenda sobre derechos de crédito*) — enforceable in bankruptcy with special privilege status under Article 270 TRLC

This dual-recourse structure is stronger than pure receivables factoring, where the buyer takes full insurer risk with no claim back against the hospital.

In practice, if a major insurer defaulted, the hospital's liquidity would be stressed — but hospitals are diversified across many insurers. A hospital where one insurer represents 25% of revenue loses 25% of expected cash flow, not 100%. The hospital remains a going concern with other revenue streams.

**Frequency:** Extremely rare. Spanish insurer insolvency is a near-zero probability event for the health insurers in Cobra HOY's universe:

- The DGSFP (Spain's insurance regulator) revoked authorization for roughly 12–15 entities between 2015 and 2025. Nearly all were small *mutualidades de previsión social* (mutual provident societies) and niche players — not major health insurers.
- The most notable recent case was Hello Auto (July 2025), a small auto insurer that failed to meet the €4M minimum capital requirement. It was not a health insurer.
- EIOPA's EU-wide "Failures and Near Misses" database recorded only 219 cases across all 31 EEA countries from 1999–2021. Spain had only 7 entities with solvency ratios below 1 in the 2005–2012 period studied.
- The major health insurers Cobra HOY is exposed to are backed by some of the largest financial groups in Europe: Sanitas (Bupa Group), SegurCaixa Adeslas (CaixaBank + Mutua Madrileña), Mapfre (207% SCR ratio, 2024), DKV (Munich Re), and ASISA (cooperative with 4M+ policyholders). These entities are heavily capitalized under Solvency II and subject to continuous DGSFP supervision.
- When an insurer does fail, the Consorcio de Compensación de Seguros (CCS) manages the liquidation and portfolio transfers — but CCS protects policyholders, not hospital trade receivables. Hospital claims in an insurer liquidation would be ordinary unsecured claims in the insurer's estate.

**Mitigation:** Portfolio-level concentration limits per insurer enforced by the Advance Engine; short duration (15–60 days) limits exposure window; dual recourse to hospital; SEOGA can flag early-warning signals from insurer payment behavior changes.

### 8.3 Hospital insolvency

**Trigger:** The hospital — the SPV's legal counterparty — enters *concurso de acreedores* (insolvency proceedings) under the *Ley Concursal* (TRLC, RDL 1/2020).

**What happens:** The insolvency declaration triggers several effects:

**The SPV's protection — special privilege:** The notarized Umbrella Pledge (*prenda marco*) over credit rights grants the SPV *privilegio especial* under Article 270 TRLC. This means the SPV is paid from the pledged account balance with priority over unsecured creditors. The privilege applies provided the pledge was formalized in a public deed (*documento público*) or a document with a reliable date (*fecha fehaciente*) prior to the insolvency declaration.

**The risk — enforcement stay:** Secured creditors cannot enforce their security interests for up to one year from the insolvency declaration if the pledged assets are deemed "necessary for the business activity" (*bienes necesarios para la continuidad de la actividad profesional o empresarial*). A hospital's insurer receivables could plausibly be classified as necessary. During the stay, the insolvency administrator (*administración concursal*) controls the hospital's assets — and banks cannot execute sweeps from the pledged account without authorization from the administrator or the insolvency judge.

**Potential escape — financial collateral regime:** If the pledge over the bank account qualifies as financial collateral under Royal Decree-Law 5/2005 (which transposed the EU Financial Collateral Directive), it would be ring-fenced from the automatic stay entirely. Whether healthcare receivables pledges qualify under RDL 5/2005 is an open question that must be resolved by counsel before MVP launch.

**Clawback risk:** The insolvency administrator can use *acciones rescisorias* to rescind transactions from the two years prior to insolvency that are deemed detrimental to the estate — without needing to prove fraud. This means if a hospital signed the Umbrella Pledge within 2 years of entering *concurso*, the pledge could theoretically be challenged. Mitigation: the pledge is at arm's length, provides real economic value to the hospital (working capital access), and is a standard commercial arrangement — characteristics that typically qualify for the safe harbor under the TRLC.

**Frequency:** Hospital insolvency in Spain's private healthcare sector is uncommon. Private hospitals with active SEOGA relationships are established centers with diversified insurer contracts. The short duration of advances (15–60 days) further limits exposure — the maximum amount at risk at any moment is the sum of outstanding advances to that hospital, not a long-term credit commitment.

**Mitigation:** Concentration limits per hospital; short advance duration limits exposure window; notarized pledge provides special privilege; tripartite control agreement provides practical cash-flow control pre-insolvency; SEOGA relationship provides early visibility into hospital financial stress (declining claim volumes, increasing disputes).

### 8.4 Pledged account interference

**Trigger:** The hospital attempts to redirect funds from the pledged account before the daily sweep executes — either in bad faith or under financial pressure.

**Pre-insolvency (hospital is solvent):** The tripartite control agreement contractually binds the bank to reject hospital-only payment instructions on the pledged account. The account should be configured with restricted access — view-only for the hospital, no unilateral outbound transfers. The daily sweep should be the first operation each banking day.

However, Spanish law does not have a statutory "control agreement" framework equivalent to the US Deposit Account Control Agreement (DACA). The tripartite agreement is a contractual arrangement — its enforceability depends on the specificity of its terms and the bank's operational compliance. If the bank fails to block a hospital-initiated transfer (due to human error, system misconfiguration, or process gap), the SPV's recourse is a contractual claim against the bank for breach of the control agreement.

**Post-insolvency:** Once the hospital enters *concurso*, the insolvency administrator assumes control of the hospital's assets. The bank is legally prohibited from executing sweeps without the administrator's authorization or a court order. The SPV must then enforce its security interest through the insolvency process (subject to the enforcement stay discussed in 8.3).

**Design implications for MVP:**

1. **Bank partner selection is a structural decision, not a vendor decision.** The bank must have the technical capability and operational discipline to enforce account-level restrictions in real time — not just in its contracts.
2. **The control agreement must explicitly restrict:** (a) all hospital-initiated outbound transfers without SPV co-authorization, (b) standing orders and direct debits from the pledged account, (c) online banking write access for the hospital. The bank's sweep must execute as the first daily operation.
3. **Explore RDL 5/2005 qualification.** If the pledge can be structured as financial collateral under RDL 5/2005, the SPV gains the right to enforce even during *concurso* without waiting for the enforcement stay to expire. This is a high-priority question for legal counsel.

**Mitigation:** Bank partner due diligence on operational controls; contractual liability for the bank if it fails to enforce the control agreement; real-time pledged account monitoring via bank API (CAMT.053/054) to detect unauthorized movements immediately; RDL 5/2005 structuring as a potential enhancement.

### 8.5 Systemic or correlated risk

**Trigger:** A macroeconomic event, regulatory change, or sector-wide shock that simultaneously affects multiple insurers or hospitals — degrading the entire portfolio rather than individual exposures.

**Scenarios considered:**

- **Pandemic or public health crisis:** COVID-19 provides the most relevant precedent. Private healthcare demand in Spain *increased* during and after COVID as public system waiting lists grew. Private hospital revenues were resilient. This risk may be inversely correlated — a public health crisis increases demand for private care.
- **Regulatory shock:** A change in Spanish law requiring BdE licensing for B2B lending, or AEPD enforcement action against health-data-based credit scoring. Impact: operational disruption, not capital loss. Existing advances would still settle; new origination would pause until resolved.
- **Insurance sector stress:** A systemic event (e.g., major catastrophe, regulatory capital requirement tightening) that strains multiple Spanish insurers simultaneously. The Solvency II framework and DGSFP supervision make this a low-probability scenario, but it would manifest as widespread payment delays rather than defaults. The short duration of advances limits the exposure window.

**Mitigation:** Portfolio diversification across 60+ insurers and multiple hospitals; short duration means the portfolio naturally de-risks within 60 days if new origination is paused; SEOGA's real-time data provides early detection of sector-wide payment behavior changes; the SPV can stop originating new advances within hours if systemic stress is detected.

---

## 9. What Goes Into the Investor Document

The investor-facing 2-pager needs to land six points:

1. **The market gap.** €900M+/year in insurer payments to hospitals. No existing product finances these flows. We built one.
2. **The data moat.** SEOGA processes the claims. Without their data, you can't price individual claims, you can't score insurers, and you can't build the product. This isn't replicable.
3. **Two revenue streams.** Hospitals pay for speed. Doctors pay more. Same capital, same obligor risk, double the yield.
4. **The structural protection.** Pledged accounts, tripartite control agreements, notarized pledge over credit rights. Capital is secured against institutional obligors (regulated Spanish insurers), not against the hospital's general creditworthiness.
5. **The return profile.** 7–8% unlevered, 9–12% with modest leverage. Competitive with trade finance and private credit, with better obligor quality and shorter duration.
6. **The downside protection.** Dual recourse (hospital as borrower + notarized pledge with special privilege in insolvency). Insurer default is near-zero probability for regulated Spanish health insurers. Short duration means the portfolio naturally de-risks within 60 days.
