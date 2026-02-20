# Data Directory

## Overview

This directory contains the synthetic marketplace dataset used throughout the project. The data was procedurally generated via the `MarketplaceConfig` dataclass in the main notebook, using a fixed random seed (`seed=42`) to ensure full reproducibility.

## File

**`marketplace_data.csv`** — 10,000 customer records across 50 geographic neighborhoods.

## Schema

| Column | Type | Description |
|---|---|---|
| `customer_id` | int | Unique customer identifier |
| `avg_order_value` | float | Average spend per order (clipped $10–$100, normally distributed) |
| `days_since_last_order` | float | Recency of last order (exponentially distributed) |
| `total_orders` | int | Lifetime order count (negative binomial) |
| `recency_score` | float | Derived RFM recency score — `1 / (1 + days_since_last_order / 30)` |
| `frequency_score` | float | Derived RFM frequency score — `log1p(total_orders) / 5` |
| `monetary_score` | float | Derived RFM monetary score — `avg_order_value / 50` |
| `app_opens_last_week` | int | App engagement (Poisson distributed) |
| `email_engagement` | float | Email open/click rate (Beta distributed) |
| `is_premium` | int | Premium membership flag (binary, ~25% prevalence) |
| `neighborhood_id` | int | Geographic cluster ID (0–49) |
| `courier_density` | float | Neighborhood-level courier supply (Gamma distributed) |
| `treatment` | int | Whether customer's neighborhood was assigned a coupon campaign (cluster-randomized) |
| `outcome` | int | Whether the customer placed an order during the promotional period |
| `true_cate` | float | **Ground-truth** Conditional Average Treatment Effect — available because data is synthetic, used for model evaluation |
| `segment` | str | Behavioural segment: `Persuadable`, `Sure Thing`, `Other` |
| `pred_cate` | float | X-Learner predicted CATE (added during model training) |
| `Y0` | int | Potential outcome without coupon (counterfactual) |
| `Y1` | int | Potential outcome with coupon (counterfactual) |
| `expected_order_value` | float | Expected revenue per conversion used in ROI calculations |
| `outcome_treated` | int | Alias for Y1 |
| `outcome_control` | int | Alias for Y0 |

## Customer Segments

The dataset contains four ground-truth behavioural archetypes that define the true causal structure:

| Segment | Share | True Avg CATE | Description |
|---|---|---|---|
| **Persuadable** | ~5.3% | +0.39 | Medium recency/engagement, non-premium — ideal coupon targets |
| **Sure Thing** | ~3.5% | +0.06 | Highly engaged, converts regardless — wasted spend if targeted |
| **Other** | ~91.2% | ~0.00 | General population with near-zero treatment response |
| **Sleeping Dog** | <1% | Negative | High-value dormant customers who react negatively to coupons |

## Experimental Design

Treatment assignment is **cluster-randomized** — entire neighborhoods (not individual customers) were assigned to treatment or control. 50% of the 50 neighborhoods received the coupon intervention.

Network interference was explicitly simulated: control customers in high-treatment-rate neighborhoods received a spillover penalty of `0.3 × neighborhood_treatment_rate` applied to their baseline conversion probability, modelling the real-world courier scarcity effect in two-sided marketplaces.

## Regenerating the Data

To regenerate the dataset from scratch, run the **Section 2 (Data Generation)** cells of the main notebook:

```bash
jupyter notebook notebooks/Uplift_Modeling_Complete_Project.ipynb
```

The same data will be produced deterministically thanks to `random_seed=42` in `MarketplaceConfig`.
