# Cobra HOY — Engineering Brief

**Joint team · Draft v0.2 · 2026-02-26**

---

## What Are We Building

SEOGA manages healthcare claims between ~1,000 Spanish hospitals and 60+ insurers. They process ~€75–80M/month and know — per claim — what was billed, what got paid, how long it took, and what got disputed.

Hospitals need working capital now. Insurers pay in 15–60 days. We're building a platform that lets hospitals borrow against those expected payments, priced by SEOGA's data.

**We build:** Data ingestion, advance decisioning, money movement orchestration, reconciliation, investor reporting.

**We don't build:** The credit scoring model (SEOGA), the hospital UX (SEOGA embeds it in their platform), or the legal/financial structure (Laminar finance + legal).

### The One Thing You Need to Know: How Money Flows

```
Hospital has expected insurer payments (claims)
         │
SPV (lending entity) advances money to hospital
         │
Insurer pays into a bank account in the HOSPITAL's name
  (insurer sees no change — same hospital, same IBAN)
         │
Bank sweeps that account daily:
  → repayment → SPV
  → surplus → hospital's operating account
```

We orchestrate the top half. We monitor the bottom half. The bank moves the money. We never touch it. SEOGA never touches it.

**Hard constraints:** No patient data enters the platform (GDPR). We instruct, bank executes (we're not a payment institution). Insurers don't know we exist.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│  SEOGA                                                        │
│  Credit scoring · Claims data feed · Hospital UX · KYB data   │
└──────────────────────┬───────────────────────────────────────┘
                       │  API boundary (format TBD)
                       ▼
┌──────────────────────────────────────────────────────────────┐
│  FINANCING PLATFORM (we build this)                           │
│                                                               │
│  A. Data Ingestion ──► B. Lending Ledger (source of truth)    │
│                              │                                │
│  C. Advance Engine ◄─────────┤                                │
│         │                    │                                │
│  D. Disbursement ◄───────────┤                                │
│  & Settlement                │                                │
│         │                    │                                │
│  F. Pledged Account ◄────────┤                                │
│  Monitoring & Recon          │                                │
│         │                    │                                │
│  E. Pool Mgmt ◄──────────────┤                                │
│  & Ratings                   │                                │
│         │                    │                                │
│  G. Investor Reporting ◄─────┘                                │
│                                                               │
│  H. Audit Trail (event sourcing + hash chains throughout)     │
└──────────────────────┬───────────────────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────────────────┐
│  BANK PARTNER                                                 │
│  SEPA transfers · Pledged accounts · Daily sweeps              │
└──────────────────────────────────────────────────────────────┘
```

| Component | One-liner |
|-----------|-----------|
| **A. Data Ingestion** | Normalize SEOGA, bank, and internal data into a canonical store. Adapter per source. |
| **B. Lending Ledger** | Source of truth: claims, advances, hospitals, insurers, accounts, sweeps, settlements |
| **C. Advance Engine** | Decide advance offers: claim data + SEOGA score + pool rules + available capital → offer |
| **D. Disbursement & Settlement** | SEPA transfers out (advances) and sweep orchestration in (repayment) |
| **E. Pool Management & Ratings** | Group advances, enforce concentration limits, compute quality scores, track model accuracy |
| **F. Pledged Account Monitoring** | Watch bank accounts, match insurer payments to advances, trigger sweeps |
| **G. Investor Reporting** | Dashboard + monthly reports. Pool performance, model accuracy, cash flows |
| **H. Audit Trail** | Event-sourced, hash-chained, append-only. Every state transition. Blockchain migration foundation |

---

## MVP Scope

**5–10 pilot hospitals. Single SPV, single pool, single investor class. Daily batch processing. Prove two things: hospitals will use it, and SEOGA's predictions are accurate.**

### In

- Full advance lifecycle: offer → accept → fund → settle
- Proactive + on-demand advance offers
- Automated disbursement and sweep orchestration
- Daily batch reconciliation
- Per-insurer quality scoring + pool metrics
- Model accuracy tracking (SEOGA predicted vs. actual)
- Lightweight investor dashboard + monthly PDF report
- Event-sourced audit trail with hash chains

### Out

| What | Why |
|------|-----|
| Blockchain / DLT | Phase 2. Architecture designed for migration, nothing on-chain in MVP |
| Tranching | Triggers EU Securitization Regulation |
| Physician advances | Post-MVP extension |
| Real-time streaming | Daily batch is fine for 5–10 hospitals |
| Multi-bank | Single bank partner. Adapter pattern for later |
| zkTLS / data provenance | Trust relationships sufficient for MVP investor |
| Polished investor portal | Functional, not pretty |

---

## Epics

### Epic 1+H: Data Model, Ledger & Audit Trail

Build together — the audit trail isn't a bolt-on, it's the storage pattern everything writes to.

**Done when:** Core entities (Claim, Advance, Hospital, Insurer, PledgedAccount, Sweep, Settlement) implemented with full lifecycle state machines. Every state transition writes to an append-only event store with per-entity hash chains (`SHA-256(event_id + entity_id + timestamp + payload + prev_hash)`). No UPDATE or DELETE on the event store.

Full entity schemas: [data-pipeline-ratings-reporting.md, Section 2](./data-pipeline-ratings-reporting.md). Event model: Section 7.

**Can start immediately.**

---

### Epic 2: Data Ingestion

Adapter-pattern ETL. Pluggable parser per source.

**Done when:** SEOGA adapter ingests claims, credit scores, and reconciliation data (batch CSV/JSON). Bank adapter ingests CAMT.053 statements. Internal adapter consumes advance engine events. Validation catches schema violations, referential breaks, duplicates, and lifecycle regressions (a claim can't go from "settled" back to "submitted"). Failed records quarantined. Runs daily.

Detail: [data-pipeline-ratings-reporting.md, Section 1](./data-pipeline-ratings-reporting.md).

**Can start with mock data.** Real SEOGA format and bank partner API are both TBD — the adapter pattern exists precisely for this. Build against synthetic data, swap adapters when real integrations land.

---

### Epic 3: Advance Engine

**Done when:** Takes claim data + SEOGA credit score → applies haircut → checks concentration limits (per-insurer, per-hospital) → checks pool capital → generates offer (amount, fee, expected settlement date, total cost). Two modes: proactive (auto-generate for eligible claims) and on-demand. Exposes API for SEOGA's UX to fetch and accept/decline offers. Every decision logged to audit trail with full parameters.

**Can start with mock credit scores.** Haircut methodology, concentration thresholds, and fee structure are business decisions — build the engine configurable, plug in real parameters later.

---

### Epic 4: Disbursement & Settlement

**Done when:** Initiates SEPA credit transfer (SPV → hospital) on advance acceptance. Tracks disbursement status. Computes daily sweep split (repayment vs. surplus per pledged account). Sends sweep instructions to bank. Confirms execution via next-day bank feed. Handles failures (alert + escalation).

**Blocked on bank partner.** Can design interfaces and state machines now, but cannot implement the actual SEPA integration or sweep instruction API until a bank is selected and their API spec is available. This is the critical path dependency for going live.

---

### Epic 5: Reconciliation

Match insurer payments to funded advances so we know what to sweep.

**Done when:** Daily batch ingests bank feed + SEOGA reconciliation output. Matches bank transactions to claim-level settlements using insurer identity, hospital/account, amount (within tolerance), and date proximity. Links settled claims to funded advances, computes repayment amounts. Exception handling for: partial payments, overpayments, unmatched payments, late payments, disputed claims, sweep failures. MVP: operator reviews exceptions daily via a queue/dashboard.

Detail: [data-pipeline-ratings-reporting.md, Section 3](./data-pipeline-ratings-reporting.md).

**Blocked on bank partner + SEOGA reconciliation schema.** Same situation as Epic 4 — design now, implement when integrations land.

---

### Epic 6: Pool Management & Ratings

**Done when:** Advances grouped into a pool with configurable composition rules. Concentration limits enforced (shared with Advance Engine). Per-insurer scoring on 3 dimensions: payment reliability (40%), timeliness (35%), dispute rate (25%) → weighted 1–5 scale. Pool quality score = exposure-weighted insurer average. Model accuracy tracked per settlement: amount accuracy, timing accuracy, prediction bias — segmented by insurer and claim stage. Haircut adequacy metric. Two stress scenarios: insurer delay (P95) and settlement shock (90%). All computed daily.

Detail: [data-pipeline-ratings-reporting.md, Section 4](./data-pipeline-ratings-reporting.md).

**Can start with synthetic data.** Initial scoring thresholds are proposed in the pipeline doc — treat as starting points, calibrate with real data.

---

### Epic 7: Investor Reporting

**Done when:** Web dashboard with daily views: portfolio overview, advance table (filterable), insurer view, cash flows, model accuracy. Monthly PDF report: pool summary, performance, accuracy, composition, flows, exceptions, stress results, quality score. CSV export. Simple auth.

Detail: [data-pipeline-ratings-reporting.md, Section 5](./data-pipeline-ratings-reporting.md).

**This doesn't need to be a product.** Functional and data-complete. Consider: does the MVP investor even need a dashboard, or is a well-structured monthly PDF + ad-hoc data pulls enough? Clarify before over-building.

---

## Phasing & Dependencies

### What drives the timeline

The platform has two external integration boundaries: **SEOGA** (data in) and **Bank** (money out). Neither is ready today.

```
Phase A — NOW                    Phase B — SEOGA lands        Phase C — Bank lands
─────────────────────            ──────────────────────       ─────────────────────
Epic 1+H: Data model + audit     Swap mock → real adapters    Epic 4: Disbursement
Epic 2: Ingestion (mock data)    Validate schemas             Epic 5: Reconciliation
Epic 3: Advance engine (mock)    Calibrate scoring            Epic 7: Real content
Epic 6: Pool mgmt (synthetic)    Model accuracy baseline
Epic 7: Dashboard framework
```

Phase A proves the architecture end-to-end with synthetic data. Phases B and C slot in as integrations become available. The risk: **if bank selection and SEOGA data discussions stall, engineering finishes Phase A and has nothing to build.** Front-load those conversations.

### External dependencies

| What | Blocks | Owner | Status |
|------|--------|-------|--------|
| NDA execution | All real data | Both | Pending |
| SEOGA data format + delivery (API? batch? schema?) | Real ingestion | SEOGA + Laminar | Unknown |
| SEOGA credit score structure | Real advance engine | SEOGA | Unknown |
| SEOGA reconciliation output schema | Real reconciliation | SEOGA | Unknown |
| SEOGA historical data for score seeding | Real ratings | SEOGA | Unknown |
| SEOGA team size, stack, bandwidth | Collaboration model | SEOGA | Unknown |
| Bank partner selection | Disbursement, reconciliation, go-live | Laminar finance | Not started |
| Bank partner API spec | All bank integration | Bank + Laminar | Blocked on selection |
| MVP investor requirements | Dashboard scope | Laminar | Unknown |

---

## Technical Decisions

Open for the engineering team. No decisions have been made.

| # | Decision | Considerations | Status |
|---|----------|---------------|--------|
| T1 | **Language(s)** | Python suggested for pipeline/ETL. API + dashboard framework TBD | Open |
| T2 | **Database** | PostgreSQL suggested for MVP (5–10 hospitals). TimescaleDB if time-series analytics needed later | Open |
| T3 | **API framework** | FastAPI / Django REST / Flask — for the API that SEOGA's UX calls | Open |
| T4 | **Batch orchestration** | Cron / Airflow / Celery / simple queue — MVP only needs daily batch | Open |
| T5 | **Deployment** | Cloud provider, containers, serverless? Data residency matters (EU healthcare-adjacent, GDPR) | Open |
| T6 | **CI/CD** | Pipeline tooling, test strategy | Open |
| T7 | **Auth** | Investor dashboard + internal tools. Simplest thing that works for MVP | Open |
| T8 | **Frontend** | Investor dashboard framework | Open |
| T9 | **Event store** | Append-only PostgreSQL table? EventStoreDB? Must support hash chains | Open |
| T10 | **Monitoring** | Ingestion health, pipeline failures, exception alerting | Open |
| T11 | **Data residency** | EU hosting likely required. Specific region TBD — legal should weigh in | Open |
| T12 | **SEOGA integration** | REST API / batch file drop / message queue — depends on their capabilities | Blocked |
| T13 | **Mock data strategy** | How to generate realistic synthetic data for Phase A | Open |

---

## Glossary

| Term | What it means |
|------|---------------|
| **Convenio** | Framework agreement between hospital and insurer — covered procedures, rates, payment terms |
| **Baremo** | Rate schedule within a convenio — what the insurer pays per medical act type |
| **Liquidación** | Insurer's batch payment. They don't pay per-claim; they aggregate into periodic lump sums |
| **Conciliación** | Reconciliation — decomposing a batch payment into claim-level settlements. SEOGA's core skill |
| **Prenda / Prenda marco** | Pledge / Umbrella pledge — security interest over credit rights, signed once at onboarding |
| **SPV** | Special Purpose Vehicle — the legal lending entity. Investors fund it; it lends to hospitals |
| **Double Lockbox** | Hospital-owned pledged account + tripartite control agreement + daily automated sweep |
| **Sweep** | Daily transfer: repayment → SPV, surplus → hospital operating account |
| **Haircut** | Discount on claim's expected value. €1,000 claim × 15% haircut = €850 advance |
| **CAMT.053 / .054** | ISO 20022 bank statement formats. .053 = end-of-day. .054 = real-time notifications |
| **SEPA** | Single Euro Payments Area — the EUR payment rail |
| **NIF** | Spanish tax ID |
| **KYB** | Know Your Business — entity verification for AML. One-time at hospital onboarding |
| **HIS** | Hospital Information System — their core admin software (Nubimed, igaleno, SAP) |
| **Honorarios** | Physician fees. Relevant post-MVP |
