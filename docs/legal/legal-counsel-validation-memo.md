# Cobra HOY — Legal Counsel Validation Memo

**From:** Laminar (internal)
**To:** External legal counsel (TBD)
**Date:** 2026-02-18
**Status:** Draft — items for legal review and validation
**Reference documents:** Architecture & Components Overview v0.6; Investor Targeting Analysis; Return Model; Physician Advance Analysis; Capital Strategy Memo (incl. Negative Scenarios Analysis)

---

## Purpose

This memo identifies the assumptions and structural decisions in the Cobra HOY architecture that require formal legal counsel validation before MVP launch. We have deliberately excluded items that are settled law, routine procedural steps, or Phase 2 concerns. What remains are the genuinely open questions where the answer determines whether the structure works.

---

## 1. Lending License

**Architecture assumption:** B2B commercial lending in Spain does not require a BdE license.

**What we need counsel to confirm:**

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 1.1 | Does a non-bank SPV systematically and repeatedly lending to multiple Spanish hospitals through a rolling facility require any BdE licensing (EFC, EFCAL, or other)? The question is not whether a single bilateral loan is exempt — it's whether the recurring, programmatic nature of this activity changes the analysis | Must obtain license (€5M capital, 6–12 months) or restructure entirely |
| 1.2 | If the SPV is domiciled in Luxembourg (SCSp), does it require a Spanish branch or representative for ongoing lending activity in Spain? | Branch registration + local compliance infrastructure + SEPBLAC expectations for accessible compliance officer |
| 1.3 | How does Spain's upcoming EFCAL framework affect this analysis? Is pre-registration advisable? | Regulatory change mid-operation could force retroactive compliance |

---

## 2. Double Lockbox Structure — Enforceability

**Architecture assumption:** A pledge over credit rights (*prenda sobre derechos de crédito*) over future, conditional insurer payment flows, formalized via notarized deed, is enforceable in Spanish bankruptcy.

**What we need counsel to validate:**

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 2.1 | Is the *prenda sobre derechos de crédito* over expected (future) insurer payment flows enforceable under the *Ley Concursal* when the underlying claims are conditional — subject to insurer validation, adjustment, and batch reconciliation? | SPV's security interest may be classified as unsecured in hospital insolvency |
| 2.2 | Does the Umbrella Pledge (*prenda marco*) covering all present and future receivables from identified convenios satisfy the specificity requirements under Spanish pledge law? | Pledge could be challenged as too vague; may require per-claim identification |
| 2.3 | Is the tripartite control agreement (Hospital + SPV + Bank) enforceable against the hospital's insolvency administrator (*administración concursal*)? Can the administrator override the sweep instructions? Specifically: (a) can the administrator freeze or redirect pledged account funds unilaterally, and (b) does the bank's contractual obligation to execute sweeps survive the hospital's insolvency declaration, or must the bank obtain administrator/judicial authorization before continuing sweeps? | Insolvency administrator could freeze or redirect pledged account funds; bank may be legally prohibited from sweeping post-insolvency without authorization |
| 2.4 | Can the pledge annex (monthly CSV update of funded claims) be treated as a clerical update to the existing notarial deed, or does it require a new notarial appearance? | Per-advance notarial requirements would break the scalability model |
| 2.5 | **RDL 5/2005 financial collateral qualification:** Can the pledge over the hospital's pledged bank account and/or credit rights be structured as financial collateral under Royal Decree-Law 5/2005 (transposing the EU Financial Collateral Directive)? If yes, the SPV's security interest would be ring-fenced from the automatic enforcement stay in hospital insolvency, and enforcement could proceed without judicial intervention via direct appropriation or set-off. This is the single most impactful structural enhancement for investor downside protection | If the pledge does not qualify under RDL 5/2005, the SPV faces a potential enforcement stay of up to 1 year during hospital *concurso* if the pledged assets are deemed necessary for business continuity (*bienes necesarios*). During this period, the SPV cannot enforce and insurer payments accumulate in the pledged account under the insolvency administrator's control |
| 2.6 | **Clawback safe harbor:** Do the Umbrella Pledge, tripartite control agreement, and lending agreement qualify for the arm's-length / ordinary-course safe harbor under the TRLC's *acciones rescisorias* (Article 226 ff., 2-year look-back period)? The pledge provides real economic value to the hospital (working capital access), is at arm's length, and is a standard commercial arrangement — but we need formal confirmation that these characteristics are sufficient to survive a clawback challenge if the hospital enters *concurso* within 2 years of signing | If the pledge is vulnerable to clawback, the insolvency administrator could rescind the entire Double Lockbox structure — the pledge, the control agreement, and potentially individual advance transactions — leaving the SPV as an unsecured creditor in the hospital's insolvency |
| 2.7 | **Scope of special privilege on future receivables:** Under Article 270 TRLC, receivables arising from agreements executed *before* the insolvency declaration are automatically covered by the pledge as they arise (even post-insolvency). Confirm that receivables arising from convenios identified in the Umbrella Pledge — where the convenio pre-dates insolvency but individual claims arise post-insolvency — remain within scope of the *privilegio especial*. Does the distinction between the framework agreement (convenio) and the individual claim (medical act + insurer obligation) create any ambiguity? | If post-insolvency receivables from pre-existing convenios are excluded from the pledge, the SPV's special privilege is limited to receivables that had already crystallized before the insolvency declaration — significantly reducing recovery in a hospital insolvency scenario |

---

## 3. Convenio Restrictions — Anti-Assignment and Anti-Pledge

**Architecture assumption:** The Double Lockbox avoids triggering anti-assignment clauses because the structure is a secured loan, not a receivable assignment. No insurer consent is needed.

**What we need counsel to validate:**

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 3.1 | Does a pledge over credit rights arising from a convenio constitute an "assignment" or "encumbrance" caught by standard anti-assignment clauses in hospital-insurer convenios? | Insurers could challenge the pledge |
| 3.2 | Under Spanish law, is an anti-pledge clause in a convenio enforceable against a third-party pledgee (the SPV) who was not a party to the convenio? Or does the pledge still have effect between hospital and SPV, even if it constitutes a contractual breach by the hospital toward the insurer? | Determines whether this is a contractual risk (hospital bears it) or a structural risk (SPV bears it) |
| 3.3 | If the pledge is unenforceable for certain convenios, is a SEPA Direct Debit mandate from the hospital to the SPV a viable fallback security mechanism? | Need a secondary collection path for excluded convenios |

**Action required from SEOGA:** Provide sample convenio terms from the top 10 insurers by volume for legal review.

---

## 4. SPV Domicile — Tax

**Architecture assumption:** A Luxembourg SCSp provides tax transparency for Spanish-source lending income, with 0% WHT under the EU Interest & Royalties Directive.

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 4.1 | Confirm tax transparency (pass-through treatment) for a Luxembourg SCSp receiving Spanish-source lending income, and confirm 0% WHT on interest flows under the EU Interest & Royalties Directive | Unexpected tax leakage reduces investor returns and may require restructuring |

---

## 5. GDPR and Healthcare Data

**Architecture assumption:** SEOGA's claims data is aggregated and anonymized before entering the lending platform. No individual patient data flows to the SPV.

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 5.1 | Does the use of SEOGA's historical claims data (medical act types, insurer settlement patterns, deviation rates) for financial credit scoring constitute a change of purpose under GDPR Article 5(1)(b), even if aggregated at hospital/insurer/procedure level? | AEPD enforcement action; fines up to 4% of turnover; cease and desist |
| 5.2 | At what level of aggregation does healthcare claims data cease to be "special category" data under Article 9? Is hospital + insurer + procedure-type sufficient, or could re-identification be argued? | If special category status persists, processing requires an Article 9(2) exception |
| 5.3 | Physician advance extension (Model C): physician names, IBANs, and honorario amounts are personal data already processed by SEOGA. Does extending this to the advance use case require a new purpose limitation analysis or updated consent? | Could block the physician advance product if not properly grounded |

*Note: DPA drafting, controller/processor mapping, legal basis selection, and DPO assessment are expected as part of the DPIA deliverable, not as separate opinion items.*

---

## 6. Payment Services — Laminar's Role

**Architecture assumption:** Laminar is a technology provider, not a payment institution. The bank partner executes all payment flows.

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 6.1 | Does Laminar's role — sending sweep instructions to the bank via API, triggering disbursements, instructing payment allocation under a tripartite control agreement — constitute "providing payment services" under PSD2 / Ley 19/2018? | PI license required (€20K–€125K capital, 6–15 months) |

---

## 7. EU Securitization Regulation

**Architecture assumption:** MVP avoids the Securitization Regulation by using a single pool with no tranching.

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 7.1 | Confirm that a single SPV with a single pool of advances, without tranching, does not constitute a "securitization" under Article 2(1) of Regulation 2017/2402 | Compliance with risk retention, due diligence, transparency requirements |
| 7.2 | At what point does adding investors with different return terms within the same pool cross the line into "tranching"? (e.g., preferred return for early investors, waterfall priority) | Investor terms that resemble subordination could inadvertently trigger the regulation |

---

## 8. Physician Advance — Structure Risks

**Architecture assumption:** Under Model C (hospital-mediated), the SPV lends to the hospital, the hospital advances to the physician. No consumer credit regulation applies.

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 8.1 | Under Model C, could regulators "look through" the SPV-to-hospital loan and apply consumer credit rules (CCD2) because the funds explicitly finance physician advance payments? | Consumer credit licensing, disclosure, cooling-off periods |
| 8.2 | Can a hospital lawfully deduct a voluntary advance fee from a contracted physician's (*autónomo*) honorario? If the physician is an employee, does this trigger protections under the *Estatuto de los Trabajadores*? | Fee mechanism may be unlawful for certain physician relationships |
| 8.3 | Is the fee charged to the physician for early payment classified as interest (subject to *Ley de Represión de la Usura*) or a service fee? Annualized rates of 6–7% are in range — but is this analyzed as a personal transaction? | Usury challenge could invalidate the pricing model |

---

## 9. Dual Recourse — Hospital Repayment Obligation

**Architecture assumption:** The advance is a B2B loan from the SPV to the hospital. The hospital's repayment obligation is unconditional — it does not depend on whether the insurer pays. If an insurer defaults, delays, or adjusts a claim, the hospital remains fully liable to the SPV. This "dual recourse" (contractual claim against the hospital + notarized pledge with special privilege) is a core structural advantage over pure receivables factoring.

**What we need counsel to validate:**

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 9.1 | Confirm that the lending agreement can be drafted so that the hospital's repayment obligation is unconditional — i.e., the hospital cannot argue that repayment was implicitly conditional on the insurer paying the underlying claim. Are there any Spanish contract law doctrines (*rebus sic stantibus*, frustration of purpose, or similar) that a hospital could invoke to avoid repayment if its expected insurer revenue fails to materialize? | If the hospital can argue conditional repayment, the dual-recourse structure collapses — the SPV's claim against the hospital becomes contingent on insurer payment behavior, eliminating the structural advantage over factoring |
| 9.2 | In a scenario where the insurer defaults and the hospital is solvent but refuses to repay from its own resources: what is the most efficient enforcement path for the SPV's contractual claim? Is a *procedimiento monitorio* (payment order) available given the likely claim size (€50K–€500K per hospital), or does the SPV need to pursue ordinary civil proceedings? | Enforcement cost and timeline affect the practical value of dual recourse — if enforcement takes 18+ months, the contractual claim is theoretically strong but practically weak |
| 9.3 | Can the lending agreement include a *pacto de compensación* (set-off clause) allowing the SPV to apply any funds in the pledged account — including surplus from other insurer payments — against an unpaid advance from a defaulting insurer? Or would this be challenged as preferential treatment of one creditor over others? | If set-off is not available, the SPV must enforce its claim through slower judicial channels rather than directly applying available pledged account balances |

---

## 10. Telematic Notary

| # | Question | Risk if Wrong |
|---|----------|---------------|
| 10.1 | Is the Umbrella Pledge (*prenda marco*) over future credit rights eligible for telematic notarization under Law 11/2023, or is physical appearance required? | In-person notary visits would significantly slow hospital onboarding |

---

## 11. Recommended Engagement Scope

Based on the above, we recommend engaging a **Spanish fintech-focused law firm** with structured finance and data protection capabilities.

### Deliverables requested from counsel:

1. **Formal legal opinion** on B2B lending license exemption (Section 1)
2. **Structural opinion** on Double Lockbox enforceability in bankruptcy — including RDL 5/2005 financial collateral qualification, clawback safe harbor analysis, enforcement stay implications, and scope of special privilege on future receivables (Section 2)
3. **Convenio analysis** on anti-pledge restrictions (Section 3 — requires SEOGA sample convenio input)
4. **SPV domicile tax memo** (Section 4)
5. **GDPR/DPIA** including legal basis, controller/processor mapping, DPO assessment, and physician advance data analysis (Section 5)
6. **Payment services carve-out** confirmation (Section 6)
7. **Securitization boundary** opinion (Section 7)
8. **Physician advance** regulatory and employment law review (Section 8)
9. **Dual recourse opinion** — confirm unconditional hospital repayment obligation, enforcement path, and set-off availability (Section 9)
10. **Template drafting:** Umbrella Pledge (structured for RDL 5/2005 qualification if viable), tripartite control agreement (with explicit restrictions on hospital account access), lending agreement (with unconditional repayment and set-off clauses)

---

*This memo should be reviewed and updated as SEOGA provides convenio samples and data room access is established.*
