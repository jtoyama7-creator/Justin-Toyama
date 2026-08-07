# Phase 4 — Market Data Memo

**Student:** Justin Toyama  
**GitHub:** jtoyama7  
**Scenario:** U.S. Pharmaceutical Exporter  
**Foreign-currency exposure:** EUR 8,000,000 receivable  
**Retrieval date:** August 7, 2026  
**Purpose:** Populate and validate the Phase 2/3 FX hedging model with market data.

## Market-data inputs

| Input | Value | Source / method | Retrieval timestamp | Notes |
|---|---:|---|---|---|
| `FC_AMT` | 8,000,000 EUR | Scenario input | 2026-08-07 | Known scenario value |
| `S0_in` | 1.1580 USD/EUR | Wall Street Journal market report | 2026-08-07 | Reported EUR/USD level after July U.S. jobs data; used as live spot proxy because a directly queryable official closing quote was not available in the retrieved sources. |
| `T_DAYS` | 365 days | Phase 4 convention | 2026-08-07 | 1-year hedge horizon |
| `R_USD` | TBD | Required: 1-year USD government yield or deposit/reference rate | 2026-08-07 | Not invented; requires direct market-data retrieval before final workbook population. |
| `R_FC` | TBD | Required: 1-year EUR government yield or deposit/reference rate | 2026-08-07 | Not invented; requires direct market-data retrieval before final workbook population. |
| `F0_in` | TBD | Preferred live 1-year forward; fallback is CIP | 2026-08-07 | Cannot compute defensibly until both selected rates are populated. |
| `K_PUT` | TBD | Scenario convention: strike at/near live spot | 2026-08-07 | Requires scenario option convention / live quote. |
| `PREM_PUT` | TBD | Scenario-given premium | 2026-08-07 | Must use the scenario value; not available in the supplied scenario details. |
| `K_CALL` | TBD | Scenario convention | 2026-08-07 | Requires scenario option convention. |
| `PREM_CALL` | TBD | Scenario-given premium | 2026-08-07 | Must use the scenario value; not available in the supplied scenario details. |

## Spot-rate observation

The retrieved August 7, 2026 market reporting states that the euro reached approximately **$1.1580**, a seven-week high, following weaker-than-expected U.S. payroll data. This is materially above the original scenario spot of 1.0890. citeturn2news0

Using 1.1580 as the working live-spot proxy, the EUR 8,000,000 receivable has an unhedged USD value of:

`8,000,000 × 1.1580 = $9,264,000`

This should replace the Phase 2 placeholder spot only after the source and timestamp are retained with the workbook.

## Forward methodology

Phase 4 requires a live 1-year forward when available. If unavailable, the CIP-implied forward is:

`F0 = S0 × (1 + R_USD × T/360) / (1 + R_FC × T/360)`

The model should use the Phase 4 convention of a 360-day denominator for this forward calculation. The resulting CIP forward should be compared with any scenario-indicative forward and the difference explained.

## Rate-selection note

The Phase 4 instructions require a **1-year USD rate** and a **1-year EUR rate**, with an explanation of why each was selected. The retrieved public sources did not provide sufficiently reliable, directly comparable August 7, 2026 1-year rates in the available search results. Rather than substitute unrelated tenors or invent values, those cells remain TBD. This is an intentional audit control.

A usable final population should select, for example, a U.S. Treasury 1-year yield (or documented USD deposit/reference rate) and a euro-area government/reference rate of comparable tenor, record the exact source and retrieval time, then calculate the CIP forward.

## Audit observations

1. **Scenario amount verified:** EUR 8,000,000.
2. **Original scenario spot verified:** 1.0890 USD/EUR from the Phase 2 specification.
3. **Live spot proxy obtained:** 1.1580 USD/EUR from August 7, 2026 market reporting. citeturn2news0
4. **No missing rate was fabricated.**
5. **Forward rate is intentionally blocked** until comparable USD/EUR 1-year rates are documented.
6. **Option strikes and premiums are intentionally blocked** until the scenario convention/premium values are available.
7. The Phase 4 handout requires the workbook to be repopulated, checks rerun, and the outputs cross-checked against the FX Hedging Lab. fileciteturn4file0

## Required next population steps

- Obtain and timestamp the USD 1-year rate.
- Obtain and timestamp the EUR 1-year rate.
- Compute the CIP-implied 1-year forward using the Phase 4 formula.
- Obtain the scenario's put/call strike and premium assumptions.
- Enter all values into the workbook's named inputs only.
- Re-run parity, sensitivity, and chart checks.
- Enter the same live inputs into the FX Hedging Lab and document any discrepancy/resolution.

## Sources

- Wall Street Journal, August 7, 2026 market report on EUR/USD and U.S. jobs data. citeturn2news0
- Phase 4 handout, FIN 321 International Business Finance, Summer 2026. fileciteturn4file0
- FX Hedging Lab: https://adamwstauffer.github.io/ai-lms/fxlab.html
