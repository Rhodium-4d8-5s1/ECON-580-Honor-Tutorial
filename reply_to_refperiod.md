# Re: comment 1 — reference period k = −4 and the full pre-period ranking

**Short version:** I'd keep the reference at k = −4, and I don't think the short pre-window is a problem.

## Why −4

−4 is the paper's own normalization: the event-study regression (eq. 17 in the Aug 2025 draft) is written with ρ₋₄ = 0, so −4 isn't something I chose from our data — it's the reference the paper fixes, and anchoring there is what lets the figure show the full sawtooth (the jump at 0 and the t+1 reversion) against the pre-period. Since this is a replication, I'd keep the reference pinned to the paper rather than re-pick it from our own pre-trend, which would be circular. So I'd use −4 regardless of how the diagnostics below come out — they're a check on your concern, not the basis for the choice.

## Your concern, checked two ways

I modified the ranking code (now `02a_full_ranking_k.do`, with the original `02_based_year_k.do` kept on record) to print the full ranking of k = −6 … −1 by the sum of squared pre-event (k < 0) coefficients, re-anchoring the saved event-study coefficients to each candidate. Ranked on the income coefficients, streaming shown alongside. Both versions you asked for.

### (a) All four frequencies

| rank | ref k | income SS | streaming SS |
|:----:|:-----:|:---------:|:------------:|
| 1 | **−4** | 0.0775 | 0.0026 |
| 2 | −2 | 0.0817 | 0.0074 |
| 3 | −3 | 0.1435 | 0.0038 |
| 4 | −6 | 0.1474 | 0.0076 |
| 5 | −5 | 0.1505 | 0.0050 |
| 6 | −1 | 0.2185 | 0.0034 |

### (b) 7-day and 28-day only (cleanest plots)

| rank | ref k | income SS | streaming SS |
|:----:|:-----:|:---------:|:------------:|
| 1 | **−4** | 0.0269 | 0.0024 |
| 2 | −2 | 0.0275 | 0.0071 |
| 3 | −3 | 0.0289 | 0.0033 |
| 4 | −1 | 0.0379 | 0.0024 |
| 5 | −6 | 0.0873 | 0.0074 |
| 6 | −5 | 0.0974 | 0.0046 |

So even taken as a robustness check, −4 is the flattest pre-trend in both versions — it isn't an outlier forced on us by the 6-period window.

I also plotted the income pre-trend anchored at −4 vs −3 side by side (`02b_refperiod_compare.do`, figures below). The two are essentially on top of each other at 7-day, 28-day and monthly, and both show the flat pre-period into the jump at 0; 14-day separates a little, consistent with it being the noisiest frequency. So moving the reference barely changes the picture — the short window doesn't make −4 look strange.

Both checks point the same way: the reference choice is immaterial here, and −4 (the paper's) is fine.

## Figures and files

The comparison figures (income pre-trend, −4 vs −3) are below. Every event-study figure — these, plus the standard −4 plots at all four frequencies — regenerates from the do-files, so you can reproduce any of them by running `01` then `02b`. I'll email you the do-files.

*(One note on the comparison plots: both references are estimated on a common balanced sample — workers with every period present at both −4 and −3 — so the two curves are directly comparable. That makes N here a bit smaller than in the baseline −4 figures.)*

---

### 7-day
![Income pre-trend, −4 vs −3, 7-day](cmp_png/es_cmp_income_7.png)

### 14-day
![Income pre-trend, −4 vs −3, 14-day](cmp_png/es_cmp_income_14.png)

### 28-day
![Income pre-trend, −4 vs −3, 28-day](cmp_png/es_cmp_income_28.png)

### Monthly
![Income pre-trend, −4 vs −3, monthly](cmp_png/es_cmp_income_monthly.png)
