# Sales Performance and Product Returns Diagnostic Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-0078D4)
![Power Query](https://img.shields.io/badge/Power%20Query-ETL-217346)

## Table of Contents

- [Executive Overview](#executive-overview)
- [Analytical Objectives](#analytical-objectives)
- [Data Architecture](#data-architecture)
- [Power Query Transformations](#power-query-transformations)
- [DAX Measures](#dax-measures)
- [Dashboard Layout & Interactivity](#dashboard-layout--interactivity)
- [Key Business Inferences](#key-business-inferences)

---

## Executive Overview

A commercial sales and returns analytics dashboard built in **Power BI Desktop**. It focuses on diagnosing product return behaviour, post-sale margin erosion, geographic return concentration, and shipping lead times.

## Analytical Objectives

1. Monitor core commercial metrics: gross sales, order count, return quantity, and return percentage.
2. Isolate causes of returns by analysing product categories, markets, and fulfilment timelines.
3. Identify customers and product lines with recurring returns to reduce handling losses.

---

## Data Architecture

The model is a **Star Schema** centred on the transactional `Orders` table, linked to dimensions through one-to-many relationships.

| Table | Type | Description |
|-------|------|-------------|
| `Orders` | Fact | Sales transactions, shipping data, profit, discounts, and keys (`OrderID`, `CustomerID`, `ProductID`, `OrderDate`) |
| `Product` | Dimension | Product IDs, categories, and sub-categories |
| `People` | Dimension | Regional personnel mapped to customer accounts |
| `Returns` | Dimension / Bridge | Return flags and order-level verification records |
| `Date Table` | Dimension | Custom DAX-generated calendar for temporal and quarterly analysis |

---

## Power Query Transformations

- **Data cleansing:** Verified primary keys across master tables and removed duplicates to prevent cardinality errors.
- **Fulfilment metric:** Added a calculated column `Shipping Days` = `[Ship Date] - [Order Date]`, cast as a whole number.
- **Geographic categorisation:** Assigned Power BI data categories to `City`, `State`, and `Country/Region` for map visuals.
- **Formatting:** Set `Sales`, `Profit`, and `Shipping Cost` to two decimal places.

---

## DAX Measures

All measures are stored in the `Orders` fact table.

```dax
Total Orders = DISTINCTCOUNT(Orders[Order ID])
```

```dax
Total Sales = SUM(Orders[Sales])
```

```dax
Total Profit = SUM(Orders[Profit])
```

```dax
Total Returns =
CALCULATE(
    COUNT('Returns'[Returned]),
    'Returns'[Returned] = "Yes"
)
```

```dax
Return Percentage = DIVIDE([Total Returns], [Total Orders], 0)
```

```dax
Returns Per Customer = DIVIDE([Total Returns], [Total Customers], 0)
```

---

## Dashboard Layout & Interactivity

| # | Component | Description |
|---|-----------|-------------|
| 1 | **Executive KPI Cards** | Total Sales, Total Orders, Total Returns, and Return Percentage |
| 2 | **Year Slicer** | Dropdown on `'Date Table'[Year]`, defaulting to "Select All" |
| 3 | **Market & Category Return Analysis** | Clustered bar of `Return Percentage` by `Market`, split by product `Category` |
| 4 | **Sales & Profit Trend** | Dual-axis monthly revenue vs. profit to detect margin erosion |
| 5 | **High-Return Accounts** | Table of the Top 3 customers by return volume |
| 6 | **Drill-Through (`Details` page)** | Right-click a `Country` to view its sales, order count, and return percentage |

---

## Key Business Inferences

- **Category return disparity:** Returns concentrate in specific categories, with Office Supplies and Technology showing high return frequency across high-volume territories.
- **Regional differences:** Developed markets show different category return patterns from emerging regions, where Furniture and Office Supplies hold larger relative return shares.
- **Fulfilment lead time:** Longer `Shipping Days` do not consistently lead to higher return rates, suggesting product-expectation mismatches, not shipping delays, drive returns.

---

## Tech Stack

- Microsoft Power BI Desktop
- Power Query (M)
- DAX

## Author

**Sarvesh** — PGDM, Jaipuria Institute of Management, Indore
