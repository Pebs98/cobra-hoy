# Cobra HOY — Three-Party Incentive Framework

**Laminar internal | Working draft | 2026-02-26**
**Status:** For discussion — not shared with SEOGA or investors

---

## 1. Structure Overview

```
┌──────────────────────────────────────────────────────────┐
│                    SPV (SCSp / SL)                        │
│                                                          │
│  LPs: Investors (capital)                                │
│  GP:  ManCo (operations + management)                    │
│                                                          │
│  Revenue:  Hospital fees + Physician fees                │
│  Pays out: Hard costs → Investor preferred return → ManCo│
│                                                          │
│  ManCo earns the SPREAD RESIDUAL, not a fixed fee        │
└──────────────────────────────────────────────────────────┘
           │ Spread residual (variable — driven by
           │ cost of capital Laminar raises at)
           ▼
┌──────────────────────────────────────────────────────────┐
│           ManCo (Management Company)                      │
│                                                          │
│  Equity: Laminar (100%)                                  │
│  Contractual participants: SEOGA (service agreement)     │
│                                                          │
│  Revenue = Gross yield − Hard costs − Investor return    │
│  Distributes: Base fees + Performance bonuses             │
└──────────────────────────────────────────────────────────┘
```

**Key design principle:** The ManCo does not charge a fixed management fee. It earns the spread residual — the difference between what the SPV earns from hospitals and what it pays to investors (after hard costs). This creates a direct incentive for Laminar to raise capital at the lowest possible cost, because every basis point of cheaper capital becomes ManCo revenue shared with SEOGA.

Investors see a preferred return (their negotiated rate). Everything behind it — how the spread is generated, how SEOGA and Laminar split it, what the performance targets are — is an internal ManCo matter governed by the SEOGA-Laminar service agreement.

---

## 2. POC Waterfall — Hospital Advances Only

The POC phase focuses exclusively on hospital advances (no physician stream). The fee is set at **1.00% per advance** — higher than the steady-state assumption (0.65%) because at POC scale, hospitals have zero alternatives and the product is unproven, so the pricing reflects both scarcity value and the cost of being an early adopter of a new financial product. The physician advance is a Phase 2 product extension.

### POC Assumptions

| Parameter | Value | Notes |
|-----------|-------|-------|
| Revenue stream | Hospital advances only | No physician advance at POC |
| Hospital fee per advance | 1.00% | Higher than steady-state; reflects zero competition + new product premium |
| Average advance duration | 30 days | |
| Capital utilization | 85% | Conservative for POC (lower than steady-state 88% — still building distribution) |
| Credit losses | 0.50% | Same conservative assumption |
| Bank & transaction costs | 0.15% | |
| SPV admin | 0.20% | |

### POC Fee Waterfall

| Line | Annualized | Notes |
|------|-----------|-------|
| Gross yield (hospital only) | 12.0% | 1.00% × 12 cycles per year |
| After utilization (85%) | **10.20%** | |
| Less: Hard costs | (0.85%) | Credit losses + bank + admin |
| **Net available for distribution** | **9.35%** | |
| Less: Investor preferred return | (negotiated) | |
| **ManCo spread residual** | **= 9.35% − investor return** | |

### POC Spread Sensitivity

| Investor preferred return | ManCo spread | ManCo revenue (€3M) | ManCo revenue (€5M) |
|--------------------------|-------------|---------------------|---------------------|
| 8.00% | 1.35% | €40.5K | €67.5K |
| 7.50% | 1.85% | €55.5K | €92.5K |
| **7.00%** | **2.35%** | **€70.5K** | **€117.5K** |
| 6.50% | 2.85% | €85.5K | €142.5K |
| 6.00% | 3.35% | €100.5K | €167.5K |

**Key observation:** The POC economics are stronger than the blended steady-state model. A 1.00% hospital-only fee at 85% utilization produces 9.35% net available — wider than the 8.65% in the blended model (0.65% hospital + 1.25% physician at 88% utilization). This means:

- At 7% investor return, the ManCo spread is **2.35%** at POC vs. 1.65% at steady state
- SEOGA and Laminar earn more per euro deployed at POC than at scale — which is the right incentive structure for early-stage risk
- There is margin to **reduce** the hospital fee over time (toward 0.65–0.80%) as the facility scales and the physician stream comes online — without compressing ManCo economics

### POC ManCo Economics (at 7% investor return)

| Deployed | ManCo total | Laminar base (35%) | SEOGA base (25%) | Perf. pool (35%) | Reserve (5%) |
|----------|-------------|-------------------|-----------------|-----------------|-------------|
| €2M | €47K | €16.5K | €11.8K | €16.5K | €2.4K |
| €3M | €70.5K | €24.7K | €17.6K | €24.7K | €3.5K |
| €5M | €117.5K | €41.1K | €29.4K | €41.1K | €5.9K |

### POC → Steady State Transition

The hospital fee stays at **1.00%** across all phases. There is no competing product — hospitals are choosing between Cobra HOY and waiting 30–120 days with no alternative. The physician stream adds yield on top.

| Phase | Hospital fee | Physician fee | Blended gross yield | Notes |
|-------|-------------|--------------|--------------------|--------------------|
| **POC (5–10 hospitals)** | 1.00% | — | 12.0% | Hospital only; proving the model |
| **Scale-up (50+ hospitals)** | 1.00% | 1.25% (introduced) | ~13.2% | Physician stream adds ~1.2% blended yield on top of hospital base |
| **Steady state (200+ hospitals)** | 1.00% | 1.25% | 13.2% | Full product suite; both streams at scale |

The physician stream is pure upside — it widens the spread for SEOGA and Laminar without requiring a fee reduction on the hospital side. At steady state with both streams, the net available for distribution is significantly wider than the POC, meaning everyone's economics improve as the product matures.

---

## 3. Steady-State Waterfall — Blended Model (Hospital + Physician)

### Assumptions

| Parameter | Value |
|-----------|-------|
| Average advance duration | 30 days |
| Hospital fee per advance | 1.00% |
| Physician fee per advance | 1.25% |
| Physician volume (% of hospital flow) | 40% |
| Capital utilization | 88% |

### Fee Waterfall

| Line | Annualized | Notes |
|------|-----------|-------|
| Blended gross yield | 13.2% | (60% × 1.00% + 40% × 1.25%) × 12 cycles |
| After utilization (88%) | **11.62%** | Revenue the SPV actually earns |
| Less: Credit losses (net of haircut) | (0.50%) | Conservative; SEOGA model targets <0.30% |
| Less: Bank & transaction costs | (0.15%) | SEPA transfers, account maintenance |
| Less: SPV admin (audit, legal, custodian) | (0.20%) | Pass-through hard costs |
| **Net available for distribution** | **10.77%** | Split between investors and ManCo |
| Less: Investor preferred return | (negotiated) | See sensitivity table below |
| **ManCo spread residual** | **= 10.77% − investor return** | Variable — Laminar incentivized to minimize cost of capital |

### Spread Sensitivity — How Cost of Capital Drives Everyone's Economics

| Investor preferred return | ManCo spread | ManCo revenue (€3M) | ManCo revenue (€50M) | Investor gets |
|--------------------------|-------------|---------------------|---------------------|--------------|
| 8.00% | 2.77% | €83.1K | €1.39M | 8.00% |
| 7.50% | 3.27% | €98.1K | €1.64M | 7.50% |
| **7.00%** | **3.77%** | **€113.1K** | **€1.89M** | **7.00%** |
| 6.50% | 4.27% | €128.1K | €2.14M | 6.50% |
| 6.00% | 4.77% | €143.1K | €2.39M | 6.00% |

**The incentive:** If Laminar raises capital at 6% instead of 7%, ManCo revenue increases by 27% (from €1.89M to €2.39M on €50M deployed). Both SEOGA and Laminar benefit directly. Even at 8% investor return, ManCo earns a healthy 2.77% spread — the 1.00% hospital fee creates a structurally wide margin.

**What investors see:** A preferred return — their negotiated rate. The SPV's offering documents define the preferred return; the ManCo spread is simply the operating margin of the business. This is structurally identical to how any lending business works: borrow at X, lend at Y, keep the spread.

### With Leverage (at 7% preferred return)

| | Unlevered | 1.5× | 2× |
|---|---|---|---|
| Investor return | 7.00% | 8.6% | 10.3% |
| ManCo spread | 3.77% | 3.77% | 3.77% |

Note: Leverage amplifies investor returns without changing the ManCo spread (leverage cost is borne by the SPV and reduces investor net return, not ManCo income). ManCo benefits from leverage indirectly — it makes the facility more attractive to investors, enabling Laminar to raise at lower preferred returns.

### How This Evolves Over Time

As the facility builds a track record, investors will accept lower preferred returns. The spread — already wide at 1.00% hospital fee — widens further:

| Phase | Likely investor return | ManCo spread | Driver |
|-------|----------------------|-------------|--------|
| MVP (no track record) | 7.0–8.0% | 2.77–3.77% | First-mover pricing; investors demand premium for new product risk |
| Year 1 (track record building) | 6.5–7.0% | 3.77–4.27% | Data proves the model; SEOGA scoring validated; operational track record |
| Year 2+ (proven) | 5.5–6.5% | 4.27–5.27% | Competitive with supply chain finance on a risk-adjusted basis; secondary market potential |
| On-chain (Phase 2) | 5.0–6.0% | 4.77–5.77% | Tokenized pool attracts broader capital base; transparency drives tighter pricing |

**This is the long-term value proposition for SEOGA and Laminar:** The 1.00% hospital fee creates a structurally wide base margin. As the track record brings cheaper capital, the spread only grows. At Year 2+ with 6% investor return, the ManCo spread is nearly 5% — a highly profitable operating business.

---

## 4. ManCo Internal Economics

### Revenue Allocation

The ManCo's total revenue is the spread residual (variable). Internally, this splits three ways. The percentages stay constant — the absolute amounts grow as the spread widens (cheaper capital) and as deployed capital scales.

| Bucket | Share | Purpose |
|--------|-------|---------|
| **Laminar base** | 35% | Platform operations, bank partner management, structuring, ongoing legal |
| **SEOGA base** | 25% | Data operations, scoring model maintenance, insurer liaison, hospital distribution support |
| **Performance pool** | 35% | Quarterly allocation based on KPI scorecard (see §5) |
| **ManCo reserve** | 5% | Operating buffer, unexpected costs |

### Illustrative Economics — Two Dimensions of Growth

ManCo revenue grows on **two axes**: deployed capital (SEOGA drives utilization) AND spread width (Laminar drives cost of capital). This table shows the interplay:

**At 7.0% investor return (3.77% spread):**

| Deployed capital | ManCo total | Laminar base | SEOGA base | Perf. pool | Reserve |
|-----------------|-------------|-------------|------------|------------|---------|
| €3M (MVP) | €113K | €39.6K | €28.3K | €39.6K | €5.7K |
| €10M | €377K | €132K | €94.3K | €132K | €18.9K |
| €50M | €1.89M | €660K | €471K | €660K | €94K |
| €100M | €3.77M | €1.32M | €943K | €1.32M | €189K |
| €200M (full network) | €7.54M | €2.64M | €1.89M | €2.64M | €377K |

**At 6.0% investor return (4.77% spread) — post track record:**

| Deployed capital | ManCo total | Laminar base | SEOGA base | Perf. pool | Reserve |
|-----------------|-------------|-------------|------------|------------|---------|
| €3M (MVP) | €143K | €50.1K | €35.8K | €50.1K | €7.2K |
| €10M | €477K | €167K | €119K | €167K | €23.9K |
| €50M | €2.39M | €835K | €596K | €835K | €119K |
| €100M | €4.77M | €1.67M | €1.19M | €1.67M | €239K |
| €200M (full network) | €9.54M | €3.34M | €2.39M | €3.34M | €477K |

**The compounding effect:** At €50M deployed, moving from 7% to 6% investor return increases ManCo revenue by 26% — from €1.89M to €2.39M. SEOGA's total potential (base + performance) goes from ~€801K to ~€1.01M/year. The 1.00% hospital fee creates a wide enough base that the ManCo is a substantial business even at higher investor costs.

### For SEOGA — Comparison to Alternatives

| Alternative | SEOGA earns (on €50M volume) | Recurring? | Upside? | Capital at risk? |
|-------------|------------------------------|-----------|---------|-----------------|
| Bank referral deal | €50–75K one-time | No | No | No |
| Fintech equity offer | Unknown (illiquid equity) | No | Yes (if it works) | Yes |
| **Cobra HOY (Year 1, 7% investor)** | **~€801K/year** | **Yes** | **Yes (spread widens)** | **No** |
| **Cobra HOY (Year 2+, 6% investor)** | **~€1.01M/year** | **Yes** | **Yes (continues widening)** | **No** |

SEOGA earns more by doing what they're already good at (scoring, distribution), and their income grows automatically as Laminar successfully raises cheaper capital over time. At the 1.00% hospital fee, the economics are substantial — SEOGA's share alone approaches €1M/year at €50M deployed.

---

## 5. Performance Pool — KPI Scorecard

The performance pool (35% of ManCo revenue) is allocated quarterly based on five metrics. Each metric has an owner — the party whose actions most directly determine performance. The pool is variable: it grows as the spread widens and capital scales — reinforcing the behaviors that drive both.

### Metrics

| # | Metric | Definition | Target | Weight | Owner |
|---|--------|-----------|--------|--------|-------|
| 1 | **Utilization rate** | Deployed capital ÷ available facility capacity, measured daily, averaged over quarter | ≥85% | 25% | SEOGA |
| 2 | **Scoring accuracy** | Annualized realized loss rate (net of haircut recovery) vs. model-predicted loss rate | Loss rate <0.50%; prediction within ±15% of actual | 25% | SEOGA |
| 3 | **Platform reliability** | Weighted composite: platform uptime, sweep execution success rate, settlement within SLA | ≥99.5% uptime; ≥99% sweep success; ≥95% settlements within SLA | 15% | Laminar |
| 4 | **Capital continuity** | Facility fully funded (no redemption gaps); capital recycling velocity on target | 100% of committed capital available; avg. cycle ≤35 days | 15% | Laminar |
| 5 | **Cost of capital improvement** | Weighted average investor preferred return, measured quarterly; improvement vs. prior quarter | Quarter-over-quarter reduction in blended investor cost | 20% | Laminar |

**Why metric 5 matters:** This directly rewards Laminar for raising cheaper capital — widening the spread that benefits both SEOGA and Laminar. It connects the "capital continuity" responsibility (keep the facility funded) with a quality dimension (fund it at the best terms). In early quarters when there's only one investor, this metric scores based on the initial terms negotiated. As the investor base diversifies, it tracks the blended cost.

### Scoring & Payout

Each metric scores 0–100%:
- **100%:** Target met or exceeded → full allocation for that metric
- **75%:** Within 10% of target → 75% allocation
- **50%:** Within 25% of target → 50% allocation
- **0%:** More than 25% below target → zero allocation for that metric

Unallocated performance pool amounts roll into the ManCo reserve (not redistributed).

### Illustrative Quarterly Allocation (€50M deployed, 7% investor return, all targets met)

| Metric | Pool allocation | SEOGA receives | Laminar receives |
|--------|----------------|---------------|-----------------|
| Utilization (25%) | €41.3K | €41.3K | — |
| Scoring accuracy (25%) | €41.3K | €41.3K | — |
| Platform reliability (15%) | €24.8K | — | €24.8K |
| Capital continuity (15%) | €24.8K | — | €24.8K |
| Cost of capital improvement (20%) | €33.0K | — | €33.0K |
| **Quarterly total** | **€165K** | **€82.5K** | **€82.5K** |
| **Annualized** | **€660K** | **€330K** | **€330K** |

Note: At a 6% investor return (wider spread), the same percentages yield larger absolute amounts — the performance pool would be €209K/quarter (€835K annualized). The system self-reinforces: Laminar's success at metric 5 (cheaper capital) grows the pool that rewards everyone.

---

## 6. Three-Party Term Sheet

### A. INVESTORS

| Dimension | Terms |
|-----------|-------|
| **Vehicle** | SPV (Luxembourg SCSp or Spanish SL at MVP) |
| **Participation** | LP interest in SPV; passive, no operational role |
| **Return** | Negotiated preferred return (target range: 6–8% depending on vintage and track record). Capital cycles every 15–60 days |
| **Duration** | Quarterly redemption windows with 30-day notice |
| **Fees** | No fixed management fee. Investors receive their preferred return; the ManCo earns the spread residual. Investors see this as: "I get my negotiated return; the operators earn their margin from the business, not from my pocket." |
| **Structural protection** | Double Lockbox (pledged accounts + tripartite control agreements); notarized Umbrella Pledge; dual recourse (hospital + pledge) |
| **Transparency** | Real-time investor dashboard: pool composition, utilization, loss rates, model accuracy, sweep activity, spread metrics |
| **Governance rights** | Quarterly reporting; annual audit; approval rights on material changes to investment guidelines (concentration limits, new asset types, leverage policy) |
| **Skin in the game** | Capital at risk. Mitigated by: short duration, institutional obligors (regulated Spanish insurers), diversification, bankruptcy-enforceable security interest |
| **What they DON'T do** | No operational involvement; no direct hospital relationships; no credit decisions |
| **Alignment with operators** | Investor returns are senior in the waterfall — they get paid before ManCo. Operators earn only if the business generates enough spread to cover investor returns AND hard costs. This means SEOGA and Laminar are structurally subordinate to investor returns. |

### B. SEOGA

| Dimension | Terms |
|-----------|-------|
| **Role** | Data layer + distribution + risk monitoring |
| **Specific obligations** | Credit scoring model (build, maintain, back-test); hospital distribution (origination UX, relationship management); insurer liaison (reconciliation, dispute resolution, collections); utilization targeting (proactive advance offers, hospital engagement) |
| **Compensation — base** | 25% of ManCo spread residual (contractual service agreement). At €50M / 7% investor return: ~€471K/year. At €50M / 6% investor return: ~€596K/year. |
| **Compensation — performance** | Up to 50% of the performance pool (metrics 1 + 2). At €50M / 7%, all targets met: ~€330K/year. At €50M / 6%: ~€418K/year. |
| **Total comp range** | At €50M / 7% investor return: ~€801K/year (base €471K + perf €330K). At €50M / 6%: ~€1.01M/year (base €596K + perf €418K). Grows on two axes: deployed capital AND spread width. |
| **Skin in the game** | Performance-linked income — scoring accuracy and utilization rate directly determine ~41% of SEOGA's total compensation. Additionally, the spread-share model means SEOGA's income grows automatically when Laminar raises cheaper capital — creating a shared interest in the track record that enables cheaper raises. No capital at risk. |
| **What SEOGA compares this to** | Bank referral deal: €50–75K one-time on same volume. Fintech equity offer: illiquid, risk-bearing. Cobra HOY: ~€800K/year at launch scaling to €1M+ as track record builds — recurring, no capital at risk. |
| **Boundaries** | SEOGA never touches money. Never holds a financial license. Never is a party to the lending agreement. Never directly interacts with investors. Their risk is reputational and income-based, not financial. |
| **Governance rights** | Seat on ManCo advisory committee; approval rights on changes to credit scoring methodology or hospital eligibility criteria; right to audit ManCo financials annually |
| **Exit / termination** | Service agreement with 12-month notice period; run-off provisions for existing advances; SEOGA data and scoring model remain SEOGA's IP |

### C. LAMINAR

| Dimension | Terms |
|-----------|-------|
| **Role** | Financial architecture + technology platform + capital raising |
| **Specific obligations** | Platform build and operations (lending ledger, advance engine, settlement, monitoring); bank partner management (API integration, control agreements, sweep orchestration); SPV administration and compliance; capital raising and investor relations; legal/structural maintenance |
| **Compensation — base** | 35% of ManCo spread residual (as ManCo equity owner). At €50M / 7% investor return: ~€660K/year. At €50M / 6%: ~€835K/year. |
| **Compensation — performance** | Up to 50% of the performance pool (metrics 3, 4, 5). At €50M / 7%, all targets met: ~€330K/year. At €50M / 6%: ~€418K/year. |
| **Total comp range** | At €50M / 7%: ~€990K/year (base €660K + perf €330K). At €50M / 6%: ~€1.25M/year (base €835K + perf €418K). |
| **Additional value** | ManCo equity (100%); reusable fintech infrastructure applicable to other verticals; GP position with optionality for future carry structure |
| **Skin in the game** | Platform development costs (~€200–400K to MVP); ManCo equity value tied to facility performance; performance-linked income on operational metrics; spread-share means Laminar's income is directly tied to their fundraising effectiveness — raise expensive capital, earn less |
| **Boundaries** | Laminar never touches money. Bank partner executes all payment flows. Laminar sends instructions, bank moves funds. |
| **Governance rights** | ManCo equity holder; GP of SPV; investment committee decisions (advance limits, concentration, new hospitals); approval on all structural changes |
| **Exit / termination** | ManCo equity transfers per shareholder agreement; SPV GP role transfers require investor consent |

---

## 7. Risk Allocation Matrix

| Risk | Who bears it | Why them | Mechanism |
|------|-------------|----------|-----------|
| **Credit risk** (insurer doesn't pay) | Investors (first); SEOGA (via performance fee reduction) | Investors deploy the capital; SEOGA's scoring determines haircuts | Haircuts sized by SEOGA model; SEOGA's scoring accuracy metric directly impacts their performance pay; higher losses compress net available → ManCo spread shrinks → everyone earns less |
| **Utilization risk** (capital sits idle) | SEOGA (via performance fee reduction); ManCo (lower spread on lower deployed base) | SEOGA controls distribution and hospital engagement | Utilization metric determines 25% of SEOGA's performance pay; lower deployed capital = lower absolute ManCo revenue (spread is on deployed, not committed) |
| **Operational risk** (platform failure, sweep errors) | Laminar (via performance fee reduction + reputational) | Laminar builds and operates the platform | Platform reliability metric determines 15% of Laminar's performance pay |
| **Capital cost risk** (raising expensive capital) | Laminar (via compressed spread); SEOGA (lower ManCo revenue) | Laminar controls fundraising and investor relations | Cost of capital improvement metric determines 20% of performance pool; raising at 8% instead of 6% cuts ManCo revenue by 75% — the single most impactful risk to operator economics |
| **Regulatory risk** (BdE licensing, AEPD enforcement) | Laminar (absorbs compliance costs); SPV (operational impact) | Laminar designed the structure; their responsibility to get it right | Legal costs borne by ManCo; Laminar bears reputational consequences |
| **Reputational risk** (hospital trust, SEOGA brand) | SEOGA | Their hospital network is the distribution channel | Damage to SEOGA's platform reputation directly reduces utilization → reduces everyone's income |
| **Structural risk** (Double Lockbox enforceability) | Investors (if security interest fails in insolvency) | This is the core investment risk | Mitigated by: notarized pledge, dual recourse, short duration, RDL 5/2005 qualification (TBD) |
| **Model risk** (SEOGA scoring misprices risk) | Investors (bear losses above haircut); SEOGA + Laminar (via compressed spread) | Joint risk — SEOGA builds the model, investors fund based on it, track record affects future cost of capital | Continuous back-testing; scoring accuracy metric; higher losses → higher hard costs → smaller spread for ManCo |

---

## 8. SEOGA Negotiation Positioning

### What to lead with

1. **Recurring revenue that grows on two axes.** Banks pay one-time referral fees. We pay a share of the operating margin that grows as (a) the facility scales (SEOGA drives utilization) and (b) the cost of capital drops (Laminar builds the track record that SEOGA's data makes possible). At €50M / 7% investor return: ~€800K/year. At €50M / 6% (post track record): ~€1M/year. At €200M / 6%: ~€4M+/year.

2. **No capital at risk.** Unlike a fintech equity offer, SEOGA never invests money. Their downside is limited to variable income — they always keep the base fee. And unlike equity, the income starts flowing from day one.

3. **Your quality widens the spread.** The better SEOGA's scoring, the lower the loss rate, the stronger the track record, the cheaper Laminar can raise capital, the wider the spread, the more everyone earns. SEOGA's data quality is literally the engine that drives the business margin. The performance metrics just formalize what's already true.

4. **Exclusive channel.** Cobra HOY can only be built on SEOGA's data. There's no plan B. SEOGA isn't a vendor — they're the co-creator of the product. The ManCo advisory seat reflects this.

### What to expect SEOGA will push on

| SEOGA concern | Likely response |
|---------------|----------------|
| "Why is my base only 25%?" | Because 35% goes to a performance pool where you earn up to half (metrics 1 + 2). Your total potential is 25% base + 17.5% from the pool = 42.5% of ManCo revenue. At €50M / 7%, that's ~€800K/year. And it grows — your 25% base on a 4.77% spread (€596K) is nearly double what 35% base on a 3.77% spread would be (€660K). The spread-share rewards patience. |
| "What if losses are high due to factors outside my scoring?" | The scoring accuracy metric measures prediction accuracy, not absolute loss level. If you predicted 0.40% and actual is 0.45%, you scored well. The metric rewards model accuracy, not zero losses. |
| "Banks don't ask me for performance targets." | Banks don't offer you €800K+/year recurring with built-in growth either. The performance structure is how we justify paying you 10×+ what a bank referral deal would pay. |
| "I want a higher base / lower performance share." | Negotiable within range. Floor: 30% base / 30% performance pool. Below 30% performance pool, the alignment signal to investors weakens — and that alignment signal is what allows us to raise cheaper capital, which grows your income. |
| "What about exclusivity?" | Reasonable to discuss. SEOGA providing data to a competing lending facility would undermine the entire structure. A non-compete for the healthcare lending vertical (not all of SEOGA's business) is fair. In exchange, we can offer MFN on terms. |
| "What if Laminar raises expensive capital and my income is low?" | Fair concern. That's why the cost-of-capital improvement metric exists in the performance pool — Laminar is penalized for raising expensive capital. And you can see the spread in the ManCo reporting. But fundamentally, this is a partnership: SEOGA builds the data moat, Laminar leverages it to raise capital. Both parties need the other to perform. |

---

## 9. Regulatory Notes on This Structure

| Element | Regulatory impact |
|---------|------------------|
| **Spread-share (no fixed management fee)** | The ManCo earns the residual operating margin of the SPV. This is functionally identical to a lending business where the operator keeps the spread between lending yield and cost of capital. No additional regulatory trigger vs. a fixed management fee — it's simply how the GP is compensated. Investors see a preferred return; the SPV's operating margin is an internal matter. |
| **Investor preferred return** | Structured as an LP preferred return in the SPV docs. Standard for SCSp/fund structures. Does NOT constitute a guaranteed return (which would require different regulatory treatment) — it is a priority in the distribution waterfall. If the SPV underperforms, the preferred return is not paid in full. |
| **ManCo as GP** | The GP must be registered as AIFM (light-touch) if SPV manages >€100M (or >€500M unleveraged with no redemption rights for 5 years). Below thresholds, registration only — not full authorization. |
| **Performance pool** | Internal ManCo compensation mechanism. Not an investor-facing performance fee or carried interest. Does not trigger any investor protection regulation. The KPI scorecard is a private arrangement between Laminar and SEOGA. |
| **SEOGA contractual participation** | Service agreement between ManCo and SEOGA. No securities issuance, no profit-sharing that would require CNMV registration. SEOGA receives a contractual share of ManCo revenue — this is vendor compensation, not equity participation. |
| **No fixed management fee = no AIFMD fee disclosure issue** | Under AIFMD, management fees must be disclosed to investors. Since the ManCo doesn't charge a fixed fee but rather earns the residual spread, the disclosure is simpler: "The ManCo earns the operating margin of the SPV after LP preferred distributions and hard costs." This is standard for lending-business SPVs. |

---

## 10. Phase 2 Considerations (On-Chain Migration)

The spread-share model maps naturally to on-chain architecture. When the facility migrates (within Spanish regulatory constraints per Ley 6/2023):

- **SPV participations** can be tokenized as loan participation tokens. Investors receive their preferred return automatically from pool cash flows via smart contract.
- **Spread residual** is computed on-chain: gross pool yield − hard costs − investor preferred distributions = ManCo allocation. No off-chain calculation needed.
- **Performance pool** metrics can be computed using oracle-verified data (SEOGA scoring data via zkTLS attestations, bank API transaction data, utilization rates from the lending ledger's on-chain event log).
- **SEOGA's and Laminar's compensation** auto-distributes quarterly via smart contract based on the KPI scorecard — base shares applied to the spread residual, performance pool allocated per metric scores.
- **Cost of capital becomes market-driven:** If LP tokens trade on secondary markets, the effective investor return is set by the market, not by bilateral negotiation. The spread widens or tightens based on demand. ManCo and SEOGA participate in this automatically — no renegotiation needed.

The ManCo structure provides a clean legal wrapper for the on-chain automation — the smart contract executes, but the ManCo is the legal entity with enforceable rights. The spread-share model is inherently more on-chain-compatible than a fixed management fee, because it's just arithmetic on observable cash flows.

---

## 11. Summary: Why This Structure Works for Everyone

| Party | What they get | What keeps them honest | How spread-share helps them |
|-------|-------------|----------------------|---------------------------|
| **Investors** | Negotiated preferred return (6–8%); 15–60 day cycles; institutional obligor quality; full transparency | Capital at risk; short duration means they can exit quickly if performance disappoints | Operators are structurally subordinate — investors get paid first. ManCo only earns if the business generates enough margin after paying investors. |
| **SEOGA** | ~€350K/year at €50M / 7% investor return, scaling to ~€563K at 6% (vs. €50–75K one-time from a bank); no capital at risk; advisory seat; scales with network | ~41% of total comp is performance-linked; scoring accuracy and utilization directly determine their income | SEOGA's income grows automatically as Laminar raises cheaper capital. Building track record through good scoring literally pays them more. |
| **Laminar** | ~€433K/year at €50M / 7%, scaling to ~€696K at 6%; ManCo equity; reusable infrastructure; GP position | Platform development costs already sunk; ManCo value tied to facility performance; operational metrics determine performance pay | Laminar is directly rewarded for raising cheaper capital — every bps of lower investor return becomes ManCo revenue. This is the core fundraising incentive. |

### The Alignment Test

The spread-share model creates **three reinforcing loops:**

1. **SEOGA → Track record → Cheaper capital → Wider spread → More SEOGA income.** SEOGA's scoring accuracy drives low losses. Low losses build investor confidence. Confident investors accept lower returns. Lower returns widen the spread. SEOGA earns more. SEOGA is rewarded for the very thing that makes cheaper capital possible.

2. **Laminar → Cheaper capital → Wider spread → More Laminar income.** Laminar raises at better terms. Better terms widen the spread. Laminar earns more. Laminar is directly rewarded for fundraising effectiveness — unlike a fixed management fee where Laminar earns the same regardless of what investors are paying.

3. **SEOGA utilization → More deployed capital → More absolute ManCo revenue → Everyone earns more.** SEOGA drives hospital adoption. Higher utilization means more capital deployed. More deployed capital means larger absolute ManCo revenue (even at the same spread percentage). Everyone benefits from SEOGA's distribution effectiveness.

**What breaks the loops:** Expensive capital (Laminar's fault) compresses the spread and hurts everyone. Poor scoring (SEOGA's fault) increases losses, damages the track record, and makes future capital more expensive — hurting everyone. Platform failures (Laminar's fault) drive hospitals away, reducing utilization — hurting everyone. Each party bears the consequences of their own underperformance AND the secondary effects on the other party's income.

No one can free-ride. Everyone compounds together.

---

*This framework is a working draft for internal Laminar discussion and SEOGA negotiation preparation. Numbers are illustrative and will be refined based on SEOGA feedback, investor feedback, and legal counsel input on ManCo/SPV structure.*
