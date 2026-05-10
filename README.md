# Unveiling Customer Purchase Dynamics: A Sequential Pattern Mining Approach Using PrefixSpan Algorithm

> **Author:** Touseef Hanif  


---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Results](#results)
- [Business Implications](#business-implications)
- [Limitations](#limitations)
- [Tech Stack](#tech-stack)
- [Future Work](#future-work)
- [References](#references)

---

## Overview

This research investigates **sequential purchasing patterns** of customers in an online retail environment using the **PrefixSpan algorithm** — a highly efficient sequential pattern mining technique. The study analyzes over one million transactions from a UK-based online retailer (2009–2011) to uncover hidden purchase dynamics and translate them into actionable business strategies.

### Key Outcomes at a Glance

| Metric | Value |
|---|---|
| Total Transactions (Raw) | 1,067,371 |
| Valid Transactions (Post-cleaning) | 779,425 |
| Unique Customers | 3,656 |
| Products Analyzed | 200 |
| Frequent Patterns Discovered | 382 |
| Meaningful Patterns (Filtered) | 323 |
| High-Confidence Rules (>30%) | 6 |
| Customer Segments | 4 |
| Average Sequence Length | 7.19 transactions |
| Average Rule Confidence | 39.6% |

---

## Dataset

**Source:** [Online Retail II – UCI Machine Learning Repository](https://doi.org/10.24432/C5CG6D)

The dataset contains real transactional data from a UK-based, non-store online retailer specializing in unique all-occasion gift-ware, spanning **December 1, 2009 to December 9, 2011**.

### Schema

| Attribute | Description | Type |
|---|---|---|
| `InvoiceNo` | Unique 6-digit invoice number per transaction | String |
| `StockCode` | Unique 5-digit product code | String |
| `Description` | Product name | String |
| `Quantity` | Number of units per transaction | Integer |
| `InvoiceDate` | Date and time of transaction | DateTime |
| `Price` | Unit price in British Pounds (£) | Float |
| `CustomerID` | Unique 5-digit customer identifier | String |
| `Country` | Customer's country of residence | String |

### Missing Values (Raw)
- `Description`: 4,382 missing values
- `CustomerID`: 243,007 missing values

---

## Methodology

### 1. Data Preprocessing

The preprocessing pipeline was applied in the following stages:

| Step | Action | Records After |
|---|---|---|
| Raw Dataset | — | 1,067,371 |
| Remove missing `CustomerID` | Essential for building sequences | 824,364 |
| Remove duplicates | Eliminate 26,479 duplicate rows | 797,885 |
| Filter invalid transactions | Remove negative quantity / price ≤ 0 | 779,425 |

**Feature Engineering** — New columns derived:
- `TotalPrice` = Quantity × Price
- `Year`, `Month`, `MonthYear` — from `InvoiceDate`
- `DayOfWeek` — for temporal pattern analysis
- `Hour` — for transaction timing analysis

---

### 2. Sequence Generation

1. **Product Encoding** — Top 200 most frequent products encoded with integer IDs (0–199)
2. **Transaction Grouping** — Transactions grouped per customer by invoice and sorted chronologically
3. **Itemset Creation** — Each transaction converted to a set of purchased product IDs
4. **Sequence Formation** — Ordered list of itemsets per customer (only customers with ≥ 2 transactions included)

> Average sequence length: **7.19 transactions/customer** (range: 2 to 50+)

---

### 3. PrefixSpan Algorithm

PrefixSpan (Pei et al., 2004) is a pattern-growth algorithm that avoids expensive candidate generation by using **prefix-projected databases**.

**Algorithm Steps:**
1. **Initialization** — Scan for all frequent 1-patterns meeting the minimum support threshold
2. **Recursive Projection** — For each frequent pattern, create a projected sub-database
3. **Pattern Growth** — Recursively mine projected databases to extend prefixes
4. **Termination** — Stop when no new patterns can be extended

**Parameters used:**
- **Minimum Support:** 20 customers (~0.55% of customer base)
- **Maximum Pattern Length:** No explicit limit (patterns up to length 3 discovered)

---

## Results

### Top 10 Sequential Patterns

| Rank | Pattern | Support | % of Customers |
|---|---|---|---|
| 1 | WHITE HANGING HEART T-LIGHT HOLDER → REGENCY CAKESTAND 3 TIER | 56 | 1.53% |
| 2 | WHITE HANGING HEART T-LIGHT HOLDER → PARTY BUNTING | 52 | 1.42% |
| 3 | WHITE HANGING HEART T-LIGHT HOLDER → RED HANGING HEART T-LIGHT HOLDER | 52 | 1.42% |
| 4 | RED HANGING HEART T-LIGHT HOLDER → WHITE HANGING HEART T-LIGHT HOLDER | 48 | 1.31% |
| 5 | WHITE HANGING HEART T-LIGHT HOLDER → ASSORTED COLOUR BIRD ORNAMENT | 47 | 1.29% |
| 6 | WHITE HANGING HEART T-LIGHT HOLDER → HEART OF WICKER SMALL | 45 | 1.23% |
| 7 | JUMBO BAG STRAWBERRY → JUMBO BAG RED RETROSPOT | 43 | 1.18% |
| 8 | REGENCY CAKESTAND 3 TIER → GREEN REGENCY TEACUP AND SAUCER | 42 | 1.15% |
| 9 | WHITE HANGING HEART T-LIGHT HOLDER → WOODEN FRAME ANTIQUE WHITE | 41 | 1.12% |
| 10 | WHITE HANGING HEART T-LIGHT HOLDER → HEART OF WICKER LARGE | 40 | 1.09% |

> **96.9% of all patterns are length-2**, confirming most cross-purchase decisions are immediate or near-term.

---

### High-Confidence Sequential Rules (>30%)

| Rank | If Customer Buys... | They Will Next Buy... | Support | Confidence |
|---|---|---|---|---|
| 1 | WHITE HANGING HEART → WOODEN FRAME ANTIQUE WHITE | WHITE HANGING HEART T-LIGHT HOLDER | 19 | **46.34%** |
| 2 | JUMBO BAG STRAWBERRY → JUMBO BAG RED RETROSPOT | JUMBO BAG RED RETROSPOT | 18 | 41.86% |
| 3 | WHITE HANGING HEART → PARTY BUNTING | WHITE HANGING HEART T-LIGHT HOLDER | 21 | 40.38% |
| 4 | WHITE HANGING HEART → PARTY BUNTING | PARTY BUNTING | 19 | 36.54% |
| 5 | WHITE HANGING HEART → RED HANGING HEART T-LIGHT HOLDER | WHITE HANGING HEART T-LIGHT HOLDER | 19 | 36.54% |
| 6 | WHITE HANGING HEART → REGENCY CAKESTAND 3 TIER | REGENCY CAKESTAND 3 TIER | 20 | 35.71% |

---

### Customer Segmentation (K-Means, k=4)

| Segment | Size | Avg. Sequence Length | Avg. Spending | Profile |
|---|---|---|---|---|
| High-Value | 850 | 12.5 txns | £450 | Frequent buyers, high spend, many pattern matches |
| Regular | 1,200 | 7.8 txns | £180 | Consistent behavior, moderate engagement |
| Occasional | 1,100 | 4.2 txns | £85 | Infrequent purchases, low engagement |
| One-Time | 506 | 2.0 txns | £35 | Minimal purchases, minimal engagement |

---

## Business Implications

### Cross-Selling
Implement a **"Frequently Bought Next"** feature on product pages populated dynamically from top sequential patterns. Customers buying *WHITE HANGING HEART T-LIGHT HOLDER* should be recommended *REGENCY CAKESTAND 3 TIER*.

### Personalized Recommendations
Integrate the 6 high-confidence rules (>30%) into the recommendation engine to suggest the most probable next purchase based on individual customer purchase history.

### Targeted Marketing by Segment
- **High-Value** — Loyalty programs, VIP service, early access to new products
- **Regular** — Seasonal promotions, bundle offers on complementary products
- **Occasional** — Re-engagement campaigns, limited-time discounts
- **One-Time** — Win-back campaigns, incentives for a second purchase

### Inventory Optimization
Co-locate frequently sequentially purchased products in warehouses (e.g., *WHITE HANGING HEART T-LIGHT HOLDER* + *REGENCY CAKESTAND 3 TIER*) and synchronize their inventory levels.

### Product Bundling
Create bundles based on high-support patterns — e.g., a **"Decorative Essentials Bundle"** combining *WHITE HANGING HEART T-LIGHT HOLDER*, *PARTY BUNTING*, and *REGENCY CAKESTAND 3 TIER* at a discounted price.

---

## Limitations

- **Product Scope** — Only the top 200 products were analyzed; niche/less frequent items were excluded
- **Temporal Granularity** — No time-gap modeling between purchases (purchases days apart vs. months apart are treated equally)
- **External Factors** — Seasonality, marketing campaigns, and product availability were not incorporated
- **Generalizability** — Findings are specific to a UK-based gift-ware retailer and may not transfer to other categories or geographies

---

## Tech Stack

| Library | Purpose |
|---|---|
| `pandas` | Data loading, cleaning, manipulation |
| `numpy` | Numerical computations |
| `matplotlib` & `seaborn` | Data visualization |
| `prefixspan-py` | PrefixSpan algorithm implementation |
| `scikit-learn` | K-Means clustering for customer segmentation |
| `networkx` | Sequential purchase network visualization |

---
---

## Future Work

- **Temporal Modeling** — Incorporate explicit time-gap functions to distinguish immediate vs. delayed sequential purchases
- **Advanced Algorithms** — Explore RNNs or transformer-based models for sequence prediction
- **Multi-Dataset Analysis** — Apply methodology to different retailers and product categories to validate generalizability
- **Real-Time Implementation** — Build a real-time recommendation system with continuously updated patterns
- **Causal Analysis** — Investigate whether recommendations actually causally influence purchase decisions

---

## References

1. Chen, D. (2012). *Online Retail II [Dataset]*. UCI Machine Learning Repository. https://doi.org/10.24432/C5CG6D

2. Pei, J., Han, J., Mortazavi-Asl, B., Wang, J., Pinto, H., Chen, Q., & Dayal, U. (2004). Mining sequential patterns by pattern-growth: The PrefixSpan approach. *IEEE Transactions on Knowledge and Data Engineering*, 16(11), 1424–1440. https://www.cs.sfu.ca/~jpei/publications/span.pdf

3. Goel, L., & Mallick, C. (2015). Customer purchasing behavior using sequential pattern mining technique. *International Journal of Computer Applications*, 119(1), 1–5. https://www.ijcaonline.org/archives/volume119/number1/21032-2939

---

*This research was conducted as part of coursework at Air University Islamabad, Department of Computer Science.*
