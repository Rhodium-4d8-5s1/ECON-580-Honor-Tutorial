# Stage 2 — baseline fixes + income-process robustness (B.3, B.4)

This covers the Stage 1 review fixes and the two Appendix B robustness checks. All analysis is at the pay-period level, in GBP levels, run separately by pay frequency (7 / 14 / 28 / monthly). The outcome is EWA streaming (first-differenced throughout).

## Baseline fixes (`01_replication_by_payperiod_updated.do`)

**Table 1 — dropped column 3, common sample across columns 1–2.** Column 3 (Total Pay) was identical to column 2 at the pay-period level (one paycheck per period, so pay-per-check = total), so it is removed. Columns 1 and 2 are now estimated on the same observations: the period-ahead (lead) instrument in column 1 is missing for each worker's final period, so column 1 defines the estimation sample and column 2 is restricted to it. Verified equal Ns across the two columns at every frequency.

**Event-study reference period — set `ref_k = -4`.** The reference period was arbitrary at `-3`. I chose it empirically: for each candidate `k`, re-anchor the event study to `k` and sum the squared pre-event (k<0) coefficients across all four frequencies; the flattest is `k = -4` (income pre-trend sum 0.078 at `-4` vs 0.144 at `-3`), and streaming agrees. This also matches the paper's normalization (eq. 17), so it resolves the Stage 1 flag. The selection is reproducible in `02_based_year_k.do`.

## B.3 — less-than-full persistence (quasi-differencing)

Three columns per frequency (`table_b1_*.tex`, produced by `05_B3_table.do`): (1) baseline first difference; (2) quasi-differenced with in-sample ρ; (3) quasi-differenced with ρ = 0.996 (Commault). Only own income and the coworker instrument are quasi-differenced; streaming stays first-differenced (paper Result AR).

**Result: the estimate is robust to relaxing the unit root.**

| frequency | baseline (ρ=1) | estimated ρ | ρ = 0.996 |
|---|---|---|---|
| 7-day | 0.071 (F=110) | 0.075 (F=107) | 0.071 (F=110) |
| 14-day | 0.085 (F=18) | 0.036 (F=1.0) | 0.085 (F=18) |
| 28-day | 0.158 (F=69) | 0.167 (F=22) | 0.159 (F=69) |
| monthly | 0.111 (F=152) | 0.122 (F=115) | 0.111 (F=152) |

- The **ρ = 0.996 column is essentially identical to baseline** at every frequency — the clean confirmation that the estimate is insensitive to the unit-root relaxation, consistent with the paper (0.221 → 0.196).
- The **estimated-ρ column is close to baseline** for 7-, 28-day and monthly. The 14-day value (0.036) is an outlier, but it's a **weak-instrument artifact** (first-stage F = 1.0), driven by the implausible in-sample ρ there — see the caveat below.

**Caveat on in-sample ρ.** The eq-B.8 autocovariance-ratio estimator (`03_B3_estimate_rho.do`) is unstable in this sample. The worker ρ is roughly 0.5–0.9; the coworker ρ is noisy and at times implausible (0.18 at 14-day; the stability check across lags k=0…3 returns negative / >1 values at the coarser frequencies). This is likely because we are in GBP levels with a noisy leave-one-out firm mean, versus the paper's monthly log income. I therefore read the B.3 robustness off the ρ = 0.996 benchmark and the stable frequencies rather than the in-sample ρ. Estimated ρ values and the stability check are in `03_B3_estimate_rho.do` and `04_B3_stability_rho.do`.

## B.4 — serial correlation in the transitory shock (MA(0) check)

`Cₖ = Cov(Δy, −Δȳ_coworker at t+k)`, k = 0…4, three columns (first-difference / est ρ / ρ=0.996), per frequency, produced by `06_B4_leadcov.do`. Under MA(0), C₀ and C₁ are the dominant terms and k≥2 collapses toward zero.

**Result: MA(0) is qualitatively supported, as in the paper.** The covariances concentrate at k=0 (negative) and k=1 (positive) — the paper's sign pattern — and fall off at k≥2. The collapse is cleanest at 7-day (k≥2 roughly an order of magnitude below C₀, C₁); at 14-, 28- and monthly the k≥2 values are smaller than C₀/C₁ but noisier, as the longer leads fall on smaller samples. The ρ = 0.996 column tracks the first difference closely; the estimated-ρ column does not collapse, reflecting the same unreliable in-sample ρ as in B.3. (Magnitudes are large because we're in £ levels, not the paper's logs — only the relative pattern is meaningful.)

## How to run / notes for review

- **Run `01_replication_by_payperiod_updated.do` first** — it saves the per-frequency panels (`payperiod_panel_*.dta`) that the B.3/B.4 scripts read.
- The ρ values hardcoded in `05_B3_table.do` and `06_B4_leadcov.do` come from `03_B3_estimate_rho.do` (k=0).
- Paths at the top of each new do-file are left blank — set `$pdata` (the panel folder) and the output/log paths before running.
- New do-files (do not edit the baseline): `02_based_year_k.do`, `03_B3_estimate_rho.do`, `04_B3_stability_rho.do`, `05_B3_table.do`, `06_B4_leadcov.do`.

## Open question

For the 7- and 14-day frequencies, you suggested calendar-week fixed effects (week labeled by its Monday) instead of calendar-month. I left this out of this PR pending your confirmation — happy to add it here or as a follow-up. Everything above uses calendar-month FE, as in the baseline.
