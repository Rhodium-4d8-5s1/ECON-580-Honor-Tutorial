Thanks for the review, @afras-sial. Below are items 2–6 (item 1 was answered above). All tables and figures are compiled in **Stage2_round2_tables.pdf**, which I've emailed along with the do-files and logs. Per your request, the event-study reference period is now **k = −2** (re-ran `01_replication_by_payperiod_updated.do`). The Table 1 regressions don't depend on the reference period, so only the figures changed.

All estimates are 2SLS in levels (GBP) with the period-ahead coworker instrument; SEs are clustered by firm.

---

### Item 2: frequency-specific benchmark ρ

The benchmark column in `05a_B3_table.do` and `06a_B4_leadcov.do` now uses Commault's 0.95/yr converted to each pay-period length: 7-day **0.999**, 14-day **0.998**, 28-day **0.996**, monthly **0.996**.

As you expected, the impact is negligible. The benchmark-ρ β matches the first-difference β at every frequency (third decimal):

| | 7-day | 14-day | 28-day | Monthly |
|---|---|---|---|---|
| ρ = 1 | 0.0711 | 0.0852 | 0.1585 | 0.1113 |
| Benchmark ρ | 0.0711 | 0.0851 | 0.1585 | 0.1114 |

### Item 3: ρ estimated without worker FE

`03a_B3_estimate_rho.do` re-estimates ρ (eq. B.8, k = 0), residualizing on calendar-month FE only. Dropping worker FE moves every estimate to **0.89–0.99**, close to the benchmark:

| | Worker + month FE: ρ_w | Worker + month FE: ρ_co | Month FE only: ρ_w | Month FE only: ρ_co |
|---|---|---|---|---|
| 7-day | 0.7275 | 0.8276 | 0.9494 | 0.9920 |
| 14-day | 0.5028 | 0.1765 | 0.8884 | 0.9379 |
| 28-day | 0.6361 | 0.8434 | 0.9370 | 0.9628 |
| Monthly | 0.5347 | 0.6393 | 0.9536 | 0.9895 |

I added this as a new column in both B.3 and B.4:

- **B.3:** β with no-worker-FE ρ is 0.0705 / 0.0821 / 0.1583 / 0.1116 (7 / 14 / 28 / monthly), within 0.004 of the first-difference estimate everywhere.
- **14-day first stage:** with no-worker-FE ρ, the first-stage F is 20.5. With the worker-FE ρ it was 1.02.
- **B.4:** the new column tracks the first-difference and benchmark columns at every k: C₀ < 0, C₁ > 0, then small. Only the worker-FE column departs from the MA(0) pattern.

### Item 4: stability check output

Here is the output of `04_B3_stability_rho.do` (log attached in the email). It gives eq. B.8 ρ at lags k = 0…3, with worker + month FE:

| | Series | k=0 | k=1 | k=2 | k=3 |
|---|---|---|---|---|---|
| 7-day | ρ_w | 0.7275 | 0.7606 | 0.8849 | 0.7653 |
| | ρ_co | 0.8276 | 0.8763 | 0.8923 | 0.8738 |
| 14-day | ρ_w | 0.5028 | 0.6588 | 0.7677 | 0.6683 |
| | ρ_co | 0.1765 | −0.4239 | −1.4080 | −0.1346 |
| 28-day | ρ_w | 0.6361 | 0.6784 | 0.5316 | 0.2236 |
| | ρ_co | 0.8434 | 0.8191 | 0.5079 | 0.3255 |
| Monthly | ρ_w | 0.5347 | 0.6303 | 0.2763 | −0.0179 |
| | ρ_co | 0.6393 | 1.3396 | −0.8163 | −0.1072 |

The in-sample ρ is not stable across lags, especially for the coworker series at 14-day and monthly. That is why I read robustness off the benchmark-ρ column.

### Item 5: calendar-week FE

`07_weekFE_baseline.do` replaces calendar-month FE with calendar-week FE, labeling each week by its Monday. It produces the baseline (ρ = 1) tables and event-study figures in two variants: with and without worker FE. The table below shows Table 1, column 1 (period-ahead instrument):

| | 7-day | 14-day | 28-day | Monthly |
|---|---|---|---|---|
| Month FE + worker FE (baseline) | 0.0711 | 0.0852 | 0.158 | 0.111 |
| Week FE + worker FE | 0.0689 | 0.0604 | 0.156 | 0.107 |
| Week FE, no worker FE | 0.0691 | 0.0765 | 0.158 | 0.108 |

Week FE barely move the 7-day, 28-day and monthly estimates. The 14-day estimate with worker FE falls to 0.060, while its first-stage F rises from 17.7 to 50.3. The pay-per-check column and all 16 figures are in the compiled file (Section 4, Figures 5–8).

### Item 6: looser sample

`08_loose_sample.do` uses the same sample construction as `01`, except the minimum-tenure rule is **≥ 4 pay periods** (baseline: ≥ 13). This applies to the regression tables only; the event study keeps the balanced panel. On this sample I re-ran the ρ estimation (both with and without worker FE), B.3 and B.4.

| | 7-day | 14-day | 28-day | Monthly |
|---|---|---|---|---|
| N (loose) | 1,865,672 | 791,953 | 2,079,046 | 837,817 |
| N (baseline) | 1,821,478 | 731,669 | 1,691,917 | 680,271 |
| β, ρ = 1 (loose) | 0.0758 | 0.0891 | 0.1639 | 0.1240 |
| β, ρ = 1 (baseline) | 0.0711 | 0.0852 | 0.1585 | 0.1113 |

The looser rule adds 2–23% more observations and raises β by at most 0.013. The no-worker-FE and benchmark columns behave the same way. B.4 on the loose sample shows the same pattern as the baseline, with C₀ within about 6% of the baseline value.

---

**Files (emailed):**

- Compiled file: `Stage2_round2_tables.pdf`
- Do-files: `01_replication_by_payperiod_updated.do` (ref_k = −2), `03a_B3_estimate_rho.do`, `04_B3_stability_rho.do`, `05a_B3_table.do`, `06a_B4_leadcov.do`, `07_weekFE_baseline.do`, `08_loose_sample.do`
- Logs: one per do-file

All figures and tables can be reproduced by running the do-files in order (01 first).
