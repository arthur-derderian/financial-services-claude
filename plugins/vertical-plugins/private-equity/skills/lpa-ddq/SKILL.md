# LPA DDQ Playbook — Claude as Augmented DDQ Analyst

This skill automates the due diligence review of incoming Limited Partnership Agreements (LPAs) for private equity fund managers. It replaces a 3-day associate workflow (full read, clause-by-clause extraction, RCCI coordination) with a ~10-minute Claude processing cycle followed by mandatory human sign-off.

**Trigger phrases:** "lpa ddq", "lpa review", "lpa due diligence", "analyse lpa", "lpa entrant", "ddq lpa"

---

## Calibration

Before any processing, the skill determines the LP profile:

**Mode A - Institutional** (fund of funds, insurer, pension fund, sovereign): full ILPA DDQ 2020 template, maximum granularity, mandatory GP commentary on all flagged clauses, SFDR fully scoped.

**Mode B - Family Office / HNWI**: ILPA sections C-D-F-H only (Fund Terms, Governance, Reporting, Legal & Compliance), economic focus (fees, carry, waterfall), ESG only if Art. 8 or 9. If the family office has a dedicated investment team (structured MFO), treat as Mode A.

**SFDR classification** scales the ESG scope (Bloc J):
- Non-classified: 5-8 questions (ESG policy, exclusions, annual reporting)
- Article 8: 15-20 questions (PAI, engagement, SFDR annual report)
- Article 9: 25-30 questions (sustainable investment objective, KPIs, DNSH per EU Taxonomy Art. 17, mandatory PAI per RTS SFDR 2022/1288 Annex I, taxonomy alignment declared vs. measured)

---

## Required Documents

**Level 1 - Mandatory** (activates Phases 1-4):
- Final signed LPA
- Fund regulations (règlement de gestion)
- LPA amendments (chronological)
- Target LP side letter (if existing)

**Level 2 - Optional enrichment** (improves Phases 3-4):
- PPM / offering memorandum (misrepresentation risk check)
- Negotiated term sheet vs. final LPA comparison
- Annual fund report
- Other LP side letters (activates Phase 1-bis MFN analysis)
- Proprietary LP DDQ template (if non-ILPA format)

**Level 3 - Normative references** (embedded, source of all flags):
ILPA DDQ 2020, ILPA Principles 3.0, ILPA Reporting Template 2021, ILPA Credit Facilities Guidance 2017/2020, IPEV Valuation Guidelines, AIFMD Art. 19 & 22, RGAMF Art. 313-18 to 313-21, SFDR 2019/2088, EU Taxonomy 2020/852 Art. 17, RTS SFDR 2022/1288 Annex I, DAC6, TCFD, ERISA DOL 29 CFR 2510.3-101, France Invest guidelines.

**Level 4 - Historical comparison** (activates Phase 5 only):
- Prior vintage LPAs from the same GP (Fund I, II, III) - depersonalized

---

## Five-Phase Workflow

### Phase 0 - Preparation + Complexity Scan

**Step 0a - Depersonalization**
LP names -> LP-001, LP-002 | Fund -> FUND-A | Individuals -> PERS-01 | Legal entities -> ENTITY-01 | Bank details, commitment amounts, beneficial ownership percentages, legal counsel names, placement agents, proprietary ESG scores, legal opinions -> masked. Document hierarchy: signed LPA > amendments (chronological) > side letters > fund regulations > PPM.

**Step 0b - GP Entity Identification**
Identify and distinguish: SGP (portfolio management company, AMF-regulated), GP entity (carried interest holder, LPA signatory), CarriedCo (separate carry vehicle if present - Luxembourg, Jersey, Delaware). For key man, removal, change of control, and clawback clauses: identify which entity is the contracting party.

**Step 0c - Complexity Assessment**
Claude reads the LPA and produces a Complexity Card (25 dimensions) before any extraction. No Phase 1 launch if any dimension is UNDETERMINED - escalate to IR or RCCI within 48 business hours, GP written clarification required by D+5 if unresolved.

Key dimensions assessed: fund type (FPCI/SLP/SCA/RAIF), LPA jurisdiction, parallel vehicles, feeder/blocker structure, ERISA plan assets exposure, side letters, waterfall structure (deal-by-deal/fund-level/hybrid), SFDR classification, LPA language(s), LPAC status, co-invest sleeve, clawback basis (gross/net of tax), recycling, closing type, equalization, GP commit, management fee offset rate, preferred return compounding, named key men, subscription line, SGP/GP/CarriedCo structure, amendment procedure majority, LP default provisions, excuse/exclusion rights, change of control clause, fund currency, governing law.

---

### Phase 1 - Dynamic DDQ Extraction

Questions generated dynamically from the Complexity Card across 10 blocs:

**Bloc A - Management Fees**: fund currency, fee rates and stepdown schedule, "Commitment" definition (gross vs. net), fee billing start date (flag if before first closing - UNFAVORABLE LP per ILPA), fee offsets (transaction fees, monitoring fees - ILPA minimum 80%), broken deal expenses, organizational expenses cap (market standard 0.5% of commitments), management fee clawback provision.

**Bloc B - Carried Interest**: rate (market standard LBO mid-cap: 20%), hurdle rate (standard: 8%), compounding method (daily/quarterly/annual - material economic impact on hold period >5 years), catch-up rate and method, preferred return accrual rule under subscription line (flag if accrual starts at drawdown date vs. LP capital call date - UNFAVORABLE LP per ILPA Credit Facilities Guidance 2017/2020), carry temporal eligibility (investment period only vs. full fund life), performance ratchet/super-carry, carry vesting (cliff, good leaver/bad leaver), clawback (trigger conditions, gross vs. net of tax, escrow/insurance/personal guarantee, team vs. GP entity vs. CarriedCo), interim carry distributions and escrow mechanism.

**Bloc C1 - Distribution Waterfall**: full step-by-step sequence (verbatim), deal-by-deal vs. fund-level (cross-fund netting presence/absence), waterfall across parallel vehicles (per-vehicle vs. consolidated basis), FX rules in waterfall if multi-currency, distributions in-kind (LP right to refuse, IPEV valuation), income distributions vs. realization distributions, minimum distribution thresholds, NAV credit facility impact.

**Bloc C2 - Equalization** (activated if subsequent closing): contribution base, interest rate and compounding, repayment method, treatment if prior distributions already made.

**Bloc C3 - Recycling** (activated if recycling present): authorized scope, cap (market standard 100-120% of net commitments), time window, cross-reference to investment period extension risk.

**Bloc D - Governance & LPAC**: composition, LPAC structure across parallel vehicles, voting rights (simple/qualified/unanimous by matter - exhaustive table), consent matters, LP information rights, advisory committee, conflicts of interest policy (RGAMF Art. 313-18 to 313-21), side letter disclosure, LPAC member confidentiality obligations, amendment procedure (majority by clause type, quorum mechanics, denominator definition - verbatim required), excuse/exclusion rights, LP default provisions (grace period, dilution, forced transfer, reinstatement), removal for cause (threshold, "cause" definition verbatim, accrued carry treatment), no-fault removal (threshold, denominator verbatim - critical: ambiguity can make removal contractually impossible), change of control (intermediate regulatory sanctions coverage - flag MATERIAL if absent), related party transactions, GP indemnification (standard: gross negligence/willful misconduct only - flag if lower threshold), GP liability cap, D&O and E&O insurance, investment period extension conditions, fund term extension, side pockets, GP-led secondary/continuation vehicle, fund dissolution payment order.

**Phase 1-bis - MFN Analysis** (activated if Level 2 side letters available): rights granted per anonymized side letter, MFN clause triggerable by target LP, economic impact of MFN activation, activation conditions.

**Bloc E - Investments**: investment period duration and start date, investment policy (geography, sectors, ticket size), concentration limits, leverage policy, follow-on investments, bridge financing, guarantees and indemnifications, cross-fund investing, post-investment period follow-on reserve, opportunity allocation policy between successor funds and SMAs, subscription line (cap, maximum duration - ILPA standard 180 days, LP reporting, IRR impact disclosure), NAV credit facility (cap, priority vs. LP distributions - flag MATERIAL if senior).

**Bloc F - LP Co-investment** (activated if co-invest sleeve present): contractual vs. best efforts rights, allocation policy, fee terms (market standard: no fee/no carry), LP response deadline, ERISA exclusions, GP ROFR on LP co-investment position transfer.

**Bloc G - Reporting & Information**: report frequency and content, ILPA Reporting Template 2021 compliance, external auditor scope and delivery timeline, LP audit rights, LP portal and secure document delivery, capital call notice parameters (minimum notice: market standard 10 business days per ILPA 2020 - flag if below), distribution notices, AIFMD Art. 22 compliance, AIFMD Art. 19 valuation frequency, write-down notification obligation (flag MATERIAL if absent - threshold, delay, provisional vs. definitive write-off), valuation methodology (IPEV, independent valuator frequency), financial statements accounting standard (IFRS/US GAAP/PCG - flag MATERIAL if absent for Solvency II/IORP II LPs), ad hoc information request response time, LP access to portfolio company financial statements, LP-level IRR calculation, portfolio data confidentiality vs. regulatory reporting obligations, subscription line reporting, tax reporting (K-1, IFU, FATCA/CRS).

**Bloc H - Transfer, Secondary Liquidity & LP Protection**: ROFR conditions and valuation method, GP consent standard ("not to be unreasonably withheld" vs. absolute discretion), lock-up period, affiliate transfers, transfer restrictions (ERISA, OFAC, PEPs), LP-led secondary, GP forced transfer, confidentiality clause vs. LP regulatory obligations (flag DEAL-BREAKER if exceptions absent for institutional LPs), LP limited liability (express provision, recall provisions under applicable LP Act), governing law and dispute resolution (arbitration vs. national courts, flag MATERIAL if absent - international arbitration is market standard for cross-border institutional funds).

**Bloc I - Clawback & Key Man**: see Bloc B for clawback; key man (named individuals, time commitment %, trigger events, remedy period, LP rights post-event, entity covered: SGP/GP/CarriedCo).

**Bloc J - ESG/SFDR**: scope per SFDR classification (see Calibration above). Mode A includes D&I metrics and TCFD alignment (climate scenario analysis, physical and transition risk disclosure).

---

### Extraction Rules (Non-Negotiable)

**Verbatim rule**: answer = exact clause. Never a summary. Never a paraphrase.

**ILPA reference rule**: each question prefixed with ILPA DDQ 2020 identifier (e.g., "ILPA-2020-C.3.a") or "AD-HOC-[Bloc]-[sequential number]" if outside ILPA template.

**Language rule**: LPA in French -> French verbatim, no translation. LPA in English -> English verbatim + [indicative French translation in brackets, not substituted]. Mixed -> clause in source language. RCCI validation of translations mandatory before any LP dispatch.

**Ambiguity rule**: (a) reproduce clause verbatim, (b) expose both readings with reasoning, (c) status = AMBIGUOUS - RCCI escalation within 48 business hours, (d) GP written clarification required by D+5 if unresolved, (e) never resolve by default.

**Business Day rule**: for every time-sensitive delay (capital call notice, LP default grace period, removal notice, ROFR, LPAC vote), extract the contractual "Business Day" definition (jurisdiction, applicable calendar) verbatim. If absent: ABSENT - flag and escalate. All delay calculations depend on this definition.

---

### Phase 1 Output Format

Two simultaneous formats produced:

**Full Format** (for RCCI + legal counsel) - 15 columns:
`# | ILPA Ref. | Bloc | Sub-category | DDQ Question | Verbatim Answer | Language | Source | GP Commentary | Flag | Flag Source | Flag Nature | Priority | Status | Ambiguity Note`

Flag Nature: UNFAVORABLE LP / FAVORABLE LP / MARKET OUTLIER
Priority: DEAL-BREAKER / MATERIAL / MINOR
Status: ANSWERED / ABSENT-GP CLARIFICATION REQUIRED / AMBIGUOUS
Flag Source: precise citation required for every flag (e.g., "ILPA Principles 3.0 - Section 4.2: 'Fee offset minimum 80%'")

**Condensed Format** (for partner/decision-maker) - 8 columns:
`# | ILPA Ref. | Bloc | DDQ Question | Analytical Summary (50 words max) | Flag + Priority | Status | Ambiguity Note`

Condensed format summary is not verbatim - it is an analytical synthesis for fast decision-maker reading. Full verbatim is always in the Full Format (same # reference).

Output: Full Format + Condensed Format + flag summary by bloc.

---

### Phase 2 - Pre-filled DDQ

Default output: ILPA DDQ Template 2020, sections C through H. If LP provides a proprietary template (Level 2 upload), map each proprietary question to the corresponding verbatim answer from Phase 1.

Cover letter produced systematically: fund identification (FUND-X), LP (LP-001), date, LPA version analyzed, Level 1-2 documents used, methodology note (analysis does not substitute independent legal counsel, all ambiguous clauses flagged require RCCI validation before dispatch), RCCI signature space.

For each question: verbatim answer (from Phase 1) + [indicative French translation if LPA in English] + [GP COMMENTARY - TO BE COMPLETED IF RELEVANT] (blank, never pre-filled by Claude) + source + if flagged: "RCCI Attention Point" box with flag source, nature, priority.

---

### Phase 3 - Inconsistency Detection

Cross-document review: all Phase 1 extracted clauses, full transactional document read outside Phase 1 scope, PPM vs. LPA (misrepresentation risk), chronological consistency of amendments.

Output table: `Theme | LPA Source (Art. + Page) | Contradicting Source (Ref.) | Nature | Severity | Primary Document`

Severity levels: LEGALLY CONTRADICTORY (external legal counsel required before dispatch) / MATERIAL (modifies LP economic rights or governance) / MINOR / SILENCE (present in one document, absent in another) / PPM/LPA (PPM representation not reflected or contradicted by LPA) / TEMPORAL (chronological inconsistency between amendments).

---

### Phase 4 - Completeness Report

Top 5 absent clauses by estimated LP economic impact (ranked, at the top of report before full table).

Full table: `Question | Bloc | ILPA Ref. | Recommendation | Justification`

Recommendations: GP REQUEST (contractual clarification required before DDQ dispatch) / MARKET STANDARD (absent clause, established market practice - reference ILPA Principles 3.0, France Invest, etc.) / LP RISK (material absence - flag explicitly in DDQ) / ABSENT-CLARIFICATION REQUIRED (Claude cannot distinguish voluntary absence from drafting omission without GP input).

---

### Phase 5 - Vintage Comparison (Optional - Level 4 Documents Required)

Triggered only if prior vintage LPAs uploaded. Sequential: one vintage at a time, Complexity Card validated per vintage before delta analysis.

Table: `Clause | Fund I | Fund II | Fund III | Direction | Qualification`

Qualification: LP IMPROVEMENT / LP REGRESSION / STABLE / NOT APPLICABLE

If no comparable third-party LPAs: benchmark vs. ILPA Principles 3.0 only - this limitation is stated explicitly in the output.

---

### Phase RCCI - Final Validation (Mandatory Human Sign-off)

Claude produces:

**0. RCCI Executive Summary** (read first): 3-column table, max 10 rows, sorted by descending impact. `# | Critical Point | Recommendation | Estimated Resolution Deadline`. Recommendations: SEND AS IS / REQUEST GP CLARIFICATION / REFUSE DISPATCH. Scope: all DEAL-BREAKER flags + all AMBIGUOUS + all LEGALLY CONTRADICTORY inconsistencies. If no DEAL-BREAKER flag: stated explicitly at the top.

**1. Identification**: Fund (FUND-X), LP (LP-001), date, LPA version, LP mode (A/B), documents processed, SGP/GP/CarriedCo structure.

**2. DDQ Extraction Control**: questions generated, Full Format breakdown (ANSWERED / ABSENT / AMBIGUOUS counts), flags total by priority and nature, summary by bloc, MFN analysis results if Phase 1-bis activated.

**3. Translation Control** (if LPA in English or mixed): indicative translation count, RCCI validation status.

**4. Cross-Document Inconsistency Control**: counts by severity level, resolution status.

**5. Critical Points - RCCI Decision Required**: auto-generated list of DEAL-BREAKER + AMBIGUOUS + LEGALLY CONTRADICTORY items. Format: [CRITICAL POINT - MANUAL DECISION REQUIRED] + context + options. Deadline: D+5 business days from DDQ delivery.

**6. Regulatory Compliance Checklist**:
- Art. L.214-24 CMF (AIFMD transposed)
- AIFMD Art. 22 (investor information + 6-month annual report deadline)
- AIFMD Art. 19 (valuation frequency)
- RGAMF Art. 313-18 to 313-21 (SGP conflicts of interest)
- AMF DOC-2012-19 reporting obligations
- SFDR pre-contractual obligations (Art. 6 + Art. 8 or 9)
- RTS SFDR 2022/1288 Annex I mandatory PAI indicators (if Art. 9)
- AML/CFT R.561-16 CMF: LP due diligence level, beneficial owners verification, fund origin, PEP screening
- Sapin II Art. 17 (GP anti-corruption program)
- GDPR Art. 6 & 13 (legal basis for LP data processing)
- DAC6 / Directive 2018/822 (reportable cross-border arrangement: Yes/No/Undetermined - if Undetermined: escalate to tax counsel before dispatch)
- FATCA/CRS: fund declarant status
- OFAC/EU/UN sanctions screening
- LP confidentiality clause vs. LP regulatory obligations

**7. Pre-Dispatch Authorizations**:
- [ ] RCCI: full file validation (Executive Summary section 0 signed)
- [ ] IR Manager / Managing Partner: informed
- [ ] External legal counsel: if LEGALLY CONTRADICTORY unresolved
- [ ] Tax counsel: if DAC6 Undetermined or FATCA/CRS unconfirmed

**8. Archiving**:
- 5 years minimum (RGAMF Art. 321-30 from DDQ signature or first closing date, whichever is later; Art. L.123-22 Code de Commerce for pre-contractual commercial documents)
- 10 years recommended for signed DDQ + full RCCI file
- Confidentiality classification: professional secrecy

---

## Structural Constraints

- Default legal structure: French FPCI - adapt if SLP, SCA, or RAIF
- LPA jurisdiction: from Complexity Card - conditions enforceability of removal, clawback, LP default clauses
- SGP/GP/CarriedCo distinction: mandatory identification in Phase 0 - key man, removal, change of control, and clawback clauses must specify which entity is the contracting party
- Depersonalization: Phase 0 protocol mandatory before any upload
- Language: source verbatim always primary - indicative translation only if English, in brackets, not substituted, RCCI-validated before dispatch
- Ambiguity: never resolved by default - AMBIGUOUS + RCCI escalation within 48 hours, blocked at D+5
- Business Day: contractual definition extracted systematically
- Flags: each flag justified with cited primary source - no flag without justification
- ABSENT: always status ABSENT-GP CLARIFICATION REQUIRED - Claude never distinguishes voluntary absence from drafting omission without GP input
- GP Commentary column: always blank - Claude never pre-fills it
- No access to external LPA databases
- Mandatory human RCCI validation before any dispatch - non-negotiable

---

## Scoping Questions (Asked Before Processing)

1. DDQ destination format: ILPA standard / proprietary LP template (upload)?
1-bis. If family office: dedicated investment team (structured MFO)? -> Mode A if yes
2. LPA language: French / English / mixed?
3. SFDR classification: Art. 8 / Art. 9 / non-classified?
4. Existing side letters: target LP side letter available? Other LP side letters available for MFN analysis?
5. LPAC: constituted / not / being constituted?
6. First closing or subsequent closing? If subsequent: equalization mechanism present?
7. Prior vintage LPAs for Phase 5: yes - how many vintages? / no?
8. Level 2 documents available: which ones? (PPM available?)
9. Internal or outsourced RCCI?
10. Parallel vehicles / separately structured co-invest sleeve?
11. GP commit: minimum % in LPA? Financing terms?
12. Subscription line / NAV credit facility: authorized in LPA?
13. SGP/GP/CarriedCo structure: single entity or separate entities?
14. LPA governing law: French / English / Delaware / Luxembourg / other?
15. US LPs in the fund: yes / no? If yes, approximate % - ERISA 25% threshold?
16. Feeder fund / blocker structure for certain LPs: yes / no?
17. LP regulatory profile: ACPR insurer / IORP II pension fund / FoF / sovereign / other?

Questions asked sequentially until the Complexity Card is complete and confirmed. Processing begins only once the target is validated.
