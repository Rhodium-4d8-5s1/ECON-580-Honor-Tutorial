# Stage 1 - Code review: `replication_by_payperiod.do` and `dynamic_payperiod.do`

This contains the notes on the two baseline do-files, comparing against the paper. No code was changed whereas issues are flagged only. 

---

## 1. What each file estimates

### `replication_by_payperiod.do`
Static pay-period replication, in **GBP levels**, run separately for 7-, 14-, 28-day and monthly workers. Produces the three-column Table 1 and the event-study figures (`es_combined_*` over the full −6…+6 window; `es_fig4_*` truncated to k ≤ 0).

Estimating equation (per frequency), 2SLS:

    D.stream_it = beta * D.pay_it + alpha_i + tau_m + e_it,   D.pay_it instrumented by the coworker shock

- `D.stream_it` = change in `pp_stream` (EWA streaming summed to the pay period), GBP
- `D.pay_it` = change in `wages_pp` (own pay per check), GBP
- `alpha_i` worker FE (`employee_id`), `tau_m` calendar-month FE (`cal_month`); SEs clustered by firm (`company_id`)

**Paper correspondence:** Table 1 ("Impact of Income on Consumption") and Figure 4 (event studies).

**Outcome and units (key interpretation point):** The outcome is streaming in GBP *levels*, not log consumption. So `beta` is a £-streamed-per-£-of-pay object (an MPC-type quantity) thus is **not** comparable to the paper's headline Δlog-income coefficient of 0.221 (an elasticity), nor to its 0.10 nondurable MPC. The elasticity analogue is the separate `replication_by_payperiod_log.do`.

### `dynamic_payperiod.do`
Horizon-by-horizon response of streaming to the same transitory shock, by frequency, at horizons **H = 3 and H = 5**, estimated by **GMM** (worker + calendar-month FE partialled out, firm-clustered SEs). Produces the per-period MPC (`L_h`) and cumulative MPC (`S_h`) tables and figures.

**Paper correspondence:** Appendix J / Figure 5 ("Dynamic and Cumulative MPCs"). Same levels/units point as I have stated above. But it is the natural choice since £ streamed per £ of shock already is an MPC.

---

## 2. Sample restrictions (in the order applied)

Two phases in total. **Phase A** is applied once to the daily admin file before the frequency split whereas **Phase B** runs inside the per-frequency loop, so its counts are per frequency. W = distinct workers and PP = distinct employee × pay-period.

**Phase A (once):**

| # | Restriction | Workers | Pay-periods |
|---|---|---|---|
| 0 | raw `admin_data_all_ss` | 817,736 | 11,405,901 |
| 1 | non-missing `company_id` (filled within worker, then drop remaining missing) | 499,018 | 7,854,812 |
| 2 | non-switchers (single `company_id` over the worker's history) | 499,018 | 7,854,812 |
| 3 | modal pay length in {7,14,28,30,31} | 418,143 | 7,458,872 |
| 4 | non-missing `payday_date` | 418,143 | 7,458,872 |
| 5 | collapse to employee × pay-period | 418,143 | 7,458,872 |

Note: step 1 is the one large cut (817,736 → 499,018 workers), but it mainly removes empty non-worker day-rows in the raw daily file rather than genuine workers.

**Phase B (per frequency — one column each for 7 / 14 / 28 / monthly):**

Each cell is workers / pay-periods.

| # | Restriction | 7 | 14 | 28 | monthly |
|---|---|---|---|---|---|
| 6 | keep this frequency | 48,861 / 2,129,852 | 45,032 / 1,006,414 | 235,406 / 3,115,413 | 88,844 / 1,207,193 |
| 7 | drop each worker's first & last pay period | 47,617 / 2,032,130 | 42,989 / 916,350 | 218,364 / 2,644,601 | 81,945 / 1,029,505 |
| 8 | minimum tenure: ≥ 13 remaining periods (2·event_window+1) | 34,789 / 1,955,484 | 24,739 / 811,288 | 88,712 / 1,931,160 | 35,520 / 757,718 |
| 9 | minimum coworkers: `company × payday_date` cell with > 10 workers | **34,678 / 1,948,939** | **24,679 / 809,885** | **88,360 / 1,922,990** | **35,132 / 748,871** |

Row 9 (bold) is the final estimation sample per frequency. 
Note: In row 0-3 the pay-period count still includes rows later dropped for missing `payday_date` (step 4), so read pay-periods from step 4 on.

---

## 3. Why the analysis is split by pay frequency

Each frequency is estimated as a separate sample. The leave-one-out coworker mean is built within `company_id × payday_date` cells, and it is constructed after the frequency split and after restrictions 6–8. 

So here are several possible consequences that I think would be of concern:

- The instrument averages only over same-firm, same-frequency coworkers who share the exact `payday_date` **and** survive the sample restrictions, so this is definitely not over the whole firm.
- The same `payday_date` requirement is what ties this to the frequency split: Only workers on the same pay schedule share paydays, say a weekly worker and a monthly worker almost never get paid on the same date, so they could never be in the same cell anyway. Thus, splitting by frequency is what makes a common `payday_date` (and thus a coworker cell) possible.

---

## 4. Instrument construction and the three Table 1 columns

Leave-one-out mean (per `company × payday_date` cell):

    firm_wages_pp_lo = (sum over cell of wages_pp  -  own wages_pp) / (firm_workers - 1)

Three columns share the same second stage (only the instrument changes):

| do-file column | instrument | timing | paper column |
|---|---|---|---|
| col 1 | `coworker_shock = -F.d_firm_wages_pp_lo` | one-period-ahead (lead) | col 1 — Period-Ahead Pay Per Check |
| col 2 | `coworker_shock_ppc = d_firm_wages_pp_lo` | contemporaneous | col 3 — Pay Per Check |
| col 3 | `coworker_shock_tot = d_firm_wages_tot_lo` | contemporaneous | col 5 — Total Pay |

Col 1 is the clean transitory (period-ahead) instrument whereas cols 2–3 are the §4.5 broadening steps that re-admit predictable variation. The paper's cols 2 and 4 add the (Δlog income) × checking-buffer interaction, but they are omitted here.

At the pay-period level `wages_pp = pp_wages / paychecks_in_period` and `wages_tot = pp_wages`, so cols 2 and 3 are identical whenever `paychecks_in_period = 1`. See issue (b) in the next section for more on this.

---

## 5. Issues flagged (not fixed)

**(a) Estimation sample differs across Table 1 columns.** Col 1's instrument is a lead (`F.coworker_shock`), cols 2-3 are contemporaneous. There is no common-sample restriction before the three `ivreghdfe` calls, so col 1 loses each worker's last usable period that cols 2–3 keep. Measured on the server:

| Frequency | col 1 (period-ahead) | col 2 (pay-per-check) | col 3 (total) | col 2 − col 1 |
|---|---|---|---|---|
| 7-day | 1,879,543 | 1,914,235 | 1,914,235 | 34,692 |
| 14-day | 760,527 | 785,206 | 785,206 | 24,679 |
| 28-day | 1,746,172 | 1,834,551 | 1,834,551 | 88,379 |
| monthly | 678,511 | 713,680 | 713,680 | 35,169 |

In every frequency `col 2 = col 3 > col 1`, with the gap equal to the workers' final periods the lead instrument cannot use. So the three columns are not estimated on a common sample. Should the three columns be forced onto the common sample?

**(b) "Cols 2 and 3 identical by construction" depends on `paychecks_in_period = 1` for all rows.** As explained in Section 4, cols 2 and 3 coincide only if `paychecks_in_period = 1` everywhere, If any period had two paychecks, the two columns would diverge for those rows. I have confirmed on the server that it's 1 for 100% of rows.

**(c) Event-study reference period.** Code sets `ref_k = -3` (each event-time coefficient is differenced against the k = −3 level) whereas the paper normalizes at **k = −4** (eq. 17). This is just a normalization difference, but it makes the levels non-comparable to the paper's figure unless aligned. I also think the horizon unit differs: pay periods here are not aligned with months in the paper.

**(d) Levels vs. logs (interpretation, not a defect).** As in Section 1, `beta` is an MPC-type object in £, not the paper's log elasticity. The log analogue is `replication_by_payperiod_log.do`. I state this explicitly so that the coefficient isn't read against 0.221.

**(e) Leave-one-out defined over the estimation sample.** `firm_workers` and the leave-one-out mean use only workers surviving restrictions 6-8, so the instrument is not a leave-one-out over the full firm roster. I am stating this in case the intended comparison group is broader.

Thank you for your precious time.
