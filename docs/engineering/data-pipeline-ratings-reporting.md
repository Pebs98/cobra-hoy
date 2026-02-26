# Data Pipeline for Ratings & Reporting — Technical Requirements

**Project:** Cobra HOY (SEOGA x Laminar)
**Document type:** Internal — Technical Requirements
**Author:** Laminar
**Date:** 2026-02-26
**Status:** Draft v0.1
**Parent document:** MASTER.obra-hoy-architecture.md

---

## 1. Data Ingestion

### 1.1 POC Scope

The POC pipeline ingests data from three sources in batch mode. The integration model with SEOGA is the primary dependency — until the API contract or batch file format is agreed, ingestion design is provisional.

#### Source: SEOGA

| Attribute | Detail |
|-----------|--------|
| **Data** | Claims data (hospital, insurer, procedure type, expected amount, claim lifecycle stage, submission date). Credit scores per claim or claim cohort. Reconciliation output (settled amount per claim, adjustments, rejections). Historical payment timing distributions per insurer |
| **Format** | Batch file (CSV/JSON) or API pull — TBD pending SEOGA integration discussions. POC can accept either |
| **Frequency** | Daily end-of-day batch. SEOGA publishes reconciliation output within 4 days of insurer liquidation; the pipeline ingests on the next daily cycle |
| **Validation on ingestion** | Schema validation (required fields, types, ranges). Referential integrity (every claim references a known hospital and insurer). Duplicate detection (claim ID uniqueness). Temporal consistency (claim lifecycle stages must advance forward — a claim cannot regress from "settled" to "submitted"). Flag but do not reject records that fail non-critical validation; quarantine records that fail critical validation (missing claim ID, unknown hospital) |

#### Source: Bank Partner

| Attribute | Detail |
|-----------|--------|
| **Data** | Pledged account transactions (incoming payments, balances). Sweep execution records (amounts transferred to SPV account, surplus released to hospital). SPV account transactions (sweep receipts, disbursement debits) |
| **Format** | CAMT.053 (end-of-day statement) or CAMT.054 (debit/credit notification). Alternatively, bank-specific API with JSON payloads |
| **Frequency** | End-of-day for POC. CAMT.053 provides a complete daily statement; CAMT.054 provides individual transaction notifications and may arrive intra-day but the pipeline processes them in the nightly batch |
| **Validation on ingestion** | Statement balance reconciliation (opening balance + transactions = closing balance). Transaction deduplication (CAMT message IDs). Currency validation (EUR only). Account reference validation (every transaction maps to a known pledged account in the registry) |

#### Source: Advance Engine

| Attribute | Detail |
|-----------|--------|
| **Data** | Every advance decision: claim(s) covered, hospital, insurer, advance amount, haircut applied, fee, expected settlement date, concentration limit headroom at time of decision, decision outcome (offered/accepted/funded/declined) |
| **Format** | Internal event stream or database records — Laminar-built system, schema fully under our control |
| **Frequency** | Event-driven (written at decision time), consumed by the pipeline in the daily batch |
| **Validation on ingestion** | Referential integrity (every advance references claims that exist in the lending ledger). Business rule validation (advance amount ≤ claim expected amount × (1 − haircut)). State consistency (an advance cannot be "funded" without a preceding "accepted" event) |

#### POC ingestion architecture

A straightforward batch ETL:

```
SEOGA batch file ──┐
                   │     ┌─────────────┐     ┌──────────────┐
Bank CAMT.053 ─────┼────►│  Ingestion   │────►│  Staging     │────► Canonical
                   │     │  Service     │     │  Tables      │      Data Store
Advance Engine ────┘     │  (validate,  │     │  (quarantine │
                         │   normalize) │     │   + clean)   │
                         └─────────────┘     └──────────────┘
```

**Technology recommendation:** Keep it simple for POC. A Python-based ETL orchestrated by a lightweight scheduler (cron, Airflow with a single DAG, or a simple task queue). PostgreSQL as the data store. No need for streaming infrastructure at this stage.

**Open dependency:** SEOGA's data format and delivery mechanism. The ingestion service should be designed with an adapter pattern — a pluggable parser per source — so that switching from batch CSV to API pull requires changing only the adapter, not the pipeline.

### 1.2 Full Scope

Full scope upgrades every batch path to streaming or near-real-time, and adds resilience for multi-bank, high-volume operation.

#### Source: SEOGA

| Change from POC | Detail |
|-----------------|--------|
| **Frequency** | Real-time event stream (webhook or message queue). SEOGA publishes claim lifecycle events as they occur — new claim registered, claim validated, reconciliation completed, dispute opened, dispute resolved |
| **Format** | Structured API (REST or gRPC) with versioned schema and backward-compatible evolution. Alternatively, a shared message bus (Kafka, RabbitMQ) if SEOGA is willing to publish events |
| **Volume** | SEOGA processes ~€75–80M/month across 1,000+ centers. Assuming average claim size of €200–500, this is 150K–400K claims/month, or 5K–13K claims/day. Event volume (including lifecycle updates) may be 3–5x claim count |
| **Validation** | Same as POC, plus: schema version checking, message ordering guarantees (per-claim ordering via partition key), dead-letter queue for unparseable messages, automated alerting on ingestion failures or anomalous volume (>2σ deviation from trailing 30-day average) |

#### Source: Bank Partner(s)

| Change from POC | Detail |
|-----------------|--------|
| **Frequency** | Webhook-driven (CAMT.054 real-time notifications) supplemented by CAMT.053 end-of-day reconciliation. The pipeline processes incoming payment notifications within minutes, enabling same-day sweep matching |
| **Multi-bank** | Abstraction layer per bank partner. Each bank integration implements a standard interface (incoming transaction feed, sweep instruction API, balance query). A bank adapter registry manages configuration per partner |
| **Volume** | Proportional to hospital count. At 1,000+ hospitals, expect 1,000+ pledged accounts, each receiving 1–10 insurer payments/month. Daily transaction volume: 1K–10K across all accounts |

#### Source: Advance Engine

| Change from POC | Detail |
|-----------------|--------|
| **Frequency** | Real-time event stream. Every advance decision, state transition, and exception is published as a domain event |
| **Volume** | Proportional to claims volume. If 20–40% of claims are advanced, expect 30K–160K advance lifecycle events/month |

#### Full scope ingestion architecture

```
SEOGA events ──────┐
                   │     ┌─────────────┐     ┌──────────────┐
Bank webhooks ─────┼────►│  Event       │────►│  Stream      │────► Canonical
                   │     │  Ingestion   │     │  Processing  │      Data Store
Advance Engine ────┘     │  Gateway     │     │  (enrich,    │
events                   │  (auth,      │     │   validate,  │
                         │   validate,  │     │   route)     │
                         │   buffer)    │     └──────────────┘
                         └─────────────┘
                               │
                               ▼
                         Dead Letter Queue
                         (failed messages)
```

**Technology recommendation:** Event-driven architecture using a message broker (Kafka or AWS Kinesis for ordered, partitioned event streams). Stream processing via a lightweight consumer framework (Kafka Streams, Flink, or even well-structured consumer services). PostgreSQL remains the canonical store (or upgrade to TimescaleDB for time-series analytics on payment timing data). The key architectural shift is from pull-based batch to push-based event processing.

**Open dependency:** Whether SEOGA can or will publish events in real-time, or whether the pipeline must poll SEOGA's API at high frequency. A polling adapter is a viable fallback but introduces latency.

---

## 2. Canonical Data Model

The canonical data model is the pipeline's source of truth. It is shared across POC and full scope — the schema is the same; only the ingestion frequency and processing mode change.

### 2.1 Core Entities

#### Claim

The atomic unit of the lending product. A claim represents a hospital's expected payment from an insurer for medical services performed.

| Field | Type | Description |
|-------|------|-------------|
| `claim_id` | UUID | Laminar-assigned unique identifier |
| `seoga_claim_ref` | String | SEOGA's external reference for the claim |
| `hospital_id` | FK → Hospital | Originating hospital |
| `insurer_id` | FK → Insurer | Paying insurer |
| `procedure_type` | Enum/String | Medical procedure category (not individual procedure — no patient-level data) |
| `submitted_amount` | Decimal (EUR) | Amount billed to the insurer |
| `expected_amount` | Decimal (EUR) | SEOGA's modeled expected settlement amount (after predicted adjustments) |
| `settled_amount` | Decimal (EUR), nullable | Actual amount paid by insurer (populated after reconciliation) |
| `claim_lifecycle_stage` | Enum | `registered` → `submitted` → `insurer_validated` → `in_liquidation` → `settled` → `closed`. Also: `disputed`, `rejected`, `adjusted` |
| `credit_score` | Composite | SEOGA's credit assessment: probability of payment, expected timing, confidence interval |
| `submission_date` | Date | When the claim was submitted to the insurer |
| `expected_settlement_date` | Date | SEOGA's predicted settlement date |
| `actual_settlement_date` | Date, nullable | When the insurer actually paid |
| `convenio_ref` | String | Reference to the hospital-insurer framework agreement |
| `created_at` | Timestamp | When the claim entered the pipeline |
| `updated_at` | Timestamp | Last modification |

**State transitions tracked:** Every lifecycle stage change is recorded as an immutable event (see Section 7, Audit Trail). The claim entity stores the current state; the event log stores the full history.

#### Advance

A funded advance against one or more claims.

| Field | Type | Description |
|-------|------|-------------|
| `advance_id` | UUID | Unique identifier |
| `hospital_id` | FK → Hospital | Borrowing hospital |
| `claims` | FK[] → Claim | Claims covered by this advance |
| `advance_amount` | Decimal (EUR) | Amount disbursed to hospital |
| `haircut_pct` | Decimal | Haircut applied (e.g., 0.15 = 15%) |
| `fee_amount` | Decimal (EUR) | Fee charged to hospital |
| `expected_settlement_date` | Date | When the advance is expected to settle (driven by underlying claim timing) |
| `actual_settlement_date` | Date, nullable | When the advance actually settled |
| `status` | Enum | `offered` → `accepted` → `funded` → `settling` → `settled`. Also: `declined`, `expired`, `defaulted`, `in_exception` |
| `disbursement_ref` | String | SEPA credit transfer reference for the disbursement |
| `concentration_headroom_at_decision` | JSON | Snapshot of concentration limits at time of advance decision (per-insurer, per-hospital) |
| `created_at` | Timestamp | |
| `updated_at` | Timestamp | |

#### Hospital

| Field | Type | Description |
|-------|------|-------------|
| `hospital_id` | UUID | |
| `seoga_hospital_ref` | String | SEOGA's identifier |
| `legal_name` | String | |
| `nif` | String | Spanish tax ID |
| `onboarding_status` | Enum | `prospective` → `kyb_submitted` → `kyb_verified` → `pledge_signed` → `active` → `suspended` |
| `pledged_account_id` | FK → PledgedAccount | |
| `umbrella_pledge_ref` | String | Notarial reference for the umbrella pledge |
| `facility_limit` | Decimal (EUR) | Maximum outstanding advance exposure for this hospital |
| `created_at` | Timestamp | |

#### Insurer

| Field | Type | Description |
|-------|------|-------------|
| `insurer_id` | UUID | |
| `name` | String | |
| `seoga_insurer_ref` | String | SEOGA's identifier |
| `payment_timing_profile` | JSON | Historical distribution of payment timing (e.g., P25/P50/P75/P95 days from submission to settlement) |
| `settlement_rate_profile` | JSON | Historical ratio of settled amount to submitted amount, by procedure type |
| `dispute_rate` | Decimal | Historical dispute/rejection rate |
| `concentration_limit` | Decimal (EUR) | Maximum pool exposure to this insurer |
| `solvency_rating` | String, nullable | External rating if available (e.g., Solvency II tier) |
| `updated_at` | Timestamp | |

#### Pledged Account

| Field | Type | Description |
|-------|------|-------------|
| `pledged_account_id` | UUID | |
| `hospital_id` | FK → Hospital | Account holder |
| `bank_partner_id` | FK → BankPartner | |
| `iban` | String | |
| `control_agreement_ref` | String | Tripartite control agreement reference |
| `status` | Enum | `pending_opening` → `active` → `frozen` → `closed` |
| `current_balance` | Decimal (EUR) | Last known balance |
| `balance_as_of` | Timestamp | |

#### Sweep

A daily sweep operation on a pledged account.

| Field | Type | Description |
|-------|------|-------------|
| `sweep_id` | UUID | |
| `pledged_account_id` | FK → PledgedAccount | |
| `sweep_date` | Date | |
| `total_incoming` | Decimal (EUR) | Total insurer payments received since last sweep |
| `repayment_amount` | Decimal (EUR) | Amount swept to SPV account (advance repayment) |
| `surplus_amount` | Decimal (EUR) | Amount released to hospital operating account |
| `sepa_refs` | String[] | SEPA transfer references for the sweep transactions |
| `status` | Enum | `pending` → `instructed` → `executed` → `confirmed`. Also: `failed`, `partial` |
| `matched_advances` | JSON | Which advances this sweep partially or fully settles, with amounts per advance |

#### Settlement

Links a specific insurer payment to one or more claims and advances.

| Field | Type | Description |
|-------|------|-------------|
| `settlement_id` | UUID | |
| `insurer_id` | FK → Insurer | |
| `hospital_id` | FK → Hospital | |
| `pledged_account_id` | FK → PledgedAccount | |
| `bank_transaction_ref` | String | Reference from CAMT.053/054 |
| `gross_amount` | Decimal (EUR) | Total batch payment amount from insurer |
| `settlement_date` | Date | Date payment was received |
| `claim_allocations` | JSON/FK[] → ClaimSettlement | SEOGA's decomposition: amount allocated per claim, adjustments, rejections |
| `reconciliation_status` | Enum | `received` → `decomposed` → `matched` → `swept`. Also: `partially_matched`, `unmatched` |
| `seoga_reconciliation_ref` | String | Reference to SEOGA's reconciliation output |

### 2.2 Entity Relationships

```
Hospital ──1:N──► Claim ◄──N:1── Insurer
    │                │
    │                └──N:M──► Advance
    │                              │
    └──1:1──► PledgedAccount       │
                   │               │
                   └──1:N──► Sweep ┘
                                │
Insurer ──1:N──► Settlement ◄───┘
                      │
                      └──► ClaimSettlement (SEOGA decomposition)
```

### 2.3 POC vs. Full Scope — Data Model Differences

The entity schema is identical. The differences are operational:

| Dimension | POC | Full Scope |
|-----------|-----|------------|
| **Hospital count** | 5–10 | 1,000+ |
| **Bank partners** | 1 | Multiple (BankPartner entity gains importance) |
| **Insurer payment timing profile** | Seeded from SEOGA historical data; updated manually | Continuously updated from observed pipeline data |
| **Concentration limits** | Simple per-insurer and per-hospital caps | Dynamic limits that adjust based on pool composition, insurer credit trends, and portfolio strategy |
| **Partitioning** | Single-partition PostgreSQL | Partitioned by hospital or date range for query performance |

---

## 3. Reconciliation Logic

The pipeline does not perform independent reconciliation. SEOGA reconciles insurer batch payments against individual claims — this is their core competency (Section 3.1 of master architecture). The pipeline consumes SEOGA's reconciliation output and uses it to match insurer payments to funded advances.

### 3.1 POC Scope — Daily Batch Reconciliation

The reconciliation flow runs once per day after both the bank feed and SEOGA reconciliation data are available.

#### Step 1: Ingest bank transaction feed

The nightly batch pulls the CAMT.053 statement for every active pledged account. Each incoming credit is recorded as a raw transaction:

```
Bank CAMT.053 → Incoming credit on pledged account
  → Transaction: {account, amount, date, payer_ref, bank_ref}
```

#### Step 2: Ingest SEOGA reconciliation output

SEOGA publishes their reconciliation within 4 days of the insurer's liquidation. The output decomposes the insurer's batch payment into claim-level settlements:

```
SEOGA Reconciliation → Per-claim settlement
  → [{claim_ref, submitted_amount, settled_amount, adjustment_reason, status}]
```

#### Step 3: Match bank transactions to SEOGA decomposition

The pipeline matches the bank-level payment (Step 1) to SEOGA's claim-level decomposition (Step 2) using:

- **Insurer identity** — the payer on the bank transaction maps to a known insurer
- **Hospital/account** — the pledged account maps to a specific hospital
- **Amount cross-check** — the sum of SEOGA's claim-level settlements for a given insurer + hospital + liquidation period should equal the bank transaction amount (within a tolerance for bank fees or rounding)
- **Date proximity** — the bank transaction date should fall within the expected window of the insurer's liquidation cycle

#### Step 4: Link to funded advances

Once claim-level settlements are matched, the pipeline identifies which settled claims have funded advances:

```
For each settled claim:
  IF claim has a funded advance with status = "funded" or "settling":
    → Mark advance as "settling" (or "settled" if fully covered)
    → Record the settlement amount against the advance
    → Calculate: repayment_due = min(advance_outstanding, settled_amount_allocated)
    → Remainder (if any) = surplus to hospital
```

#### Step 5: Compute sweep instruction

Aggregate all advance repayments for a given pledged account:

```
sweep_repayment = SUM(repayment_due for all matched advances on this account)
sweep_surplus = account_incoming - sweep_repayment
```

The sweep instruction is passed to the bank partner for execution. The bank executes per the tripartite control agreement — the pipeline instructs, the bank moves the money.

#### Step 6: Confirm sweep execution

The next day's bank feed confirms the sweep was executed. The pipeline records the confirmed sweep and updates advance statuses accordingly.

#### Exception handling (POC)

| Exception | Detection | Handling |
|-----------|-----------|----------|
| **Partial payment** | SEOGA reconciliation shows settled amount < expected amount for a claim | Record the shortfall. If the haircut absorbs it (settled ≥ advance amount), the advance still settles normally. If settled < advance amount, flag for manual review. The hospital's contractual obligation covers the shortfall (dual recourse) |
| **Overpayment** | Bank transaction amount > sum of SEOGA claim settlements for that insurer/hospital/period | Flag for manual investigation. May indicate a payment covering non-advanced claims (legitimate surplus) or a SEOGA reconciliation lag |
| **Unmatched bank payment** | Incoming credit on pledged account with no corresponding SEOGA reconciliation | Hold in "unmatched" queue. Common cause: SEOGA reconciliation lag (up to 4 days). Auto-retry matching on subsequent daily runs. Escalate if unmatched after 7 days |
| **Late payment** | Insurer pays after the expected settlement date | Track the delay. Update the insurer's payment timing profile. If the advance is past its expected settlement date, flag for monitoring but do not automatically default — late payment is common and modeled |
| **Disputed claim** | SEOGA flags a claim as disputed or rejected | If the claim has a funded advance, move the advance to `in_exception`. Track dispute resolution via SEOGA updates. If dispute resolves favorably, the advance settles normally. If the claim is rejected, the hospital owes the advance under dual recourse — escalate for collection |
| **Sweep failure** | Bank feed shows sweep instruction was not executed or was partial | Alert immediately. Investigate with bank partner. May indicate operational issue at the bank or a freeze on the pledged account |

**Manual verification in POC:** An operator reviews all exceptions daily. The pipeline flags and categorizes; a human decides. This is acceptable for 5–10 hospitals.

### 3.2 Full Scope — Real-Time Reconciliation

The reconciliation logic is identical; the execution model changes from daily batch to event-driven processing.

| Change from POC | Detail |
|-----------------|--------|
| **Bank transactions** | Processed on arrival (CAMT.054 webhooks), not in nightly batch. Incoming payment detected within minutes |
| **SEOGA reconciliation** | Consumed as events when published. A pending match is created when the bank payment arrives; the match completes when SEOGA's decomposition arrives (or vice versa) |
| **Sweep computation** | Computed continuously. Sweep instructions can be sent intra-day (subject to bank partner capabilities) or aggregated to daily |
| **Exception handling** | Automated escalation rules replace manual review for common cases. Configurable thresholds (e.g., auto-settle if shortfall < 2% of advance amount). Manual review only for genuinely ambiguous cases |
| **Multi-bank** | Reconciliation logic is bank-agnostic — the bank adapter normalizes all transaction data to the canonical format before it enters the matching engine |

**Full scope addition — predictive matching:** When a bank payment arrives before SEOGA's reconciliation, the pipeline can generate a *provisional match* using the insurer, hospital, amount, and date — then confirm or adjust when SEOGA's decomposition arrives. This reduces the effective reconciliation latency from 4 days to near-zero for the majority of payments.

---

## 4. Ratings Computation

### 4.1 POC Scope — Insurer-Weighted Quality Score + Model Accuracy

The POC rating methodology prioritizes two things: (1) a credible pool quality metric that a sophisticated investor can evaluate, and (2) rigorous tracking of whether SEOGA's predictions match reality.

#### 4.1.1 Per-Insurer Score

Each insurer is scored on three dimensions, all derived from SEOGA's historical data (seeded at launch) and updated as the pipeline observes actual outcomes:

| Dimension | Metric | Source | Weight (initial) |
|-----------|--------|--------|-------------------|
| **Payment reliability** | Historical settlement rate: `settled_amount / submitted_amount` over trailing 12 months | SEOGA historical data → updated by pipeline observations | 40% |
| **Payment timeliness** | Median days from claim submission to settlement, and P95 tail (worst-case timing) | SEOGA historical data → updated by pipeline observations | 35% |
| **Dispute rate** | Percentage of claims disputed or rejected over trailing 12 months | SEOGA historical data → updated by pipeline observations | 25% |

Each dimension produces a score on a 1–5 scale (5 = best). The per-insurer composite score is the weighted average.

**Scoring thresholds (initial — calibrate with data):**

| Score | Settlement Rate | Median Payment (days) | Dispute Rate |
|-------|----------------|----------------------|--------------|
| 5 | ≥ 98% | ≤ 20 | ≤ 1% |
| 4 | 95–98% | 20–30 | 1–3% |
| 3 | 90–95% | 30–45 | 3–5% |
| 2 | 80–90% | 45–60 | 5–10% |
| 1 | < 80% | > 60 | > 10% |

#### 4.1.2 Pool Quality Score

The pool-level quality score is the **exposure-weighted average** of per-insurer scores, where exposure = total outstanding advance amount per insurer:

```
Pool Quality Score = Σ (insurer_score_i × insurer_exposure_i) / Σ (insurer_exposure_i)
```

This is reported alongside:

| Metric | Description | Computation frequency |
|--------|-------------|----------------------|
| **Weighted Average Advance Rate** | Average haircut across all funded advances, weighted by advance amount | Daily |
| **Current Delinquency Rate** | Advances past expected settlement date / total outstanding advances (by amount) | Daily |
| **Concentration — Top 3 Insurers** | Combined exposure to the three largest insurer concentrations as % of total pool | Daily |
| **Concentration — Top 3 Hospitals** | Combined exposure to the three largest hospital concentrations as % of total pool | Daily |
| **Average Advance Duration** | Weighted average days from funding to settlement, for settled advances | Updated on each settlement |
| **Recovery Rate** | For advances that entered exception handling: amount ultimately recovered / original advance amount | Updated on each resolution |

#### 4.1.3 Model Accuracy Tracking

This is the POC's most critical analytical output. It answers: *Does SEOGA's prediction match what actually happens?*

For every settled advance, the pipeline computes:

| Metric | Formula |
|--------|---------|
| **Amount accuracy** | `settled_amount / expected_amount` — distribution, not just mean. Track P10, P25, P50, P75, P90 |
| **Timing accuracy** | `actual_settlement_days / expected_settlement_days` — same distribution tracking |
| **Prediction bias** | Is SEOGA's model systematically optimistic or pessimistic? `mean(settled - expected)` for amounts; `mean(actual_days - expected_days)` for timing |
| **Accuracy by insurer** | All of the above segmented per insurer — identifies which insurer models are accurate and which need recalibration |
| **Accuracy by claim stage** | Accuracy segmented by the claim lifecycle stage at which the advance was made — validates that earlier-stage claims (higher risk) are appropriately haircut |
| **Haircut adequacy** | For how many advances was the haircut sufficient? `count(settled_amount ≥ advance_amount) / count(all settled advances)` — this should be very close to 100% |

**Computation frequency:** Updated on every settlement event (daily in POC).

**Feedback loop:** Model accuracy metrics are shared with SEOGA monthly. The goal is a virtuous cycle: SEOGA improves their model, Laminar validates the improvement through the pipeline, haircuts are refined, better pricing is offered to hospitals.

#### 4.1.4 Stress Scenarios (POC — simplified)

Two basic stress tests, computed weekly:

| Scenario | Methodology |
|----------|-------------|
| **Insurer payment delay** | Assume all insurers pay at their P95 timing (worst 5% historical performance). Recompute expected cash flows. What is the maximum capital-at-risk (unfunded advances) at any point in the stress period? |
| **Settlement rate shock** | Assume all insurers settle at 90% of their historical settlement rate. Recompute expected losses. Are haircuts sufficient to absorb the shock? What is the residual loss after haircut absorption? |

### 4.2 Full Scope — Comprehensive Rating Methodology

Full scope extends the POC framework with per-hospital scoring, dynamic stress scenarios, and a structured internal rating scale.

#### 4.2.1 Per-Insurer Score (enhanced)

Same three dimensions as POC, plus:

| Additional dimension | Metric | Weight |
|---------------------|--------|--------|
| **Payment trend** | Is this insurer's settlement rate / timing improving or deteriorating over the last 6 months? Linear regression slope on monthly metrics | 10% (rebalance other weights) |
| **Solvency indicator** | External credit rating or Solvency II capital ratio, if available. For unrated insurers, SEOGA's proprietary assessment based on payment behavior stability | 10% |

Scores are recalculated daily from a rolling 12-month window of pipeline-observed data (replacing SEOGA's historical seed data over time).

#### 4.2.2 Per-Hospital Score

Hospitals are scored on dimensions that affect dual-recourse recovery:

| Dimension | Metric | Source |
|-----------|--------|--------|
| **Insurer diversification** | Herfindahl index of hospital's insurer mix (lower = more diversified) | SEOGA data |
| **Advance utilization** | Outstanding advances / facility limit — high utilization signals cash flow stress | Pipeline data |
| **Repayment history** | Percentage of advances settled on time, within tolerance | Pipeline data |
| **Exception rate** | Percentage of advances entering exception handling | Pipeline data |
| **Operating tenure** | Years of SEOGA relationship (proxy for operational stability) | SEOGA data |

#### 4.2.3 Pool Quality Score (enhanced)

The pool-level score becomes a composite of insurer-weighted and hospital-weighted components:

```
Pool Quality = 0.6 × Insurer-Weighted Score + 0.3 × Hospital-Weighted Score + 0.1 × Portfolio Structure Score
```

Where **Portfolio Structure Score** reflects diversification, concentration, duration profile, and vintage distribution.

Reported on a standardized scale (e.g., AAi through Ci, where the "i" suffix denotes "internal") with published mapping criteria. This is not a formal agency rating but uses a familiar scale to communicate credit quality.

#### 4.2.4 Stress Scenarios (full scope)

| Scenario | Methodology |
|----------|-------------|
| **Insurer downgrade** | Model the impact of one major insurer (top 3 by exposure) moving from its current score to score 2. Recompute pool quality, expected losses, and capital adequacy |
| **Sector-wide payment delay** | All insurers shift to P95 timing for 3 months. Model cash flow impact and capital requirements |
| **Hospital concentration event** | Largest hospital by exposure enters financial distress. Model loss given the dual-recourse structure: insurer payments still flow, but surplus sweeps stop. What is the SPV's maximum loss? |
| **Correlation stress** | Assume the top 3 insurers by exposure experience simultaneous settlement rate deterioration (−10pp). This tests whether diversification actually provides protection or whether insurer behavior is correlated |
| **Model failure** | SEOGA's prediction accuracy degrades by 20% (amounts and timing). Recompute haircut adequacy. At what accuracy threshold do haircuts become insufficient? |

**Computation frequency:** Weekly for standard stress scenarios. Ad hoc for scenario analysis requested by investors.

#### 4.2.5 Model Accuracy — Enhanced Tracking

All POC metrics are retained, plus:

| Addition | Detail |
|----------|--------|
| **Vintage analysis** | Model accuracy tracked per monthly cohort (vintage) of advances. Identifies whether the model is improving over time |
| **Segment deep dives** | Accuracy by procedure type, by claim lifecycle stage at advance, by hospital size tier, by insurer size tier |
| **Early warning indicators** | Automated alerts when accuracy metrics deviate >1σ from trailing 90-day averages, segmented by insurer and hospital |

---

## 5. Reporting Outputs

### 5.1 POC Scope

The POC audience is a single sophisticated investor (or small syndicate) close to the deal. Reporting can be less formal but must be data-rich.

#### Monthly Investor Report

Delivered as a structured document (PDF generated from data) within 5 business days of month-end.

| Section | Content |
|---------|---------|
| **Pool summary** | Total pool size, number of active advances, capital deployed, capital available |
| **Performance** | Settlement rate (actual vs. expected), average settlement time, delinquency rate, exception count and resolution |
| **Model accuracy** | SEOGA prediction vs. actual outcomes — the core POC deliverable. Segmented by insurer |
| **Pool composition** | Breakdown by insurer, by hospital, by average advance duration. Concentration metrics |
| **Cash flows** | Disbursements, sweep receipts, surplus released, net cash flow to SPV |
| **Exceptions** | List of advances in exception handling, status, expected resolution |
| **Stress test results** | Output of the two POC stress scenarios |
| **Pool quality score** | Current score with trend |

#### Investor Dashboard (POC — lightweight)

A web-based dashboard providing daily-updated visibility. Not a polished product — functional and data-complete.

| View | Content |
|------|---------|
| **Portfolio overview** | Total deployed, available capital, pool quality score, delinquency rate — single-screen summary |
| **Advance table** | Filterable/sortable list of all advances with status, amount, hospital, insurer, expected/actual settlement |
| **Insurer view** | Per-insurer exposure, score, payment timing distribution, settlement rate |
| **Cash flow** | Daily sweep activity, cumulative disbursements and receipts, projected near-term cash flows |
| **Model accuracy** | Charts showing predicted vs. actual (amount and timing), updated daily |

**Technology recommendation:** A simple web application (React or similar) backed by the canonical data store. Authentication via email/password or SSO. No mobile optimization needed for POC.

### 5.2 Full Scope

#### Monthly Investor Report (enhanced)

Same structure as POC, with additions:

| Additional section | Content |
|-------------------|---------|
| **Hospital-level performance** | Per-hospital metrics (anonymized if required by GDPR) |
| **Vintage analysis** | Performance by monthly cohort |
| **Rating migration** | Changes in per-insurer and per-hospital scores vs. prior month |
| **Stress scenarios** | Full suite of stress test results |
| **Model validation** | Statistical tests on SEOGA model accuracy (e.g., Brier score for probability predictions, calibration plots) |
| **Regulatory compliance** | Confirmation of concentration limits, facility limits, AML status |

**Delivery:** Automated generation and distribution via secure investor portal. Available in PDF and machine-readable format (JSON/CSV export).

#### Real-Time Investor Dashboard (full scope)

A fully designed, investor-grade portal.

| View | Content |
|------|---------|
| **Executive summary** | Single-screen: pool size, quality score, yield, delinquency, concentration — with sparkline trends |
| **Pool composition** | Interactive drill-down: by insurer, hospital, procedure type, duration bucket, vintage |
| **Cash flows** | Real-time sweep activity. Projected cash flows (next 7/14/30 days) based on SEOGA's expected settlement dates. Historical cash flow charts |
| **Risk analytics** | Concentration heatmaps. Insurer/hospital score distributions. Stress scenario results |
| **Model accuracy** | Real-time accuracy dashboard with statistical detail. Filterable by segment |
| **Advance lifecycle** | Full audit trail for any individual advance — from claim origination through settlement |
| **Alerts** | Configurable alerts (email/webhook) for threshold breaches (delinquency spike, concentration limit approach, model accuracy degradation) |
| **Export** | Download any view as PDF/CSV/Excel. API access for investors who want to pull data into their own systems |
| **Multi-investor** | Role-based access: SPV manager sees everything; individual investors see portfolio-level data and their allocation |

#### Operational Reporting (internal)

Not investor-facing, but critical for pipeline operation:

| Report | Audience | Frequency |
|--------|----------|-----------|
| **Reconciliation status** | Operations team | Daily |
| **Exception queue** | Operations team | Real-time |
| **Sweep execution** | Operations + Bank partner | Daily |
| **Ingestion health** | Engineering | Real-time (automated alerts) |
| **Data quality** | Engineering + Operations | Weekly |

---

## 6. Data Provenance & Verification (Full Scope Only)

### 6.1 The Problem

Cobra HOY's data pipeline depends on two external parties: SEOGA (claims and reconciliation data) and the bank partner (transaction data). An investor evaluating the pool must currently trust that:

- SEOGA's claims data genuinely reflects what was submitted to and settled by insurers
- The bank's transaction data genuinely reflects what was deposited and swept
- Neither party has tampered with or fabricated data

For a single sophisticated investor close to the deal, trust relationships and contractual representations may suffice. For institutional investors at scale, a cryptographic verification layer makes the data pipeline independently auditable — the investor (or their auditor) can verify data integrity without trusting any single party's assertion.

### 6.2 zkTLS Attestations — What They Enable

**zkTLS** (e.g., Opacity Network) allows a party to cryptographically prove that specific data was received from a specific HTTPS endpoint at a specific time — without revealing the full session or requiring cooperation from the server.

Applied to Cobra HOY:

| Attestation | Who attests | What is proved | Verification |
|-------------|-------------|----------------|--------------|
| **SEOGA claims data** | SEOGA (or an independent attestation node) | That the claims data SEOGA feeds into the pipeline genuinely came from insurer portals — i.e., the claim statuses, amounts, and reconciliation outputs are authentic insurer data, not fabricated by SEOGA | Investor can verify the TLS proof against the insurer portal's TLS certificate and the attested data fields |
| **SEOGA reconciliation** | SEOGA | That the reconciliation output (settled amounts per claim) matches what the insurer's portal or payment system reported | Same verification path |
| **Bank transaction data** | Bank partner (or the SPV, which has direct API access via the tripartite control agreement) | That the transaction data in the pipeline genuinely came from the bank's API — account balances, incoming payments, and sweep execution records are authentic | Investor can verify the TLS proof against the bank's API endpoint certificate |

### 6.3 Attestation Architecture

```
Insurer Portal ──TLS──► SEOGA ──attestation──► zkTLS Proof (claims data)
                                                    │
                                                    ▼
Bank API ──TLS──► SPV/Laminar ──attestation──► zkTLS Proof (transactions)
                                                    │
                                                    ▼
                                              ┌─────────────────┐
                                              │  Attestation     │
                                              │  Registry        │
                                              │  (append-only,   │
                                              │   hash-chained)  │
                                              └────────┬────────┘
                                                       │
                                                       ▼
                                              Investor Verification
                                              (independent tooling)
```

**Attestation frequency:** Per reconciliation cycle (for SEOGA data) and per bank statement (for transaction data). In practice, this means daily attestations.

**What is NOT attested:** The Advance Engine's internal decisions (haircuts, concentration checks) are not externally attestable via zkTLS because they are Laminar-internal computations with no external TLS endpoint. These are verified via the audit trail (Section 7) and open-source decision logic.

### 6.4 Bank Data — Special Consideration

The SPV already has direct, authenticated API access to the bank partner via the tripartite control agreement. This means:

- The SPV can independently verify bank data without relying on any intermediary
- A zkTLS attestation of bank API data by the SPV itself is technically valid but circular (the SPV attesting its own data source)
- The stronger model: the bank partner provides a digitally signed statement (e.g., signed CAMT.053) or supports an independent attestation node that produces zkTLS proofs of the bank's API responses

**Recommendation:** Require the bank partner to provide digitally signed statements as part of the bank selection criteria. This is simpler and more robust than zkTLS for bank data, since the bank is already a regulated, trusted counterparty.

### 6.5 Open Questions — Data Provenance

| Question | Impact |
|----------|--------|
| Can SEOGA technically produce zkTLS attestations from insurer portals? This requires SEOGA to run an attestation client alongside their portal-scraping infrastructure | Determines feasibility of the SEOGA attestation path |
| Which zkTLS protocol is suitable? TLSNotary requires a notary server; Reclaim Protocol uses different trust assumptions. Both are maturing but not yet production-standard at scale | Technology selection |
| What is the attestation latency and computational cost? If producing a proof takes minutes per session, it may not scale to thousands of reconciliation events per day | Architecture design |
| Can the bank partner provide signed statements (CAMT.053 with digital signature)? Some banks already support this; others do not | Determines whether zkTLS is needed for bank data or if signed statements suffice |

---

## 7. Audit Trail

### 7.1 Design Principles

The audit trail must support three use cases:

1. **Investor transparency** — any advance can be traced from origination through settlement with a complete, verifiable record
2. **Regulatory compliance** — AML record-keeping, data processing logs, dispute documentation
3. **Future blockchain migration** — the audit trail architecture should be directly portable to an on-chain event log without redesign

These principles apply equally to POC and full scope. The POC builds the audit trail from day one; full scope adds cryptographic integrity and higher-assurance storage.

### 7.2 Event Sourcing Model

Every state change in the pipeline is recorded as an immutable event. The current state of any entity (claim, advance, sweep, etc.) can be reconstructed by replaying its event history.

#### Event structure

| Field | Type | Description |
|-------|------|-------------|
| `event_id` | UUID | Globally unique event identifier |
| `event_type` | String | e.g., `claim.lifecycle_changed`, `advance.funded`, `sweep.executed`, `settlement.matched` |
| `entity_type` | String | `claim`, `advance`, `hospital`, `pledged_account`, `sweep`, `settlement` |
| `entity_id` | UUID | The entity this event applies to |
| `timestamp` | Timestamp (UTC) | When the event occurred |
| `actor` | String | Who/what caused the event: `seoga_ingestion`, `bank_feed`, `advance_engine`, `operator:<name>`, `system:reconciliation` |
| `payload` | JSON | Event-specific data. For state changes, includes both `previous_state` and `new_state` |
| `source_ref` | String, nullable | External reference (e.g., SEOGA message ID, CAMT statement ID, SEPA transfer reference) |
| `metadata` | JSON | Additional context: pipeline version, ingestion batch ID, correlation IDs |

#### What is logged

| Category | Events |
|----------|--------|
| **Claim lifecycle** | `claim.created`, `claim.lifecycle_changed` (with from/to states), `claim.credit_score_updated`, `claim.settled`, `claim.disputed`, `claim.rejected` |
| **Advance lifecycle** | `advance.offered`, `advance.accepted`, `advance.funded`, `advance.settling`, `advance.settled`, `advance.defaulted`, `advance.exception_entered`, `advance.exception_resolved` |
| **Reconciliation** | `settlement.received` (bank payment detected), `settlement.decomposed` (SEOGA reconciliation consumed), `settlement.matched` (linked to advances), `settlement.exception` (mismatch detected) |
| **Sweep** | `sweep.instructed`, `sweep.executed`, `sweep.confirmed`, `sweep.failed` |
| **Pool/ratings** | `pool.quality_score_computed`, `insurer.score_updated`, `stress_test.completed` |
| **Ingestion** | `ingestion.batch_started`, `ingestion.batch_completed`, `ingestion.record_quarantined`, `ingestion.validation_failed` |
| **Manual actions** | `operator.exception_reviewed`, `operator.advance_manually_settled`, `operator.override` (with justification field) |

### 7.3 Immutability & Integrity

#### POC Scope

- Events are written to an **append-only table** in PostgreSQL. No `UPDATE` or `DELETE` operations are permitted on the event store (enforced by application-level controls and database triggers)
- Each event includes a **hash of the previous event** for the same entity, forming a per-entity hash chain:

```
event.integrity_hash = SHA-256(event_id + entity_id + timestamp + payload + previous_event_hash)
```

- This makes any tampering with historical events detectable — breaking one hash breaks the chain
- The event store is backed up daily. Backups are immutable (write-once storage)

#### Full Scope

- **Periodic anchoring:** At a configurable interval (e.g., hourly or daily), the pipeline computes a Merkle root of all events in the period and publishes it to an external, tamper-evident store. Options:
  - Public blockchain (Ethereum, or an L2 for cost efficiency) — strongest guarantee, highest cost
  - A transparency log service (similar to Certificate Transparency)
  - A consortium ledger shared with the bank partner and/or investor auditor
- **Database-level immutability:** Move from PostgreSQL with application-level controls to a database with native immutability (e.g., Amazon QLDB, or PostgreSQL with append-only partitioning and restricted access controls)
- **zkTLS attestations** (Section 6) are themselves stored as events in the audit trail, linking each data ingestion batch to its cryptographic proof

### 7.4 Blockchain Migration Path

The event sourcing model is designed for direct portability to a blockchain event log:

| Current (off-chain) | Future (on-chain) |
|---------------------|-------------------|
| Event store (PostgreSQL) | Smart contract event log |
| Per-entity hash chain | On-chain transaction hash |
| Periodic Merkle root anchoring | Every event is natively on-chain |
| Application-level immutability | Protocol-level immutability |
| Centralized event store | Distributed ledger |

The migration can be **incremental**: start by anchoring Merkle roots on-chain (Phase 2a), then move high-value events on-chain (Phase 2b), then full event log on-chain (Phase 2c). The event schema does not change — only the storage layer.

### 7.5 Retention & Access

| Dimension | POC | Full Scope |
|-----------|-----|------------|
| **Retention** | Indefinite (small data volume) | Minimum 10 years for AML compliance. Tiered storage (hot: 1 year, warm: 3 years, cold: 10+ years) |
| **Access** | Application-level read access for operators and investor dashboard | Role-based access. Investor auditors get read access to event chains for advances in their allocation. Full event store access restricted to compliance and engineering |
| **Export** | On-demand CSV/JSON export for any entity's event chain | Automated export in regulatory-compliant formats. API access for investor auditor systems |

---

## Summary of Open Dependencies

| # | Dependency | Blocks | Owner |
|---|-----------|--------|-------|
| 1 | SEOGA data format and delivery mechanism (batch file format, API specification, or message queue protocol) | Ingestion adapter design, data model field mapping, reconciliation matching logic | SEOGA + Laminar |
| 2 | SEOGA reconciliation output schema (how claim-level settlements are structured, what fields are provided, how adjustments are categorized) | Reconciliation logic, settlement entity design | SEOGA |
| 3 | Bank partner API specification (CAMT.053/054 support, webhook capabilities, sweep instruction API, signed statement support) | Bank ingestion adapter, sweep instruction module, data provenance strategy | Bank partner + Laminar |
| 4 | SEOGA credit score format (composite structure, confidence intervals, update frequency, versioning) | Credit score storage, ratings computation, model accuracy tracking | SEOGA |
| 5 | SEOGA historical data availability for model seeding (insurer payment timing distributions, settlement rates, dispute rates — at what granularity and for what time period) | Initial insurer scoring, stress scenario calibration, haircut methodology validation | SEOGA |
| 6 | SEOGA's ability to produce zkTLS attestations from insurer portals | Data provenance architecture (full scope) | SEOGA |
| 7 | Investor reporting requirements (what specific metrics, formats, and frequencies do target investors expect?) | Report design, dashboard prioritization | Laminar (investor conversations) |

---

## Implementation Timeline Guidance

### POC (target: buildable in weeks)

| Phase | Duration | Deliverable |
|-------|----------|-------------|
| **Week 1–2** | Schema + ingestion | Canonical data model in PostgreSQL. Ingestion adapters for SEOGA batch file and bank CAMT.053 (or mock data if integrations not ready). Event store with hash chains |
| **Week 3–4** | Core pipeline | Reconciliation matching logic. Advance lifecycle tracking. Sweep computation. Exception flagging |
| **Week 5–6** | Ratings + reporting | Per-insurer scoring. Pool quality score. Model accuracy tracking. Monthly report template. Basic investor dashboard |
| **Week 7–8** | Integration + hardening | End-to-end testing with real or realistic data. Operator workflows for exception handling. Documentation |

**Prerequisite:** SEOGA data format and bank partner API specification must be available by Week 1. If not, Weeks 1–4 use mock data with realistic distributions, and real integration is a follow-on phase.

### Full Scope (designed for scale, built incrementally)

Full scope components are built as the POC validates the model and investor demand materializes. Priority order:

1. **Real-time ingestion** — upgrade batch to streaming as SEOGA and bank integrations mature
2. **Enhanced ratings** — per-hospital scoring, full stress suite, as the pipeline accumulates enough observed data
3. **Investor-grade dashboard** — when moving from a single investor to multiple
4. **Data provenance (zkTLS)** — when institutional investors require independent verification
5. **Audit trail hardening** — Merkle anchoring, blockchain migration steps
