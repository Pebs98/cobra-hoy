# Session Prompt: Cobra HOY — Three-Party Incentive Alignment 

## Objective

Refine the Cobra HOY incentive design so it clearly communicates incentive alignment across all three parties: **investors**, **SEOGA**, and **Laminar**.  We need it to work as both an investor teaser AND a document that SEOGA and Laminar can stand behind — where each party can see their role, their upside, their boundaries, and why the structure protects them.

## Context

Cobra HOY is a secured lending facility that provides early payment to Spanish private healthcare centers against expected insurance company payouts. Three parties make it work:

- **SEOGA** — the dominant claims processing platform in Spain's private healthcare sector (~€900M/year, 1,000+ centers, 60+ insurers). They provide the data layer: credit scoring, claims data, reconciliation, insurer liaison, hospital distribution. They never touch money and hold no financial license.
- **Laminar** — designs the financial structure, builds the technology platform, and sources capital. They also never touch money. The bank partner executes all payment flows.
- **Investors** (family offices / HNW) — deploy capital through an SPV that is the single lending vehicle. They're passive participants seeking 7–8% net returns with 15–60 day capital cycles and institutional obligor quality (regulated Spanish insurers).

### 1. Clear role boundaries ("who does what and who doesn't")
The master doc is precise about this: SEOGA never touches money, Laminar never touches money, the bank partner executes all payment flows. This separation is what makes the structure work legally and operationally, and it's what protects each party. The current draft mentions it in passing but doesn't frame it as an intentional design principle.

### 2. Value distribution across parties
The draft shows hospital fees (~0.65%) and physician fees (~1.25%) and the net return to investors (7–8%). But it doesn't explain how value flows to SEOGA or Laminar. What does SEOGA earn for providing the data and distribution layer? What does Laminar earn for building and operating the platform? These economics need to be at least directionally visible — not necessarily exact numbers, but enough to show that everyone is incentivized to make the facility perform.

### 3. Risk allocation by party
The dual recourse structure (contractual claim against hospital + notarized pledge) protects investors. But:
- **SEOGA's risk** is reputational and operational — if their credit scores are inaccurate, the model underperforms, hospitals lose trust, and the relationship network that makes everything possible degrades.
- **Laminar's risk** is execution and capital-raising — they've designed the structure and built the platform; if it doesn't work, they absorb the development cost and reputational hit.
- **Investors' risk** is financial — capital deployed, mitigated by short duration, institutional obligors, and the Double Lockbox structure.

Each party's skin in the game should be visible.

## Source Documents

Read these files in order of priority:

1. **Master Architecture Doc** — `a.MASTER.obra-hoy-architecture.md`
   - Sections 1 (Executive Summary), 3 (SEOGA Deep Dive — what they do and don't do), 5 (Financial Structure — Double Lockbox, SPV), 7 (Capital Provider Perspective)
   - This is the single source of truth for structure, roles, and mechanics

2. **Full Executive Summary** — `investor-materials/executive-summary.md`
   - Current investor-facing narrative; this is what we're refining
   - Pay attention to: Structure section, Two Revenue Streams, Return Profile, Comparable Positioning, Why Family Offices, Parties table

3. **Capital Strategy Memo** — `internal-memos/capital-strategy-memo.md`
   - Internal Laminar document with unvarnished thinking on pricing, competition, investor targeting, and the commercial logic
   - Contains the "why there's no competition" argument and capital recycling math

4. **Short Executive Summary** — `investor-materials/executive-summary-short.md`
   - Condensed version; useful as a reference for what was considered essential at minimum

5. **Current Two-Pager** — `Shareables/cobra-hoy-two-pager.docx`
   - The document we're revising; two-page Word doc with embedded Double Lockbox diagram

## Deliverable

work with me to arrive at the ideal structure for this tripartite agreement. remember we still need the buy in from seoga, they will be comparing us to banks and other fintechs so we need to give them a good deal. we also want them to have some skin in the game so they are good at scoring and on the line for risk monitoring. 

Laminar should get a good amount of recurring revenue from operating this too. 

Investors need to buy in to this for it to work so we absoluteley need to make it attractive to them. 

At some point i would like to migrate this into an onchain pool but we must work within thr confines of the spanish regulatory enviroment for this firstr phase. 
