# Sales Data Analytics

A Python-based data analytics pipeline built in Google Colab that ingests raw sales CSV data, performs end-to-end ETL (Extract, Transform, Load), validates data quality, computes key sales KPIs, and visualizes trends.

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Getting Started](#getting-started)
- [Pipeline Walkthrough](#pipeline-walkthrough)
- [Key Metrics & Outputs](#key-metrics--outputs)
- [Visualizations](#visualizations)
- [Data Schema](#data-schema)

---

## Overview

This notebook processes a raw sales dataset (`sales_raw.csv`) through a structured pipeline:

1. **Data Ingestion** — Upload and load raw CSV data
2. **Exploratory Data Analysis (EDA)** — Inspect shape, types, nulls, and duplicates
3. **Data Cleaning & Transformation** — Fix types, handle nulls/zeros/negatives, remove duplicates
4. **Fact Table Construction** — Build a clean `fact_sales` table with a computed `revenue` column
5. **ETL Validation** — Assert data integrity post-transformation
6. **KPI Computation** — Calculate total revenue, order count, and average order value
7. **Visualization** — Plot monthly trends, product performance, and distribution charts

---

## Project Structure

```
Sales_data_analytics.ipynb   # Main Jupyter Notebook
sales_raw.csv                # Input raw sales data (user-supplied)
README.md                    # Project documentation
```

---

## Requirements

- Python 3.x
- Google Colab (recommended) or local Jupyter environment
- Libraries:
  - `pandas`
  - `matplotlib`

Install locally if needed:
```bash
pip install pandas matplotlib
```

---

## Getting Started

1. Open `Sales_data_analytics.ipynb` in [Google Colab](https://colab.research.google.com/) or Jupyter.
2. Upload your `sales_raw.csv` file when prompted (Cell 1 uses `google.colab.files.upload()`).
3. Run all cells in order from top to bottom.

> **Note:** If running locally (not in Colab), replace Cell 1 with:
> ```python
> import pandas as pd
> df = pd.read_csv("sales_raw.csv")
> ```

---

## Pipeline Walkthrough

### 1. Data Ingestion
- Uploads `sales_raw.csv` via the Colab file uploader.
- Loads the file into a pandas DataFrame.

### 2. Exploratory Data Analysis (EDA)
- Inspects shape, column types, and null counts (`df.info()`, `df.isnull().sum()`).
- Checks for duplicate rows and descriptive statistics.

### 3. Data Cleaning
| Issue | Fix Applied |
|---|---|
| `transaction_date` not datetime | Coerced with `pd.to_datetime()` |
| `qty` not numeric | Coerced with `pd.to_numeric()` |
| `unit_price` has `$` prefix | Stripped and coerced to float |
| Missing critical values | Rows dropped on `transaction_date`, `qty`, `unit_price` |
| Remaining nulls in numeric cols | Imputed with column median |
| Zero quantities / prices | Replaced with median |
| Negative quantities / prices | Rows dropped |
| Duplicate `sale_id` entries | Deduplicated, keeping first occurrence |

### 4. Fact Table Construction
Produces a clean `fact_sales` table with the following columns:

| Column | Type | Description |
|---|---|---|
| `sale_id` | string | Unique transaction identifier |
| `transaction_date` | datetime | Date of the sale |
| `product_code` | string | Product identifier |
| `cust_id` | string | Customer identifier |
| `qty` | int | Quantity sold |
| `unit_price` | float | Price per unit |
| `revenue` | float | Computed as `qty × unit_price` |

### 5. ETL Validation
- Asserts no zero or negative revenue values exist.
- Asserts record count did not increase post-ETL.
- Prints an ETL Impact Summary showing raw vs. clean record counts and retention percentage.

### 6. KPI Computation
Computes and prints:
- **Total Revenue**
- **Total Orders**
- **Average Order Value**
- **Percentage of Records Retained**

---

## Key Metrics & Outputs

After running the notebook, the following are printed to the console:

```
ETL Impact Summary
------------------
Number of raw records: <raw_count>
Number of records after ETL: <etl_count>
Percentage of records retained: XX.XX%

Sales KPIs
----------
Total Revenue: XXXXXX.XX
Total Orders: XXXX
Average Order Value: XXX.XX
Percentage of Records Retained: XX.XX%
```

---

## Visualizations

The notebook generates five charts:

| Chart | Description |
|---|---|
| **Monthly Revenue Trend** | Line chart of total revenue aggregated by month |
| **Monthly Order Volume** | Bar chart of order count per month |
| **Top 10 Products by Revenue** | Bar chart of highest-grossing product codes |
| **Order Value Distribution** | Histogram of individual transaction revenue values |
| **ETL Impact: Record Count** | Bar chart comparing raw vs. cleaned record counts |

---

## Data Schema

The input file `sales_raw.csv` is expected to contain at minimum the following columns:

| Column | Expected Format | Notes |
|---|---|---|
| `sale_id` | string / int | Unique per transaction |
| `transaction_date` | date string | Any parseable date format |
| `product_code` | string | Product identifier |
| `cust_id` | string | Customer identifier |
| `qty` | numeric | May contain nulls or zeros |
| `unit_price` | numeric or `$X.XX` | May contain `$` prefix |

> Additional columns in the CSV will be ignored after the fact table is built.
