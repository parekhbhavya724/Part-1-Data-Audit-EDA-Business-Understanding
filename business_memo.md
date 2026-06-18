# Business Memo

## Situation

The 60-day churn rate is 47.0%. Retention should not be a blanket discount campaign: the evidence points to different failure modes, including inactive-but-valuable customers, recent support friction, weak web/app engagement, high discount dependence, and poor post-purchase experience.

## Dataset-Backed Churn-Risk Hypotheses

1. Customers with older purchase recency are more likely to churn. See `charts/churn_by_recency.png`.

| recency_days    |   count |     mean |
|:----------------|--------:|---------:|
| (-0.001, 30.0]  |     699 | 0.11731  |
| (30.0, 60.0]    |     441 | 0.303855 |
| (60.0, 90.0]    |     361 | 0.457064 |
| (90.0, 120.0]   |     234 | 0.653846 |
| (120.0, 180.0]  |     363 | 0.873278 |
| (180.0, 1000.0] |     302 | 0.913907 |

2. Low 30-day web/app activity is a churn warning. Customers with few or no sessions show materially higher churn than recently active customers. See `charts/churn_by_last_visit.png` and `charts/recency_vs_sessions.png`.

| sessions_30d   |   count |     mean |
|:---------------|--------:|---------:|
| (-1, 0]        |     190 | 0.663158 |
| (0, 2]         |     592 | 0.638514 |
| (2, 5]         |     599 | 0.509182 |
| (5, 10]        |     674 | 0.364985 |
| (10, 100]      |     345 | 0.208696 |

3. Recent support contact is a service-recovery signal, especially when ticket counts stack up. See `charts/churn_by_tickets.png`.

|   ticket_count_90d |   count |     mean |
|-------------------:|--------:|---------:|
|                  0 |    1902 | 0.505783 |
|                  1 |     449 | 0.351893 |
|                  2 |      46 | 0.152174 |
|                  3 |       3 | 0        |

4. Higher average discount exposure can indicate customers trained to wait for offers. See `charts/churn_by_discount.png`.

| avg_discount_pct_180d   |   count |     mean |
|:------------------------|--------:|---------:|
| (-0.01, 0.1]            |     411 | 0.800487 |
| (0.1, 0.2]              |     400 | 0.3875   |
| (0.2, 0.3]              |     715 | 0.369231 |
| (0.3, 0.4]              |     599 | 0.420701 |
| (0.4, 0.5]              |     221 | 0.411765 |
| (0.5, 0.8]              |      54 | 0.666667 |

5. Returns create churn risk and should trigger non-discount retention such as quality follow-up or exchange support.

| return_rate_180d   |   count |        mean |
|:-------------------|--------:|------------:|
| (-0.01, 0.0]       |    2142 |   0.471522  |
| (0.0, 0.1]         |       0 | nan         |
| (0.1, 0.25]        |      46 |   0.0869565 |
| (0.25, 0.5]        |     128 |   0.390625  |
| (0.5, 1.0]         |      84 |   0.75      |

6. Acquisition channel quality differs, so CRM should compare retention cost by channel before investing more in reacquisition.

| acquisition_channel   |   churn_rate |
|:----------------------|-------------:|
| Google Search         |     0.504292 |
| Instagram             |     0.499033 |
| Marketplace           |     0.491228 |
| Influencer            |     0.47619  |
| Referral              |     0.421717 |
| Organic               |     0.398204 |

## What To Investigate Before A Retention Campaign

- Validate whether high discount customers are margin-positive after retention cost.
- Split retention actions by reason: service recovery, replenishment reminder, product education, or loyalty benefits.
- Exclude customers without marketing consent from promotional outreach and use service/account channels only when appropriate.
- Create a manual review lane for customers who are high-value but have conflicting signals, such as high spend with recent support complaints.
- Track holdout groups so the company can measure incremental retention rather than normal repeat purchase behavior.
