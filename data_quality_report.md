# Data Quality Report

## Executive Summary

The customer universe contains 2,400 customers. All left-joinable snapshot-level files cover the full customer universe, while support tickets are sparse by design. The largest quality concerns are missing optional profile fields, missing order ratings, duplicate-like order records, outlier order values, and post-snapshot orders that must be excluded from feature engineering.

## Dataset Inventory

| dataset       |   rows |   columns |   duplicate_rows |   missing_cells |   unique_customers |
|:--------------|-------:|----------:|-----------------:|----------------:|-------------------:|
| customers     |   2400 |         9 |                0 |            1787 |               2400 |
| orders        |  10009 |        10 |                0 |              80 |               2400 |
| tickets       |   1921 |         8 |                0 |               0 |               1247 |
| web           |   2400 |        10 |                0 |               0 |               2400 |
| labels        |   2400 |         4 |                0 |               0 |               2400 |
| snapshot      |   2400 |        29 |                0 |            1386 |               2400 |
| interventions |   2400 |         5 |                0 |               0 |               2400 |

## Missing Values

| dataset   | column       |   missing_count |
|:----------|:-------------|----------------:|
| customers | loyalty_tier |            1386 |
| customers | skin_type    |             401 |
| orders    | rating       |              80 |
| snapshot  | loyalty_tier |            1386 |

Treatment recommendation: keep `loyalty_tier` missingness as `Not enrolled`, keep `skin_type` as `Unknown` for EDA/modeling, and impute `rating` only when aggregating customer-level features. Missing ratings are behaviorally different from low ratings and should not be overwritten in raw data.

## Duplicate-Like Records

`orders.csv` has 12 records with `_DUP` in `order_id`. They should be investigated against customer/date/category/amount before removal. For this project, they are flagged in the audit and retained in raw EDA unless an analysis specifically requires de-duplicated order events.

## Outliers

Using the IQR rule, gross amount values above INR 1,619.30 are outliers. There are 536 such rows, with a maximum gross amount of INR 24,789.38. Recommendation: winsorize or cap only in model features if the model is sensitive; for business analysis, keep them visible because high-value customers may deserve retention priority.

## Invalid Values And Leakage

There are 1,872 post-snapshot order rows after 2025-09-30. These rows are valid for label construction but invalid as model inputs. All modeling in this submission uses `rfm_modeling_snapshot.csv`, which the dictionary defines as leakage-safe.

## Join Checks

|                                 |   count |
|:--------------------------------|--------:|
| orders_without_customer         |       0 |
| tickets_without_customer        |       0 |
| web_missing_customers           |       0 |
| labels_missing_customers        |       0 |
| interventions_missing_customers |       0 |
