# Cobra HOY — Architecture & Components Overview

**Project:** Cobra HOY (SEOGA x Laminar)
**Document type:** Internal — High-Level Architecture
**Author:** Laminar
**Date:** 2026-02-26
**Status:** v1.0

---

## 1. Executive Summary

SEOGA, a platform specializing in claims processing and reconciliation between private healthcare centers and insurance companies in Spain, has engaged Laminar to design and build a structured finance solution that enables hospitals to receive early payment on their expected insurer payouts.

The product — internally named **Cobra HOY** — is a secured lending facility where hospitals borrow against their expected insurer payment flows, with risk priced by SEOGA's granular data intelligence on medical activity, insurer payment behavior, and historical reconciliation.

**This is not invoice factoring.** Hospitals do not issue discrete invoices to insurers. They have conditional claims under framework agreements (convenios) with each insurer. These claims are subject to insurer validation, reconciliation, and batch payment cycles. The "receivable" is a statistically modeled expectation of payment — and SEOGA's data is what makes that expectation quantifiable and lendable.

### Structure Principles
- **SPV as single lending vehicle** — investors participate via an SPV that is the sole lender of record, pledgee, and control agreement counterparty. Hospitals deal with one entity; investors come and go behind it
- **SEOGA never touches money** — pure data, scoring, and insurer relationship layer
- **Laminar never touches money** — pure technology layer; bank partner executes all payment flows
- **Double Lockbox** — hospital opens a pledged account in its own name (insurer sees no change); SPV controls repayment via notarized pledge + tripartite control agreement with the bank

Laminar's role is threefold:
1. **Co-build the platform** — focus on reusable fintech infrastructure components
2. **Design the financial structure** — SPV, lending facility terms, risk framework, Double Lockbox mechanics
3. **Source capital** — identify and onboard investors into the SPV

SEOGA's role:
1. **Credit scoring** — build and maintain the risk model using their proprietary data
2. **Insurer liaison** — manage the insurer relationship, collections, and dispute resolution
3. **Origination UX** — surface the "Cobra HOY" experience within their existing platform

---

## 2. Context & Market Opportunity

### 2.1 SEOGA's Position
- Processes **~€75–80M/month** in healthcare activity between centers and insurers
- Deep historical data on:
  - Daily activity volume per center and insurer
  - Deviation between registered activity and effective settlement
  - Real collection timelines per insurer (15–60 days from batch registration to month-end close)
  - Recovery rates on disputed claims
  - Segmentation by specialty and historical behavior
- 1,000+ healthcare centers, 60+ insurers, 5M+ medical acts processed
- Core value: **structured intelligence on real cash flow behavior**, not just volume

### 2.2 The Problem
Hospitals perform medical acts for insured patients. Insurers owe them money under convenio terms, but payment cycles run 15–60 days. Hospitals need working capital. Traditional bank lending against these flows is:
- Expensive (generic risk pricing, no domain-specific intelligence)
- Slow (manual application, long approval cycles)
- Rigid (all-or-nothing credit lines, not granular to individual claims)

### 2.3 The Opportunity
SEOGA sits at the data layer between hospitals and insurers. They know — in near-real-time — exactly what medical activity has been performed, what has been submitted, what stage of insurer validation it's at, and the statistical likelihood and timing of payment. This data advantage enables:
- **Granular, per-claim risk pricing** based on actual insurer behavior
- **Automated advance offers** presented proactively to hospitals
- **A "one-click" experience** embedded in the platform hospitals already use daily

---

## 3. SEOGA Deep Dive — What They Do and Don't Do

> *Internal insight — based on Laminar research. Not shared with SEOGA.*

### 3.1 What SEOGA Does

SEOGA (Servicio de Optimización de la Gestión Asistencial) is a service platform — not a software product — that covers the **full administrative lifecycle** between healthcare centers and insurers:

| Function | Detail |
|----------|--------|
| **Unified claims portal** | Single interface replacing 60+ separate insurer portals for claim submission, authorization, and tracking |
| **Rules engine** | Applies each insurer's baremo, normas de valoración, session limits, and business rules at claim submission |
| **Reconciliation (conciliación)** | Decomposes insurer batch payments (liquidaciones) into claim-level settlements. Identifies billing discrepancies within 4 days of liquidation |
| **Payment incident management** | Detects collection incidents (incidencias de cobro), documents them, supports hospitals in filing claims |
| **Physician fee calculation (honorarios)** | Maps batch insurer settlements to individual medical acts and calculates per-physician payment based on actual settlement — not billed amounts |
| **Reporting & analytics** | Segmented reports by insurer, specialty, policy type. Daily-updated projected net collection amounts |
| **Smart scheduling** | Projects baremo compliance for planned appointments before scheduling |
| **Integration with HIS** | Direct integration with hospital information systems (Nubimed, igaleno, Common MS/SAP, others) |

### 3.2 What SEOGA Does NOT Do — and Why That's the Opportunity

**SEOGA does not handle money.** They never touch payment flows. Specifically:

- They do **not** process payments from insurers to hospitals
- They do **not** manage hospital bank accounts
- They do **not** disburse funds to physicians (they calculate the amounts; the hospital pays)
- They do **not** have payment initiation or collection capabilities
- They are **not** a licensed financial entity (not regulated by BdE or CNMV)

Money flows directly from **insurer → hospital bank account**. SEOGA operates entirely in the data and process layer. This gap — zero financial infrastructure — is exactly where Laminar, the bank partner, and the Double Lockbox structure (see Section 5) fit.

**What SEOGA does bring to Cobra HOY:**
- Claim-level reconciliation of batch insurer payments — exactly what the lending platform needs for matching and settlement
- Real settlement amounts vs. billed amounts — feeds directly into credit scoring accuracy
- Collection incident tracking and dispute resolution — essential for risk modeling
- Direct integration with hospital systems — Cobra HOY surfaces where admins already work
- Trusted relationships with 1,000+ healthcare centers — reduces adoption friction

---

## 4. The Nature of the Underlying Asset (What We're Actually Lending Against)

### 4.1 What a "Receivable" Actually Is

In the Spanish private health insurance system:

1. A patient holds a policy with an insurer (e.g., Sanitas, Adeslas, Mapfre)
2. The hospital has a **framework agreement (convenio)** with the insurer defining covered procedures, rates (baremo), and authorization requirements
3. The hospital performs a medical act and registers it on the insurer's portal for authorization
4. The insurer validates (or disputes) the act through its reconciliation process
5. The insurer pays the hospital per convenio terms — typically in **monthly batch cycles**, 15–60 days after the act

**Critical distinction:** The hospital's claim is **not** a formal invoice that can be cleanly assigned or sold. It is a **conditional claim under a framework agreement**, subject to:
- Insurer validation and reconciliation
- Batch payment processing
- Potential adjustments (normas de valoración, baremo deviations, session limits)
- Dispute or rejection

### 4.2 Why This Rules Out Traditional Factoring

Traditional invoice factoring requires:
- A discrete, identifiable invoice
- A clear obligor who has acknowledged the debt
- The ability to legally assign the receivable to a purchaser

Healthcare insurer claims under convenios don't cleanly satisfy these requirements. The claim is conditional until settlement, the amount can be adjusted, and assignment of convenio-based claims to third parties would introduce legal and operational complexity (insurer consent, notification requirements, disruption to existing payment flows).

### 4.3 What Makes It Lendable

What SEOGA provides is a **statistical prediction model**: given this hospital, this insurer, this type of medical act, and this stage in the claim lifecycle, there is an X% probability of receiving Y amount within Z days.

This model, built on millions of historical data points across 60+ insurers, transforms a conditional claim into a **quantifiable, risk-adjustable expected cash flow** — which is exactly what a secured lending facility can be built on.

---

## 5. Financial Structure — Secured Lending Facility

### 5.1 Structure Overview — The "Double Lockbox"

Spanish health insurers create a closed loop that prevents direct third-party interception of payment flows. Three constraints make this unavoidable:

| Constraint | Mechanism |
|------------|-----------|
| **Contractual block** | Convenios almost universally include anti-assignment clauses (*prohibición de cesión*). The insurer can refuse to pay any entity other than the hospital |
| **Compliance block** | Under Spanish AML and "Know Your Provider" rules, insurers will only pay into an IBAN where the account holder name matches the hospital's CIF. Third-party IBANs are rejected |
| **Operational block** | Changing bank details in an insurer's system is a slow, manual process — typically 30-day notice with physical signatures |

**The solution:** A "Double Lockbox" structure that mimics a direct payment to the hospital while giving the SPV legal control over the funds once they arrive.

> **Diagram:** See [Double Lockbox Structure](Shareables/diagram-01-double-lockbox.html) — interactive Mermaid diagram showing money flows, data flows, and legal relationships.

### 5.2 How the Double Lockbox Works

| Step | Component | Detail |
|------|-----------|--------|
| **A. Pledged Account** | Hospital opens a bank account **in its own name** at the partner bank | To the insurer, nothing has changed — they are still paying the hospital at a hospital-owned IBAN |
| **B. Security Interest (Prenda)** | Hospital grants a **Pledge over Credit Rights** (*prenda sobre derechos de crédito*) in favor of the **SPV** | Perfected by signing before a Spanish Notary (*póliza notarial*) — this is what makes it enforceable in bankruptcy. The SPV is the single, stable pledgee — investors change behind it without affecting the pledge |
| **C. Tripartite Control Agreement** | Signed between Hospital, **SPV**, and the Bank | Instructs the bank to execute automated daily sweeps: advance repayment → SPV's account, surplus → hospital's operating account. The bank ignores hospital instructions to redirect funds |

### 5.3 Why This Structure Works

| Principle | How It's Achieved |
|-----------|-------------------|
| **No receivable assignment needed** | This is a loan secured by a pledge over credit rights, not a purchase of receivables — anti-assignment clauses in convenios are not triggered |
| **Insurer sees no change** | The IBAN belongs to the hospital. The insurer's payment process is completely unchanged |
| **Enforceable in bankruptcy** | The notarized pledge (*póliza notarial*) ensures the security interest survives hospital insolvency under the *Ley Concursal* |
| **Fast-track enforcement** | The notarized document enables the *procedimiento ejecutivo* (fast-track judicial enforcement) under the *Ley de Enjuiciamiento Civil* |
| **No intermediary touches money** | SEOGA provides data; Laminar provides technology; money flows only between insurer → hospital's pledged account → SPV |
| **SPV absorbs investor complexity** | See Section 5.7 for full SPV rationale |

### 5.4 Bank Partner Requirements

> *Internal insight — preliminary research on bank partners for the Double Lockbox structure.*

The Double Lockbox requires a bank partner willing to:

1. Open accounts in the **hospital's name** (not virtual IBANs under a third-party EMI)
2. Sign **tripartite control agreements** (Hospital + SPV + Bank)
3. Execute **automated daily sweeps** via API (advance repayment → SPV, surplus → hospital operating account)
4. Provide real-time account monitoring via API/bank feeds (CAMT.053/054 or webhook)

**Bank partner profile:** Spanish commercial bank with API banking capabilities and willingness to support structured lending operations at scale. Candidates include banks with established fintech/BaaS units (e.g., Bankinter, CaixaBank, Sabadell) or specialist banks already serving the lending/factoring market.

> **Operational note:** Selecting **multiple bank partners** adds operational overhead (multiple API integrations, control agreement templates, reconciliation feeds) but eases **hospital onboarding** — hospitals may already bank with a partner. A single-bank strategy is simpler to operate but may require hospitals to open a new account.

### 5.5 Legal Constraints — Why Smart Contracts Alone Are Not Enough

Spanish law imposes "hard" constraints that require physical legal formalities alongside any technology layer:

| Constraint | Legal Basis | Impact |
|------------|-------------|--------|
| **Notary requirement** | *Ley Concursal* & Civil Code | A security interest is not enforceable in Spanish bankruptcy unless formalized before a Notary. Without this, a smart contract is an unsecured promise |
| **Enforcement** | *Ley de Enjuiciamiento Civil* | "Self-help" (taking the money directly) is legally risky. A notarized document is required to use fast-track judicial enforcement |
| **Account ownership** | Spanish AML Law | Banks will not allow a third party to have administrative control over a hospital-owned account without a formal, legally-binding Control Agreement |
| **Contractual privity** | *Principio de Relatividad* | The SPV is a third party to the hospital-insurer convenio. It cannot force the insurer to do anything — it can only intercept money once it hits the hospital's pledged account |

**The verdict:** Smart contracts and APIs serve as the **sweeper** (the engine that moves money 24/7 via bank API), but the **legal container** (the chassis that protects the SPV's rights in court) requires notarized instruments.

### 5.6 Scaling the Notarial Requirement — Umbrella Framework Pledge

A per-transaction visit to the notary is a non-starter at scale. The solution:

**1. Telematic Notary (Law 11/2023)**

As of late 2023 (fully operational in 2026), Spanish law allows online notarization via videoconference through the *Sede Electrónica del Notariado*. The hospital representative signs using a Qualified Electronic Signature (e.g., *Certificado de la FNMT*). This reduces a 4-hour errand to a ~5-minute video call — but even this is too much for hundreds of transactions per month.

**2. Umbrella Pledge over Future Credit Rights (the scalable solution)**

Instead of notarizing each advance, the hospital signs **one** comprehensive notarized Umbrella Pledge at onboarding:

| Element | Detail |
|---------|--------|
| **The Framework Agreement** | A single, notarized "Umbrella Pledge" (*prenda marco*) signed at the start of the lending relationship via telematic notary |
| **Future Credits Clause** | The pledge explicitly covers **all present and future receivables** arising from the specific list of convenios identified by SEOGA |
| **Reporting Annex** | Monthly (or per-advance), an updated annex (CSV or digital list of funded claims) is sent to the notary/bank to refresh the specific perimeter of the pledge. **This annex does not require a new video call or notarial appearance** — it is a clerical update to an existing public deed |

This moves the notarial bottleneck from a recurring friction point to a **one-time onboarding step**.

> *Internal note — In Phase 2 (blockchain/DLT), Spain's Securities Market Law (Ley 6/2023) allows representation of certain financial instruments via DLT. If the hospital's right to payment is tokenized, the pledge could potentially be perfected by registering the lien on the ledger rather than via notarial deed — converging the legal formality with the technology layer. Requires both hospital and bank to participate in a regulated DLT node (e.g., Iberpay). Research early for Phase 2 readiness.* Modelo ERIR

### 5.7 SPV as Lending Vehicle

The SPV is the **single lender of record** for all hospital relationships. It is not a securitization vehicle — it is a lending vehicle that aggregates investor capital and deploys it through the Double Lockbox structure.

**Why an SPV from day one:**

| Problem (without SPV) | Solution (with SPV) |
|------------------------|---------------------|
| Each investor must be named on every pledge — adding/removing investors requires re-notarizing | The SPV is the single, stable pledgee. Investors change behind it without touching any pledge |
| Tripartite control agreement involves every investor — each new investor requires amendment | The SPV is the single control agreement counterparty. Clean, stable |
| Bank must split sweep proceeds across multiple investors — complex bank-level logic | The SPV receives the full sweep. Allocation to investors happens at the SPV level |
| Hospital manages multiple creditor relationships | Hospital has ONE lending agreement with the SPV |
| Each investor independently needs AML/KYC infrastructure as lender of record | The SPV is the single obliged entity — runs KYB once, all investors benefit |
| Intercreditor disputes between direct lenders in hospital insolvency | The SPV is a single creditor with a single pledge. Clean enforcement |

**SPV structure:**

> **Diagram:** See [SPV as Lending Vehicle](Shareables/diagram-02-spv-structure.html) — hub-and-spoke diagram with roles grouped by category (legal, compliance, financial).

#### 5.7.1 SPV Domicile — Options & Trade-offs

The SPV domicile determines tax treatment, payment rail access, and investor familiarity. The capital strategy should keep both US and EU investor doors open.

| Dimension | Spain (S.L.) | Luxembourg (SCSp) | US (Delaware LLC) |
|-----------|-------------|-------------------|-------------------|
| **Familiar to US investors** | No | Yes — standard for EU private credit | Yes — native |
| **Familiar to EU investors** | Yes — native | Yes — standard | Workable |
| **SEPA access** | Native | Native | Needs EU bank account |
| **Tax transparency** | No — 25% corporate tax | Yes — pass-through | Yes — pass-through |
| **WHT on interest from Spain** | 0% (domestic) | 0% (EU Interest & Royalties Directive) | 0% (US-Spain treaty, Art. 11) |
| **Notarial compatibility** | Native | Apostille + PoA required | Apostille + PoA required |
| **Spanish AML compliance** | Native | Must build/outsource | Must build/outsource |
| **Bank partner comfort** | Most comfortable | Well-understood | Less familiar |
| **Setup cost** | ~€3K | ~€10–20K (SCSp + GP entity) | ~$2–5K + EU bank onboarding |
| **CRD6 risk (Jan 2027)** | N/A (domestic) | Exempt (fund) | Exempt (non-bank) |
| **Tokenization readiness (Phase 2)** | MiFID II / CNMV | Best EU jurisdiction for tokenized securities | US securities law complexity (Reg D/S) |

> **Caveat — SEPA access for non-EU SPV:** A US-domiciled SPV cannot directly access SEPA payment rails. It must open an EU bank account to send/receive SEPA transfers (disbursements to hospitals, receipt of sweep proceeds). SWIFT wire transfers (~€15–30 per transfer vs. <€1 for SEPA) are a non-starter for daily sweeps at scale. This adds an additional banking relationship and onboarding step.

> **Caveat — AML for non-Spanish SPV:** A Luxembourg or US SPV is the lender of record and therefore the AML obliged entity under Ley 10/2010. It must either build Spanish AML infrastructure or outsource to a Spanish compliance provider. SEPBLAC expects a designated compliance officer accessible in Spain.

> **Caveat — Notarial recognition for foreign SPV:** For a non-Spanish entity to be a valid party to a Spanish notarial deed (the Umbrella Pledge), it needs an apostilled certificate of good standing, power of attorney for the signatory, and potentially a Spanish fiscal representative (NIF). One-time setup, not recurring — but adds onboarding friction for the SPV itself.

**Recommended approach — the hybrid:**

A **Luxembourg SCSp** as the lending vehicle with a **Delaware LLC** as the GP/manager entity is the market-standard architecture for private credit funds targeting both US and EU capital:
- US investors invest in the SCSp via the familiar Delaware GP
- EU investors invest directly in the SCSp
- The SCSp has native SEPA access, tax transparency, and is the strongest jurisdiction for Phase 2 tokenization
- Setup cost: ~€10–20K (SCSp) + ~$2–5K (Delaware GP) = ~€15–25K total

> **Potential MVP simplification:** If the first investor is a single entity (e.g., one credit fund or family office willing to seed the pilot), a Spanish S.L. is the fastest path. Accept the 25% corporate tax at MVP scale. Upgrade to Luxembourg SCSp when scaling to multiple investors.

---

## 6. Solution Architecture — Components

### 6.1 Overview

> **Diagram:** See [Solution Architecture — Components](Shareables/diagram-03-solution-architecture.html) — three-tier overview (SEOGA → Financing Platform → Banking/Payment Rails) with component relationships.

---

### 6.2 Component Breakdown

> All components below are designed for vertical-agnostic reuse beyond healthcare.

#### A. Data Ingestion Layer
**Owner:** Laminar (build) · SEOGA (provide)

Normalizes three data streams into a single canonical data store:

| Source | What enters the pipeline |
|--------|-------------------------|
| **SEOGA** | Claims data (lifecycle stage, expected amounts, credit scores), reconciliation output (what insurers actually settled per claim), historical payment distributions per insurer |
| **Bank partner** | Pledged account transactions (incoming insurer payments, balances), sweep execution records, SPV account transactions |
| **Advance Engine** | Every advance decision and its parameters (claims covered, haircut, fee, concentration headroom) |

All data is validated on ingestion and quarantined if it fails critical checks. The architecture uses an adapter pattern per source — switching SEOGA from batch to API requires changing only the adapter. At POC scale, daily batch processing; at full scale, event-driven streaming.

> **Design constraint:** Data schema is abstracted for non-healthcare verticals. No patient-level PII enters the pipeline (hard GDPR constraint; see Section 8.2.D). Technical detail in *Data Pipeline for Ratings & Reporting*, Section 1.

---

#### B. Lending Ledger
**Owner:** Laminar

The **single source of truth** for the lending platform. Stores every entity and its full lifecycle:

| Entity | What it tracks |
|--------|---------------|
| **Claim** | A hospital's expected insurer payment — from registration through settlement, including predicted vs. actual amounts |
| **Advance** | A funded advance against one or more claims — from offer through settlement or exception. Carries pool assignment |
| **Hospital / Insurer** | Originator and obligor profiles, onboarding status, concentration limits, historical behavior |
| **Pledged Account** | The hospital's controlled bank account — status, balance, bank partner |
| **Sweep / Settlement** | Daily sweep operations and insurer batch payment decompositions linking payments to advances |

Every state transition is recorded as an immutable event with a cryptographic hash chain (foundation for audit trail and blockchain migration). The delta between SEOGA's predicted and actual settlement is tracked per claim — this feeds the model accuracy metrics that are the core POC deliverable.

> Technical detail: Full entity schemas and relationships in *Data Pipeline for Ratings & Reporting*, Section 2.

---

#### C. Advance Engine
**Owner:** Laminar (build) · SEOGA (scoring input)

| Aspect | Detail |
|--------|--------|
| Purpose | Determine advance offers for eligible claims |
| Inputs | Claim data + SEOGA credit score + pool rules + available facility capital |
| Logic | Apply haircut (from SEOGA model), check concentration limits, check available capital in relevant pool, generate offer |
| Output | Advance offer (amount, fee, expected settlement date) surfaced to hospital via SEOGA UX |

Two modes of operation:
1. **Proactive / automatic** — system generates and presents offers to hospitals based on configured rules (e.g., all validated claims with expected payment >30 days)
2. **On-demand** — hospital admin manually selects claims and requests an advance

Key considerations:
- Advance rate (haircut) is determined by SEOGA's credit scoring model
- Laminar enforces portfolio-level constraints: concentration limits, pool capacity, facility ceiling
- Must support dynamic haircuts — different rates per insurer, per hospital, per claim lifecycle stage, per historical behavior profile
- Offer presentation must clearly communicate: advance amount, fee/cost, expected settlement, total cost to the hospital

---

#### D. Pool Management Module
**Owner:** Laminar

| Aspect | Detail |
|--------|--------|
| Purpose | Group funded advances into a single pool for portfolio management, monitoring, and investor reporting |
| Pool logic | Advances grouped by criteria (insurer, hospital profile, maturity, claim lifecycle stage, etc.) for analytics and concentration management |
| Capital structure | Single pool, single risk profile — all investors participate *pari passu* in the same pool. No subordination, no waterfall, no differentiated loss absorption |

Key considerations:
- Pool composition rules must be configurable (this is part of what makes the product competitive)
- This module must produce real-time pool composition and performance data for the investor dashboard
- Concentration limits (by insurer, hospital, maturity) are enforced here alongside the Advance Engine
- **No tranching in MVP** — the pool is explicitly structured as a single-class exposure to avoid triggering the EU Securitization Regulation (Regulation 2017/2402), which applies when exposures are pooled and tranched into different risk classes (see Section 8.2.E)
- **Phase 2 extensibility:** The architecture supports adding tranche logic (senior/mezzanine/junior, waterfall distribution, subordination) if and when the structure is brought into compliance with the EU Securitization Regulation. This is a deliberate future capability, not an MVP feature

---

#### E. Disbursement & Settlement Module
**Owner:** Laminar

Orchestrates all money movement in the facility:

| Flow | Mechanism |
|------|-----------|
| **Disbursement** | SEPA credit transfer from SPV to hospital's operating account upon advance acceptance |
| **Collection** | Insurer pays into hospital's **pledged account** (in hospital's name — insurer sees no change); platform monitors via bank API |
| **Settlement** | Daily automated sweep: advance repayment → SPV's account, surplus → hospital's operating account |

SEPA integration required for disbursement (payment initiation + status tracking). The bank executes sweeps per the tripartite control agreement — Laminar's platform instructs, the bank moves the money. Reconciliation and sweep logic are covered in Section 6.2.F.

---

#### F. Pledged Account Monitoring & Reconciliation
**Owner:** Laminar

The **core enforcement mechanism** for the lending facility — the technology layer that operationalizes the tripartite control agreement. Three capabilities:

1. **Real-time visibility** into every pledged account — incoming insurer payments detected via bank API, balances monitored continuously
2. **Automated matching** of insurer batch payments to funded advances, using SEOGA's claim-level reconciliation output (SEOGA decomposes batch payments into individual settlements within 4 days — the pipeline consumes this rather than duplicating it)
3. **Sweep orchestration** — daily computation of advance repayment vs. surplus, with automated instruction to the bank

Exception handling covers partial payments, overpayments, unmatched payments, late payments, disputed claims, and sweep failures. If a multi-bank strategy is adopted, each bank implements a standardized adapter interface.

> Technical detail: Full reconciliation flow, matching criteria, and exception handling in *Data Pipeline for Ratings & Reporting*, Section 3.

---

#### G. Investor Dashboard & Reporting
**Owner:** Laminar

Real-time dashboard + monthly automated reports. Investors see pool composition and quality (quantified pool quality score from per-insurer and per-hospital ratings), cash flow visibility (disbursements, sweeps, projected collections), model accuracy (SEOGA prediction vs. reality), risk analytics (concentration, delinquency, stress scenarios), and a full audit trail for any individual advance.

SEOGA's data advantage — granular, per-claim risk intelligence from millions of historical data points — should be explicitly visible. This is what differentiates Cobra HOY from opaque traditional lending.

Role-based access (SPV manager vs. individual investor). Export in PDF/CSV/API. Configurable alerts for threshold breaches.

> Technical detail: Report structure, dashboard views, metric definitions, and POC vs. full scope differences in *Data Pipeline for Ratings & Reporting*, Section 5.

---

#### H. Verification, Audit Trail & Blockchain Layer
**Owner:** Laminar

Three capabilities, in order of implementation:

**1. Immutable audit trail (MVP)** — Every state transition is recorded as an immutable event with a cryptographic hash chain. Tampering breaks the chain and is detectable. Not a blockchain — an event-sourced data store designed for direct portability to one.

**2. Verifiable data inputs (Full scope — pre-blockchain)** — The pipeline's data comes from SEOGA and the bank partner. At scale, institutional investors need to verify data integrity without trusting any single party. **zkTLS attestations** (e.g., Opacity Network) cryptographically prove that SEOGA's claims data genuinely came from insurer portals and that bank transaction data genuinely came from the bank's API — giving investors an independently verifiable pipeline without requiring cooperation from insurers.

**3. Blockchain integration (Phase 2)** — Tokenized loan participations (on-chain tokens enabling fractional ownership and secondary trading), smart contract automation (waterfalls, tranche distribution), and full on-chain audit trail. Migration path is incremental: anchor Merkle roots on-chain → high-value events on-chain → full event log on-chain. Chain selection TBD (EVM-compatible L2 likely). CNMV regulatory assessment required before tokenization. Tokenized assets represent **loan participations**, not the underlying claims — important legal distinction.

> Technical detail: Event sourcing model, hash chains, zkTLS attestation architecture, and blockchain migration plan in *Data Pipeline for Ratings & Reporting*, Sections 6–7.

---

## 7. Capital Provider Perspective

### 7.1 What Are They Buying Into?

Capital providers invest in the **SPV**, which deploys their capital through a secured lending facility backed by expected healthcare insurer payment flows. The investment thesis:

- **Asset quality:** The underlying cash flows come from regulated Spanish insurance companies — institutional obligors, not SMEs or consumers
- **Short duration:** Claims settle in 15–60 days — very short-cycle capital deployment with rapid recycling
- **Data transparency:** SEOGA provides real-time, granular visibility into every underlying claim — something unavailable in traditional lending
- **Diversification:** Healthcare insurance payment flows are largely uncorrelated with traditional financial markets
- **Predictability:** SEOGA's historical data across millions of claims shows stable, modelable insurer payment behavior
- **Structural protection:** Dedicated collection accounts provide direct visibility and control over repayment flows

### 7.2 Counterparty & Exposure — Dual Recourse

**The hospital is the legal counterparty** — the SPV's lending agreement is with the hospital. Investors are not direct parties to any hospital relationship.

In the normal course, the investor's **economic exposure** is to the insurer's payment behavior — the insurer pays into the pledged account, the sweep repays the SPV, and the SPV distributes to investors. But this is only the primary repayment path, not the only one.

**The SPV has dual recourse:**

1. **Contractual claim against the hospital** — the advance is a B2B loan. The hospital's repayment obligation does not depend on whether the insurer pays. If an insurer defaults, delays, or adjusts a claim downward, the hospital still owes the SPV the full advance amount under the lending agreement. This is enforceable like any commercial debt.
2. **Notarized pledge over credit rights** (*prenda sobre derechos de crédito*) — grants the SPV *privilegio especial* (special privilege) under Article 270 TRLC, meaning priority over unsecured creditors in hospital insolvency. The pledge is perfected via notarized Umbrella Pledge at onboarding.

This dual-recourse structure is stronger than pure receivables factoring or purchase, where the buyer absorbs full insurer risk with no claim back against the hospital. Here, the investor is protected on both sides: insurer payment behavior is the expected source of repayment, and the hospital's unconditional obligation is the contractual backstop.

**The investor's risk materializes only when both layers fail simultaneously:**
1. The insurer doesn't pay (default/dispute) — mitigated by haircut + SEOGA data + diversification across 60+ insurers
2. **AND** the hospital cannot or will not repay from its own resources — mitigated by hospital diversification across multiple insurers, short advance duration (15–60 days), notarized pledge enforceable in bankruptcy, and concentration limits per hospital

### 7.3 Risk Framework

| Risk | Description | Mitigation |
|------|-------------|------------|
| **Insurer default/delay** | Insurer fails to pay or pays significantly late | Haircuts sized per insurer using SEOGA data; diversification across 60+ insurers; dual recourse — hospital remains fully liable (see 7.2). See Note 1 |
| **Claim adjustment** | Insurer settles at lower amount (baremo deviation, session limits) | SEOGA model accounts for historical deviation rates; haircut absorbs expected adjustments |
| **Claim dispute/rejection** | Insurer rejects the claim entirely | SEOGA detects disputes early via conciliation; haircut absorbs rejection risk; SEOGA manages resolution |
| **Concentration risk** | Overexposure to single hospital or insurer | Portfolio-level concentration limits enforced by Advance Engine |
| **Commingling / pledged account interference** | Hospital redirects funds or payments mix with operating funds | Double Lockbox segregation; tripartite control agreement; real-time bank API monitoring. See Note 2 |
| **Hospital insolvency** | Hospital enters *concurso de acreedores* | Notarized pledge grants *privilegio especial* (Art. 270 TRLC); short duration limits exposure; concentration limits; dual recourse (see 7.2). See Note 3 |
| **Fraud risk** | Fabricated or inflated medical acts | SEOGA validates activity against insurer records; direct visibility into insurer acknowledgments |
| **Model risk** | SEOGA's credit scoring model misprices risk | Continuous back-testing; five stress scenarios; haircuts include buffer above model predictions. See Note 4 |
| **Regulatory risk** | Changes in Spanish financial regulation | Legal counsel engaged; structure designed with regulatory flexibility; BdE/CNMV monitored |
| **Bank partner risk** | Bank unwilling to sign control agreements at scale, or changes API terms | Multi-bank strategy; standardized templates reduce negotiation friction |

> **Note 1 — Insurer credit quality:** Spanish health insurer insolvency is near-zero probability. DGSFP revoked ~12–15 authorizations in 2015–2025, nearly all small *mutualidades*. Major health insurers (Sanitas/Bupa, Adeslas/CaixaBank, Mapfre, DKV/Munich Re, ASISA) are heavily capitalized under Solvency II.

> **Note 2 — Control agreement enforceability:** Spanish law has no statutory "control agreement" framework (unlike the US DACA). Enforceability depends on contractual terms and bank operational compliance. Bank partner selection is a structural decision, not just an operational one.

> **Note 3 — Insolvency detail:** Enforcement stay of up to 1 year if pledged assets deemed necessary for operations; insolvency administrator can freeze sweeps. **Potential enhancement:** if pledge qualifies as financial collateral under RDL 5/2005, it is ring-fenced from the stay. **Clawback risk:** pledges created within 2 years of insolvency challengeable via *acciones rescisorias* (mitigated by arm's-length, fair-value safe harbor).

> **Note 4 — Model risk metrics:** MVP KPIs include amount accuracy, timing accuracy, prediction bias, per-insurer and per-claim-stage accuracy, and haircut adequacy. Five stress scenarios: insurer delay, settlement shock, hospital concentration, correlation, model failure. Technical detail in *Data Pipeline for Ratings & Reporting*, Section 4.

---

## 8. Regulatory Analysis — Bottlenecks, Costs, and Workarounds

> *Internal insight — preliminary regulatory assessment by Laminar. Not a substitute for formal legal counsel, but identifies the key gates and how to navigate them.*

### 8.1 Summary: What's Actually Required?

| Regulatory Area | Applies to MVP? | Who Registers? | Cost / Time | Avoidable? |
|----------------|-----------------|----------------|-------------|------------|
| **B2B lending license** | ❌ Not required | N/A | N/A | **B2B lending is unregulated in Spain** — no license needed |
| **Payment institution license** | ❌ Not if bank partner handles it | Bank partner | Already licensed | **Avoid by using a licensed bank partner** |
| **Notarial formalization** | ✅ Yes — one-time per hospital | Hospital + SPV + Notary | ~€500–1,000 per hospital (notary fees) | Cannot avoid — required for bankruptcy-proof security interest. Scaled via Umbrella Pledge + telematic notary. SPV is the stable pledgee |
| **AML/KYC** | ✅ Yes | The SPV (lender of record) | €10–30K setup + ongoing | Cannot avoid — mandatory |
| **GDPR / health data** | ✅ Yes | Laminar + SEOGA | €15–40K for DPIAs + DPO | Cannot avoid — **high scrutiny area** |
| **EU Securitization Reg** | ⚠️ Only if tranching | Originator/sponsor | Significant if triggered | **Avoid in MVP by using simple pool, no tranching** |
| **CNMV (securities)** | ⚠️ Only if tokenizing | Token issuer | 6–12 months + €100K+ | **Avoid in MVP — no blockchain** |
| **Consumer credit (CCD2)** | ❌ Not applicable | N/A | N/A | B2B only — not triggered |

**Bottom line for MVP: No financial license is required if the structure is designed correctly.** The two unavoidable requirements are AML/KYC and GDPR compliance.

---

### 8.2 Detailed Analysis by Regulatory Area

#### A. B2B Lending License — NOT REQUIRED ✅

**The single most important regulatory finding.** Under Spanish law, B2B commercial lending does not require a license from the Banco de España. The SPV can lend directly to hospitals without EFC registration (€5M capital, 6–12 months) or BdE authorization. The lending agreement is governed by general Spanish contract law.

**Caveat:** A formal legal opinion should confirm this for the specific structure. If the regulatory environment tightens, Spain's upcoming EFCAL (limited-activity financial credit establishments) could be a lighter-touch fallback.

---

#### B. Payment Institution License — AVOIDED VIA BANK PARTNER ✅

A PI license (€20K–€125K capital, 6–15 months) is avoided because the pledged accounts are **real bank accounts in the hospital's name**, opened and operated by a licensed **bank partner**. Laminar provides technology only; the bank executes all payment flows.

| Entity | Role | License Needed? |
|--------|------|----------------|
| **Bank partner** | Opens pledged accounts, executes daily sweeps, holds funds | Already licensed |
| **Laminar** | Technology platform — sends sweep instructions to bank via API | No — technology provider |
| **SEOGA** | Data and process layer — no money involvement | No |
| **SPV** | Lender of record — lends money, receives repayments via sweep | No (B2B lending, see 8.2.A) |
| **Investors** | Invest in SPV — not direct parties to hospital relationships | No |

**Key risk:** If Laminar is deemed to be "providing payment services" rather than "providing technology," a PI license could be required. Architecture must ensure Laminar sends instructions, bank executes — clean separation.

---

#### C. AML/KYC/KYB — MANDATORY, CANNOT AVOID ⚠️

Under Spanish Law 10/2010 and Real Decreto 304/2014, AML/KYC is required — but it is a **one-time onboarding event** per hospital, not a per-transaction requirement. Periodic refreshes are risk-based (every 3–5 years for low-risk entities). Hospitals in a regulated sector with transparent insurer payment flows are as low-risk as it gets.

**Architecture-relevant points:**

- The Double Lockbox creates dual KYC obligations (bank + SPV). In practice, the hospital submits documents once — bank and SPV share verification via Article 8 reliance (Ley 10/2010).
- SEOGA data pre-fills ~70% of the KYB form but cannot substitute for independent verification against official registries — SEOGA is not an obliged entity.
- If the SPV is non-Spanish (e.g., Luxembourg SCSp), it must still satisfy SEPBLAC's requirement for a designated compliance officer accessible in Spain — either build or outsource.

**Optimal KYB flow:** See Section 9.4 for the full onboarding diagram.

---

#### D. GDPR & Healthcare Data — MANDATORY, HIGH SCRUTINY ⚠️

**This is the most sensitive regulatory area.** SEOGA processes healthcare data (medical acts, patient-insurer interactions). Using this data for financial risk scoring is a change of purpose that requires careful legal grounding. The AEPD is aggressive — €35.5M in fines in 2024, with enforcement concentrated in finance/banking and health data profiling.

**The two hard questions (for legal counsel):**

1. **Purpose limitation** — SEOGA collects data to optimize claims processing. Using that data for financial credit scoring is a different purpose. Does aggregation at hospital/insurer/procedure level satisfy purpose limitation, or is a new legal basis required?

2. **Special category boundary** — At what level of aggregation does healthcare claims data cease to be "special category" under Article 9? If it persists, an Article 9(2) exception is needed.

**Architecture constraint:** The credit scoring model must NOT process individual patient data. It uses aggregated, anonymized data: "Hospital X + Insurer Y + Procedure Type Z = expected payment profile." No patient-level PII enters the lending platform. This is a hard design requirement, not a preference.

**Required actions:** Joint DPIA (SEOGA + Laminar) before any data flows begin. DPAs between SEOGA → Laminar → SPV. DPO appointment. These are standard deliverables from the legal engagement.

---

#### E. EU Securitization Regulation — AVOIDABLE IN MVP ✅

EU Regulation 2017/2402 applies when exposures are **pooled and tranched**. If triggered: 5% risk retention, loan-level data reporting, institutional investor due diligence requirements.

**How to avoid in MVP:** Don't tranche. A single SPV, single pool, single risk profile = no securitization trigger.

**Open question for counsel:** At what point do investors with different return terms (e.g., preferred returns for early investors) cross the line into de facto "tranching"? This matters for MVP investor structuring.

**When it becomes relevant:** Phase 2, when multiple tranches are introduced. An EC reform proposal (June 2025) for simplified private securitization disclosure may ease the path.

---

#### F. Convenio Restrictions on Pledging — UNKNOWN, MUST RESEARCH ⚠️

The framework agreements (convenios) between hospitals and insurers almost universally contain **anti-assignment clauses** (*prohibición de cesión*). The Double Lockbox structure is specifically designed to avoid triggering these clauses — there is no assignment of receivables, no change to the insurer's payment process, and the insurer is not a party to the pledge.

However, some convenios may contain broader restrictions that could affect the **pledge over credit rights** (*prenda sobre derechos de crédito*):
- Clauses that prohibit the hospital from creating any security interest over payment flows arising from the convenio
- Clauses that restrict the hospital from granting third-party rights over the commercial relationship

**This is not a regulatory issue — it's a contractual one.** The Double Lockbox eliminates the need for insurer consent or payment redirection, but the enforceability of the Umbrella Pledge could be challenged if a convenio explicitly prohibits pledging.

**Mitigant:** Even if pledging is restricted under some convenios, the tripartite control agreement with the bank still provides practical cash flow control. The legal question is whether the pledge is enforceable in hospital insolvency — if certain convenios prohibit it, those specific insurer payment flows may need to be excluded from the pledge perimeter or addressed with alternative protections (SEPA Direct Debit mandate as fallback).

**Who resolves this:** SEOGA has access to convenio terms for all 60+ insurers. This should be one of the first questions in the data room.

---

### 8.3 Estimated Regulatory Costs — MVP

| Item | Cost | Timeline | Who Pays |
|------|------|----------|----------|
| SPV formation (incl. GP entity if Luxembourg) | €15–25K | 1–2 months | Laminar |
| AML/KYC setup (SPV as obliged entity) | €10–30K | 1–2 months | SPV |
| GDPR DPIA + DPO + legal opinions | €15–40K | 2–3 months | Laminar + SEOGA (joint) |
| Bank partner onboarding + API integration | €5–15K setup + per-transaction fees | 1–2 months | Laminar |
| Legal drafting (Umbrella Pledge + Control Agreement templates) | €10–25K | 1–2 months | Laminar |
| Legal counsel (structure, contracts) | €20–50K | 2–3 months | Laminar |
| Convenio research / insurer due diligence | €0 (SEOGA internal) | 2–4 weeks | SEOGA |
| **Total MVP regulatory + legal + SPV cost** | **€75–185K** | **2–3 months critical path** | |

### 8.4 What Could Go Wrong — Regulatory Risks

| Scenario | Impact | Likelihood | Mitigation |
|----------|--------|-----------|------------|
| **BdE determines B2B lending requires licensing** | Must obtain EFC license (€5M capital, 6–12 months) OR restructure so the SPV is a licensed entity | Low — current law is clear, but regulatory trend is toward more oversight | Get a formal legal opinion before launch. If Luxembourg SCSp is used, confirm whether BdE requires a Spanish branch for ongoing lending activity |
| **AEPD challenges health data use for financial scoring** | Fines up to 4% of annual turnover. Cease and desist on data processing | Medium — AEPD is aggressive on health data + financial profiling | Anonymize/aggregate all data before it enters the lending platform. Conduct DPIA proactively. Never process patient-level PII |
| **Pooling (even without tranching) is deemed securitization** | Must comply with EU Securitization Reg — reporting, risk retention, disclosure | Low in MVP (single SPV, simple pool) — increases in Phase 2 | Keep MVP structure simple. Get legal opinion on pool-vs-securitization boundary |
| **Convenios prohibit pledging credit rights** | Umbrella Pledge structure may not be enforceable for affected convenios — must fall back to SEPA Direct Debit mandate | Medium — depends on individual convenio terms | Research early via SEOGA. Design the SEPA DD fallback from day one. Note: the Double Lockbox does NOT require insurer consent for payment redirection — only the pledge enforceability is at risk |

---

## 9. Hospital Onboarding — Minimizing Friction

### 9.1 Design Principle

Adoption is an MVP success metric. If onboarding is painful, hospitals won't use Cobra HOY regardless of how good the advance terms are. The onboarding flow must be designed for **minimal friction** — leveraging what SEOGA already has in place and structuring the legal requirements as one-time events.

### 9.2 What SEOGA Already Provides (Onboarding Advantage)

| Onboarding Step | Traditional Lending | With SEOGA |
|-----------------|-------------------|------------|
| **KYB / entity verification** | Manual document collection, legal entity checks, UBO research | SEOGA already has hospital identity, tax ID, legal structure, directors on file for 1,000+ centers — pre-fills ~70% of KYB form |
| **Claims data access** | Hospital must provide historical financial data, bank statements | SEOGA already has full claims history, settlement data, and reconciliation records — the data is already flowing |
| **Insurer relationship mapping** | Lender must understand which insurers, what volumes, what terms | SEOGA knows every insurer relationship, every convenio, every baremo rule, every historical payment pattern per hospital |
| **Integration with hospital systems** | Custom IT integration project | SEOGA is already integrated with the hospital's HIS (Nubimed, igaleno, SAP, etc.) — Cobra HOY is just a new feature in an existing platform |
| **Staff training** | New system, new login, new workflow | Hospital admins already use SEOGA daily — Cobra HOY appears as a button/module within their existing workspace |

### 9.3 Remaining Onboarding Steps (What's Still Needed)

Even with SEOGA's advantages, the Double Lockbox structure requires the following one-time steps per hospital:

| Step | Effort | Owner | Friction Level |
|------|--------|-------|---------------|
| **1. KYB document submission** | Hospital provides NIF, escritura de constitución, UBO declaration, director ID. SEOGA data pre-fills most fields | Hospital (provide docs) + SPV (verify) | **Low** — single document package, pre-filled from SEOGA. Bank and SPV consume the same verification via Art. 8 reliance agreement |
| **2. Pledged account opening** | Hospital opens a bank account **in its own name** at the partner bank | Bank partner (open) + Hospital (sign) | **Low–Medium** — standard account opening process. If multi-bank strategy, hospital may already bank with a partner |
| **3. Umbrella Pledge (Prenda Marco)** | Hospital signs a notarized umbrella pledge covering all present and future receivables from identified convenios | Hospital (sign) + Notary (formalize) + SPV (beneficiary) | **Medium** — one-time ~5-minute telematic notary video call via *Sede Electrónica del Notariado*. Hospital representative uses Qualified Electronic Signature (FNMT certificate) |
| **4. Tripartite Control Agreement** | Hospital, SPV, and Bank sign the control agreement governing the pledged account sweep mechanics | All three parties (sign) | **Low–Medium** — standardized template, signed alongside the account opening and pledge |
| **5. Lending agreement** | Hospital signs the facility agreement with the **SPV** | Laminar (prepare) + Hospital (sign) | **Medium** — legal review required, but standardized template. Can be combined with step 3 at the same notarial session |
| **6. Cobra HOY activation** | Enable the feature in the hospital's SEOGA interface | SEOGA | **Low** — configuration toggle |

**What is NOT required:** No payment routing updates, no insurer notifications, no BaaS virtual IBANs. The Double Lockbox means insurers see no change (see Section 5.1–5.3).

> **Cross-references:** KYB/KYC details (frequency, dual KYC, SEOGA data role) — see **Section 8.2.C**. Umbrella Pledge mechanics (telematic notary, future credits clause, digital annex) — see **Section 5.6**. Both are **one-time onboarding events** — not repeated per advance.

### 9.4 Onboarding Flow — Target Experience

> **Diagram:** See [Hospital Onboarding Flow](Shareables/diagram-04-hospital-onboarding.html) — step-by-step flow from SEOGA interest through parallel KYB/bank workstreams to go-live.

**Target onboarding time:** Days, not weeks. The critical path is the KYB verification (1–2 business days automated) and the telematic notary session scheduling (depends on notary availability). Everything else is automated or runs in parallel.

**Per-advance friction after onboarding:** Zero — one-click acceptance in SEOGA dashboard. No additional KYB, no notary, no paperwork.

---

## 10. Phased Approach

### Phase 1: MVP

**Goal:** Prove the model works with real capital across 5–10 pilot hospitals.

| Dimension | MVP Scope |
|-----------|-----------|
| Hospitals | 5–10 centers, all insurers SEOGA covers |
| Automation | Full: proactive offers + on-demand, automated disbursement and settlement via Double Lockbox |
| Structuring | Simple pool, no tranching (avoids EU Securitization Reg) |
| Capital | SPV funded by single investor or small syndicate (Spanish S.L. if single; Luxembourg SCSp when scaling) |
| Blockchain | None — architecture designed for future migration (event sourcing, immutable logs) |

**Timeline:** ~5–6 months from NDA to pilot ramp across five parallel workstreams (Legal/Structural, Banking, Data/Technology, Regulatory/Compliance, Capital). Three items must converge before the first hospital can onboard: platform MVP complete, bank partner API live, and legal templates finalized. Bank partner selection is the highest-risk critical path item — start immediately after NDA.

**Success metrics:** Hospital activation and advance take-up (adoption baselines); amount and timing accuracy (model validation — core POC deliverable); haircut adequacy and sweep execution (operational proof points for investors); onboarding time (<10 business days target).

> **Detailed timeline, workstream dependencies, convergence gate, and action items:** See *POC Scope & Action Plan* (`internal-memos/poc-scope-action-plan.md`).

### Phase 2: Full Vision

| Dimension | Vision |
|-----------|--------|
| Hospitals | All SEOGA-connected centers (~1,000+) |
| Structuring | Full pool & tranche structuring; multiple pools with different risk/return profiles |
| Capital | Multiple investors via SPV (Luxembourg SCSp recommended); potentially open market via tokenized SPV participations |
| Blockchain | Tokenized loan participations, smart contract waterfalls, on-chain audit trail |
| Secondary market | Tranche tokens tradeable between qualified investors |
| Physician advances | Early honorario payment via hospital channel — same Double Lockbox, higher yield (see Section 11.1) |
| Automation | Rules-based auto-advance (hospitals configure standing advance policies) |
| Reporting | Full investor dashboard + formal periodic reporting + regulatory reporting |

---

## 11. Product Extensions — Post-MVP

### 11.1 Physician Advance — Early Honorario Payment

> *Same infrastructure, new revenue stream.*

The Double Lockbox infrastructure can fund **early physician payment** — significantly improving yield without increasing credit risk, because the SPV's counterparty remains the hospital.

**Mechanism:**

> **Diagram:** See [Physician Advance — Early Honorario Payment](Shareables/diagram-05-physician-advance.html) — flow from SEOGA honorario calculation through SPV advance to sweep repayment.

**Why it works structurally:**

| Dimension | Impact |
|-----------|--------|
| **SPV counterparty** | Still the hospital — no direct lending to individuals |
| **Consumer credit regulation** | Not triggered — B2B facility, hospital is the borrower |
| **AML on individual doctors** | Not required — doctors are not parties to the lending relationship |
| **New legal vehicle** | None needed — same SPV, same Umbrella Pledge, same control agreement |
| **Credit risk** | Unchanged — the underlying obligor is still the insurer. The physician advance is just a different use of the same hospital advance |
| **Data infrastructure** | Already exists — SEOGA calculates individual physician honorarios today |

**Why doctors will pay more than hospitals:** A doctor paying a small fee to avoid waiting 60 days doesn't think in annualized terms — they think in absolute cost per advance.

**Strategic value:** Early physician payment becomes a **hospital recruitment and retention tool** for specialists. Hospitals are motivated to offer it. Physicians at multi-hospital practices create network effects ("why doesn't YOUR hospital offer this?").

**Distribution:** SEOGA does not have a physician-facing portal — doctors interact with the hospital, not SEOGA. The hospital is the distribution channel. The doctor may not even know the SPV exists — they just know their hospital now pays them faster. Physician opt-in (if required) can be handled via a lightweight mechanism in the hospital's HIS (Nubimed, igaleno), which is already integrated with SEOGA.

> **Open question — labor law:** Does the doctor need to individually consent to the fee deduction from their honorario, or can the hospital change its payroll policy unilaterally? This is a Spanish labor/employment law question (*Estatuto de los Trabajadores* for employed physicians; civil/commercial law for *autónomos* / independent contractors). Must research before launch. See Section 13, Q14.

---

## 12. Responsibilities Matrix

| Component | Laminar | SEOGA |
|-----------|---------|-------|
| Data ingestion pipeline | Build | Provide data/API |
| Lending ledger | Build & operate | — |
| Credit scoring model | — | Build & operate |
| Advance engine | Build & operate | Provide scoring input |
| Pool & tranche structuring | Build & operate | — |
| Disbursement & settlement | Build & operate | — |
| Pledged account monitoring & reconciliation | Build & operate | Provide reconciliation data |
| Investor dashboard | Build & operate | — |
| Hospital UX ("Cobra HOY") | API support | Build & integrate into platform |
| Blockchain layer | Build & operate | — |
| SPV formation + domicile selection | Lead | — |
| Financial structure design | Lead | Input & validation |
| Capital sourcing (into SPV) | Lead | Domain credibility support |
| Insurer collections & disputes | — | Own & operate |
| Regulatory/legal analysis | Co-lead | Co-lead |
| Hospital onboarding flow | Build | KYB data pre-fill, UX integration |
| Bank partner infrastructure | Build (select bank partner(s)) | — |
| Double Lockbox legal framework | Lead (draft templates) | — |
| Umbrella Pledge + Control Agreement templates | Lead (with legal counsel) | — |
| Telematic notary coordination | Build (scheduling, e-signature flow) | Support (hospital relationship) |

---

## 13. Open Questions & Next Steps

| # | Question | Owner | Priority | Dependency |
|---|----------|-------|----------|------------|
| ~~1~~ | ~~Can insurer payments be directed to a dedicated collection account?~~ | — | ~~RESOLVED~~ | **Answered: No — and they don't need to be.** The Double Lockbox structure (pledged account in hospital's name + tripartite control agreement) eliminates the need to redirect insurer payments |
| 2 | NDA execution between Laminar and SEOGA | Both | High | Blocks data access |
| 3 | Data room access — what data is available, in what format, at what granularity? | SEOGA | High | Blocks ingestion layer design and model validation |
| 4 | Integration model — API vs. batch vs. direct access? | Both | High | Blocks architecture decisions |
| 5 | SEOGA tech team capabilities — team size, stack, bandwidth for collaboration | SEOGA | Medium | Informs collaboration model and task allocation |
| 6 | Regulatory pre-assessment — does this lending structure require BdE licensing? | Laminar (legal) | High | Determines whether SPV requires BdE licensing |
| 7 | SPV domicile decision — Spanish S.L. for MVP simplicity vs. Luxembourg SCSp for multi-investor scale. Depends on first investor profile | Laminar | **High** | **Partially resolved:** analysis and recommendation in Section 5.7.1 (Luxembourg SCSp + Delaware GP recommended; Spanish S.L. as MVP fallback). Final decision blocked on investor profile (see Q7b) |
| 7b | Investor targeting — who is the target investor profile for the SPV? (US private credit, EU credit fund, family office, institutional?) | Laminar | High | Informs SPV domicile, structuring, terms, and pitch |
| 8 | Convenio restrictions — do hospital-insurer convenios contain clauses that restrict pledging credit rights (*prenda sobre derechos de crédito*)? | Laminar (legal) + SEOGA | High | If convenios prohibit pledging (not just assignment), the Umbrella Pledge structure may need adjustment |
| 9 | Bank partner selection — identify Spanish commercial bank(s) willing to offer API-controlled pledged accounts with tripartite control agreements at scale | Laminar | **High** | Critical path for MVP — determines account infrastructure and sweep mechanics |
| 10 | Umbrella Pledge legal drafting — engage fintech-focused Spanish law firm (e.g., Finreg 360, Garrigues, Uría) to draft the Umbrella Future Credits Pledge template and tripartite control agreement | Laminar (legal) | **High** | Must be ready before first hospital onboarding |
| 11 | Haircut methodology validation with historical data | SEOGA + Laminar | Medium | Requires data room access |
| 12 | Compensation framework — how is Laminar compensated for build + structuring? | Both | Medium | Blocks engagement formalization |
| 13 | Blockchain chain selection and tokenization legal framework (Phase 2 — includes DLT pledge perfection under Ley 6/2023) | Laminar | Low (post-MVP) | Should start research early |
| 14 | Physician advance — labor law analysis: can hospitals deduct an advance fee from physician honorarios without individual consent? Distinction between employed physicians (*Estatuto de los Trabajadores*) and independent contractors (*autónomos*). Determines whether opt-in mechanism is needed | Laminar (legal) | Medium (post-MVP) | Blocks physician advance feature design (Section 11.1) |
| 15 | **RDL 5/2005 financial collateral qualification** — can the pledge over the hospital's pledged bank account and/or credit rights be structured as financial collateral under Royal Decree-Law 5/2005? If yes, the SPV's security interest is ring-fenced from the automatic enforcement stay in hospital insolvency (*concurso*), and enforcement can proceed without judicial intervention. This is the single most impactful structural enhancement for downside protection | Laminar (legal) | **High** | Determines whether the SPV can enforce during *concurso* without waiting up to 1 year; affects bank partner structuring and control agreement design |
| 16 | **Insolvency clawback safe harbor** — confirm that the Umbrella Pledge, tripartite control agreement, and lending agreement qualify for the arm's-length / ordinary-course safe harbor under the TRLC's *acciones rescisorias* (2-year look-back). The pledge provides real economic value (working capital access) and is a standard commercial arrangement, but formal counsel confirmation is needed | Laminar (legal) | **High** | If the pledge is vulnerable to clawback, the enforceability of the entire Double Lockbox structure in hospital insolvency is weakened |

---

*This document is a living artifact. It will be updated as the NDA is signed, data is exchanged, and the analysis phase produces findings.*
