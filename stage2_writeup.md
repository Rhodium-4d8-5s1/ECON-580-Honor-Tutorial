# Stage 2 — baseline fixes and income-process robustness (B.3, B.4)

This covers the Stage 1 review fixes and the two Appendix B robustness checks. All analysis is at the pay-period level, in GBP levels, run separately by pay frequency (7/14/28/monthly). The outcome is EWA streaming.

## Baseline fixes (`01_replication_by_payperiod_updated.do`)

**Table 1 (dropped column 3, common sample across columns 1–2)** 
Column 3 (Total Pay) was identical to column 2 at the pay-period level (one paycheck per period, so pay-per-check = total), so it is removed. Columns 1 and 2 are now estimated on the same observations: the period-ahead (lead) instrument in column 1 is missing for each worker's final period, so column 1 defines the estimation sample and column 2 is restricted to it. Verified equal N across the two columns at every frequency.

**Event-study reference period**
The reference period was at `-3`. I chose `-4` because for each candidate `k`, re-normalize the event study to `k` and sum the squared pre-event (k<0) coefficients across all four frequencies, then we can see the flattest is `k = -4` (income pre-trend sum 0.0775 at `-4` vs 0.144 at `-3`), and streaming agrees. This also matches the paper's normalization (eq. 17). The selection is in `02_based_year_k.do`.

## B.3 less-than-full persistence

Three columns per frequency (`table_b1_*.tex` from `05_B3_table.do`): (1) baseline first difference; (2) quasi-differenced with in-sample $\rho$; (3) quasi-differenced with $\rho$ = 0.996 (Commault). 
Only own income and the coworker instrument are quasi-differenced whereas streaming stays first-differenced (paper formula).

**Result: the estimate is robust to relaxing the unit root.**

| frequency | baseline ($\rho$=1) | estimated $\rho$ | $\rho$ = 0.996 |
|---|---|---|---|
| 7-day | 0.071 (F=110) | 0.075 (F=107) | 0.071 (F=110) |
| 14-day | 0.085 (F=18) | 0.036 (F=1.0) | 0.085 (F=18) |
| 28-day | 0.158 (F=69) | 0.167 (F=22) | 0.159 (F=69) |
| monthly | 0.111 (F=152) | 0.122 (F=115) | 0.111 (F=152) |

- **The $\rho$ = 0.996 column is essentially identical to baseline at every frequency.** I think this is a clean confirmation that the estimate is insensitive to the unit-root relaxation, consistent with the paper (0.221 -> 0.196).
- **The estimated-$\rho$ column is close to baseline for 7-, 28-day and monthly.** The 14-day value (0.036) is an exception, but it's a weak-instrument artifact (F = 1.0). See below for the stability concern in this sample.

**in-sample $\rho$** 
The eq-B.8 autocovariance-ratio estimator (`03_B3_estimate_rho.do`) is unstable in this sample. The worker $\rho$ is roughly 0.5-0.9; the coworker $\rho$ is noisy and at times implausible (0.18 at 14-day; the stability check across lags k=0,...,3 returns negative or greater-than-1 values at the coarser frequencies). This is likely because we are in GBP levels with a noisy leave-one-out firm mean, versus the paper's monthly log income. Thus, the B.3 robustness is read off the $\rho$ = 0.996 benchmark and the stable frequencies rather than the in-sample $\rho$. Estimated ρ values and the stability check are in `03_B3_estimate_rho.do` and `04_B3_stability_rho.do`.

## B.4 serial correlation in the transitory shock (MA(0) check)

$C_k = \operatorname{Cov}\!\left(\Delta y,\,-\Delta \bar{y}_{\text{coworker},\,t+k}\right)$, $k=0,\ldots,4$, three columns (first-difference / estimated $\rho$ / $\rho$ = 0.996), per frequency, produced by `06_B4_leadcov.do`. Under MA(0), $C_0$ and $C_1$ are sizable and k $\geq$ 2 collapses toward zero.

**Result: MA(0) is qualitatively supported, as in the paper.** The covariances concentrate at k = 0 (negative) and k = 1 (positive) and fall off after the first two periods. The collapse is cleanest at 7-day (k $\geq$ 2 roughly an order of magnitude below $C_0$, $C_1$); at 14, 28 and monthly the k $\geq$ 2 values are smaller than $C_0$,$C_1$ but noisier, as the longer leads fall on smaller samples. The $\rho$ = 0.996 column tracks the first difference closely; the estimated-$rho$ column does not collapse, reflecting the same unreliable in-sample $\rho$ as in B.3 (Magnitudes are like this is because we're in pound levels, not the paper's logs).

## How to run the do files

- **Run `01_replication_by_payperiod_updated.do` first**: It saves the per-frequency panels (`payperiod_panel_*.dta`) that the B.3/B.4 scripts read.
- The $\rho$ values hardcoded in `05_B3_table.do` and `06_B4_leadcov.do` come from `03_B3_estimate_rho.do` (k = 0).
- Paths at the top of each new do-file are left blank, so **set `$pdata` and the output/log paths before running**.
- New do-files (do not edit the baseline): `02_based_year_k.do`, `03_B3_estimate_rho.do`, `04_B3_stability_rho.do`, `05_B3_table.do`, `06_B4_leadcov.do`.

## Follow-up question

For the 7 and 14-day frequencies, you suggested calendar-week fixed effects instead of calendar-month. I left this out of this report since it's a design change to baseline. I am happy to add it here or as a follow-up after approval. Everything above uses calendar-month FE, as in the baseline.
