<div style="border-top: 6px solid #024731; border-bottom: 1px solid #B2B2B2; padding: 12px 0; margin-bottom: 24px; font-family: 'Open Sans', Helvetica, Arial, sans-serif;">
  <div style="color:#024731;font-weight:700;letter-spacing:.06em;text-transform:uppercase;font-size:.85rem;">University of Hawaiʻi at Mānoa · Shidler College of Business</div>
  <div style="color:#000;font-weight:700;font-size:1.25rem;margin-top:4px;">FIN-321 International Finance &amp; Securities</div>
  <div style="color:#525252;font-size:.95rem;">FX Transaction Hedging Project — Technical Specification</div>
</div>

# U.S. Pharmaceutical Exporter — FX Transaction Hedge Model · Technical Specification

| Field | Value |
|------|------|
| **Created by** | Justin Toyama |
| **Updated by** | Justin Toyama |
| **Date Created** | 2026-08-07 |
| **Date Updated** | 2026-08-07 |
| **Version** | 1.0 |
| **LLM Used** | ChatGPT (GPT-5.6 Luna) — drafting and model-structure assistance |
| **Role** | Treasury Analyst / FP&A Analyst |
| **Audience** | CFO / Director of Treasury |
| **Companion Workbook** | Student FX hedging model |

---

## 1. Problem Statement

A U.S. pharmaceutical exporter expects to receive **€8,000,000** from a European customer. The receivable is denominated in euros while the company's functional and reporting currency is the U.S. dollar, creating transaction FX exposure. At the stated inception spot rate of **1.0890 USD/EUR**, the receivable has a current dollar value of approximately **$8,712,000**. A decline in the euro against the dollar before settlement would reduce realized USD proceeds and could compress the export transaction's expected margin and cash flow. This specification defines the analytical framework for comparing no hedge, forward, money-market, and option hedges.

**Exposure:** EUR receivable  
**Foreign-currency amount:** €8,000,000  
**Quote convention:** USD per EUR  
**Spot at inception (`S0_in`):** 1.0890 USD/EUR  
**Settlement date:** TBD from assignment/scenario data  
**Objective:** Protect the USD value of the euro receivable while evaluating the trade-off between certainty, cost, and upside participation.

---

## 2. Inputs (Known Variables)

### 2.1 Core Inputs

| Standardized Name | Description | Unit | Scenario Value |
|---|---|---:|---:|
| `FC_AMT` | Foreign-currency receivable | EUR | 8,000,000 |
| `S0_in` | Spot exchange rate at inception | USD/EUR | 1.0890 |
| `F0_in` | Forward rate to settlement | USD/EUR | TBD |
| `R_USD` | USD interest rate to settlement | Annual % | TBD |
| `R_FC` | EUR interest rate to settlement | Annual % | TBD |
| `T_DAYS` | Days to settlement | Days | TBD |
| `BASIS` | Day-count denominator | Days | TBD / 365 textbook assumption |
| `K_PUT` | Put strike for EUR receivable | USD/EUR | TBD |
| `PREM_PUT` | Put premium | USD/EUR | TBD |

### 2.2 Derived / Intermediate Values

| Name | Description | Formula |
|---|---|---|
| `DF_USD` | USD accumulation factor | `1 + R_USD × T_DAYS / BASIS` |
| `DF_FC` | EUR accumulation factor | `1 + R_FC × T_DAYS / BASIS` |
| `FV_PREM_PUT` | Future value of put premium | `−PREM_PUT × FC_AMT × DF_USD` |
| `USD_NO_HEDGE` | USD proceeds without hedge | `S_T × FC_AMT` |
| `USD_FWD` | Locked USD proceeds under forward | `FC_AMT × F0_in` |
| `USD_MM` | Locked USD proceeds under money-market hedge | `(FC_AMT / DF_FC) × S0_in × DF_USD` |
| `USD_PUT(S_T)` | USD proceeds under put hedge | `MAX(S_T,K_PUT) × FC_AMT + FV_PREM_PUT` |

---

## 3. Assumptions & Constraints

- All exchange rates are quoted as **USD per EUR**. A higher quote means EUR appreciation and increases the USD value of the receivable.
- The company has a **EUR receivable**, so the primary FX risk is EUR depreciation against USD.
- The base model uses a single settlement date and deterministic future-spot sensitivity rather than probability-weighted forecasts.
- The forward, money-market, and option inputs must be populated from the assignment's market-data requirements before final recommendation.
- The money-market hedge is assumed to replicate the forward hedge under covered interest-rate parity.
- Option premium is paid upfront in USD and carried to settlement at the USD interest rate for comparability.
- Counterparty credit risk, taxes, accounting treatment, transaction costs, and bid-ask spreads are excluded from the base case unless required by the assignment.
- No hedge is treated as the unprotected benchmark.

---

## 4. Calculation Flow

### Step 1 — Derived inputs

1. `DF_USD = 1 + R_USD × T_DAYS / BASIS`
2. `DF_FC = 1 + R_FC × T_DAYS / BASIS`
3. `FV_PREM_PUT = −PREM_PUT × FC_AMT × DF_USD`

### Step 2 — Forward hedge

The exporter sells €8,000,000 forward for USD at `F0_in`.

`USD_FWD = FC_AMT × F0_in`

The resulting USD proceeds are fixed at inception and do not vary with settlement spot `S_T`.

### Step 3 — Money-market hedge

1. Borrow the present value of the EUR receivable: `FC_AMT / DF_FC`.
2. Convert the borrowed EUR to USD at `S0_in`.
3. Invest the resulting USD amount to settlement.
4. Use the EUR receivable to repay the EUR borrowing at maturity.

`USD_MM = (FC_AMT / DF_FC) × S0_in × DF_USD`

The model should compare `USD_MM` with `USD_FWD` as a covered-interest-parity check.

### Step 4 — Option hedge

Purchase a EUR put option with strike `K_PUT`.

For each settlement spot `S_T`:

`USD_PUT(S_T) = MAX(S_T,K_PUT) × FC_AMT + FV_PREM_PUT`

This creates a minimum protected USD value while retaining upside if the euro appreciates sufficiently to offset the option premium.

### Step 5 — Sensitivity table

Evaluate settlement spot across a grid centered on `S0_in`, using the assignment-required range. For each `S_T`, calculate:

| Strategy | USD outcome |
|---|---|
| No hedge | `S_T × FC_AMT` |
| Forward | `USD_FWD` |
| Money market | `USD_MM` |
| Put option | `USD_PUT(S_T)` |

Also calculate hedge profit for each active hedge:

`HEDGE_PROFIT_k(S_T) = USD_k − USD_NO_HEDGE`

The overall winner at each spot is the strategy producing the highest USD proceeds. The best active hedge excludes the no-hedge alternative.

### Step 6 — Summary metrics

- Current unhedged USD value: `8,000,000 × 1.0890 = $8,712,000`.
- `USD_FWD`: calculated after the forward rate is obtained.
- `USD_MM`: calculated after USD/EUR interest rates and tenor are obtained.
- `USD_FLOOR_PUT`: minimum option-hedged proceeds over the sensitivity grid.
- Baseline comparison: evaluate each strategy at `S_T = 1.0890`.

---

## 5. Outputs

| Output | Description | Purpose |
|---|---|---|
| Input panel | All market and scenario inputs | Single source of truth |
| Strategy summary | Forward, money-market, and option outcomes | Executive comparison |
| Sensitivity table | USD proceeds across future EUR/USD rates | Core analytical evidence |
| Hedge-profit columns | Incremental value versus no hedge | Measures hedge value |
| Winner labels | Best overall and best active hedge by scenario | Decision support |
| Sensitivity chart | USD proceeds versus future EUR/USD | Visual comparison |
| Executive recommendation | Recommended strategy and rationale | Final decision memo |

### 5.1 Initial Known Value

At the current spot rate of 1.0890 USD/EUR:

**€8,000,000 × 1.0890 = $8,712,000**

This is the unhedged baseline at inception, not a guaranteed future receipt.

---

## 6. Validation & Limitations

### 6.1 Validation Checks

- Confirm `FC_AMT = 8,000,000` EUR.
- Confirm `S0_in = 1.0890 USD/EUR`.
- Confirm forward and money-market outcomes are approximately equal when covered interest-rate parity holds.
- Confirm the put hedge provides a floor once the option premium is included.
- Confirm the no-hedge outcome rises when EUR appreciates and falls when EUR depreciates.
- Confirm all strategies use the same settlement-date basis when comparing outcomes.

### 6.2 Limitations

The current scenario does not provide the forward rate, USD and EUR interest rates, settlement tenor, option strike, or option premium. Those values must be obtained from the Stage 1 assignment instructions or approved market-data sources before the numerical hedge comparison is finalized. The current specification therefore defines the calculation framework and records the known scenario inputs without inventing missing market data.

---

## 7. Decision Framework

The initial recommendation should prioritize **risk reduction and predictable USD cash flow** because the company is a U.S. exporter with a known EUR receivable. A forward hedge is the leading candidate when the objective is to lock in a known USD value and avoid option premium expense. A EUR put becomes more attractive if management places significant value on retaining upside from EUR appreciation while accepting an upfront premium. The final selection should be based on the completed market-data sensitivity analysis and the company's approved treasury risk policy.

---

## References

Stauffer, A. (2026). *FX Transaction Hedging Project — Technical Specification Template*. Shidler College of Business, University of Hawaiʻi at Mānoa. urlTemplate specificationhttps://github.com/adamwstauffer/shidler/blob/main/courses/International-Finance-And-Securities/projects/fx-hedging/_templates/template-spec.md

Stauffer, A. (2026). *FX Hedging Stage 1*. urlStage 1 instructionshttps://adamwstauffer.github.io/ai-lms/fx-hedging-stage1.html
