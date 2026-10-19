**Re: comment 1 — reference period**

I'd keep k = −4. The reference is chosen by the rule that taking the pre-event period that makes the pre-trend flattest, i.e. the smallest sum of squared pre-event (k < 0) coefficients. On the data that rule returns −4, so −4 comes from our own pre-trend. It coincides with the reference the paper uses.

**Full ranking (k = −6,...,−1)**

`02a_full_ranking_k.do` re-anchors the event-study coefficients to each candidate k and sums the squared pre-event coefficients (SS). It ranks on income, with streaming shown alongside. I kept the original `02_based_year_k.do` unchanged so the original selection step stays on record.

*(a) All four frequencies*

| rank | ref k | income SS | streaming SS |
|:----:|:-----:|:---------:|:------------:|
| 1 | **−4** | 0.0775 | 0.0026 |
| 2 | −2 | 0.0817 | 0.0074 |
| 3 | −3 | 0.1435 | 0.0038 |
| 4 | −6 | 0.1474 | 0.0076 |
| 5 | −5 | 0.1505 | 0.0050 |
| 6 | −1 | 0.2185 | 0.0034 |

*(b) 7-day and 28-day only*

| rank | ref k | income SS | streaming SS |
|:----:|:-----:|:---------:|:------------:|
| 1 | **−4** | 0.0269 | 0.0024 |
| 2 | −2 | 0.0275 | 0.0071 |
| 3 | −3 | 0.0289 | 0.0033 |
| 4 | −1 | 0.0379 | 0.0024 |
| 5 | −6 | 0.0873 | 0.0074 |
| 6 | −5 | 0.0974 | 0.0046 |

−4 ranks first in both. In (b) the top four are close (0.027–0.038), so on the two cleanest frequencies the pre-trend is flat under any of them.

**Does the 6-period window make −4 look strange?**

No. Moving the reference doesn't change the shape of the event study. It shifts the whole series up or down by a constant, namely the coefficient at the new reference. I plotted the income pre-trend anchored at −4 and at −3 on the same figure for each frequency (`02b_refperiod_compare.do`, figures below; same balanced sample as the baseline). The two curves are parallel: the −4 curve sits 0.017 (7-day), 0.082 (14-day), 0.035 (28-day) and 0.110 (monthly) away from the −3 curve, and the pre-period profile and the jump at 0 are identical. The reference only decides which period is pinned to zero, and under −4 the pre-period sits closest to zero overall, which is what the ranking measures.

**Files**

I'll email you the do-files. Every figure here, and the baseline event-study figures at −4 for all four frequencies, can be reproduced by running them: `01_replication_by_payperiod_updated.do` for the baseline, `02a_full_ranking_k.do` for the ranking, and `02b_refperiod_compare.do` for the −4 vs −3 comparison.

---

**Figures: income pre-trend, ref −4 vs ref −3 (continuous instrument)**

7-day

<img width="1703" height="1010" alt="image" src="https://github.com/user-attachments/assets/aed2a353-f822-4ed6-9d59-3ea5d01ffeb9" />

14-day

<img width="1696" height="1036" alt="image" src="https://github.com/user-attachments/assets/32c8ca63-616e-40ed-b0c4-c03e4fa7fc18" />

28-day

<img width="1661" height="1041" alt="image" src="https://github.com/user-attachments/assets/aaaa77c9-2b27-4550-a1ba-08ef5d11ee06" />

Monthly

<img width="1661" height="983" alt="image" src="https://github.com/user-attachments/assets/93aa2188-7087-4321-8d9c-ba74659f20a6" />

