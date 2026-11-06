# Warehouse & Retail Stocks – Data Science Mini Project

**Student:** Srinithi Kanniyappan  |  **Batch:** DSBO4  |  **Submitted:** 06/11/2026

Exploratory analysis and predictive modelling of warehouse and retail sales data to understand product demand, supplier performance and stock movement between warehouses and retail stores.

---

## 1. Problem Statement

A warehouse retail business handles thousands of products from many suppliers. It needs to know which products and suppliers drive sales and how stock moves from warehouse to retail, so it can avoid both excess inventory and stock-outs.

**Goal:** analyse historical sales data to find demand patterns, compare supplier and item-type performance, study the relationship between retail sales, retail transfers and warehouse sales, and suggest data-driven ways to improve inventory allocation.

### Objectives
1. Sales analysis – monthly/yearly trends
2. Product analysis – top and least-performing items
3. Supplier analysis – suppliers with the highest sales volumes
4. Category analysis – sales across item types
5. Inventory optimization – retail sales vs. transfers vs. warehouse sales
6. Business recommendations – better stock allocation, less waste

---

## 2. Dataset

- **Source:** [Warehouse and Retail Sales – data.gov / Montgomery County, MD](https://catalog.data.gov/dataset/warehouse-and-retail-sales)
- **Loaded from:** a hosted copy of `WareHouse Retail.xlsx` on GitHub (see the notebook)
- **Size:** 33,364 rows × 12 columns raw (3 empty `Unnamed` columns dropped → 9 columns)
- **Period covered:** months 6–8 of 2026 (a single year, so `YEAR` was dropped)

| Column | Type | Description |
|---|---|---|
| YEAR, MONTH | Numeric | Reporting period |
| SUPPLIER | Categorical | Product supplier (335 unique) |
| ITEM CODE | Categorical | Product code |
| ITEM DESCRIPTION | Categorical | Product name |
| ITEM TYPE | Categorical | Product category (8 types, e.g. wine, beer, liquor) |
| RETAIL SALES | Numeric | Units/amount sold at retail |
| RETAIL TRANSFERS | Numeric | Stock transferred to retail |
| WAREHOUSE SALES | Numeric | Units/amount sold from the warehouse (**target variable**) |

---

## 3. Workflow

### Data cleaning
- Dropped empty `Unnamed` columns
- Checked duplicates → **0** found
- Removed rows with missing values (23 in `SUPPLIER`, 2 in `RETAIL SALES`) → **33,341** rows remain
- Dropped constant `YEAR` column

### Exploratory Data Analysis
- Correlation: `RETAIL SALES` and `RETAIL TRANSFERS` are very strongly correlated (**0.96**); each correlates ~0.5 with `WAREHOUSE SALES`
- Seven visualisations:
  1. Pie chart – warehouse sales share by item type
  2. Bar chart – top 10 suppliers by warehouse sales
  3. Scatter plot – retail vs. warehouse sales
  4. Histogram – warehouse sales distribution
  5. Count plot – records per item type
  6. Box plot – warehouse sales, retail sales, retail transfers
  7. Histograms – the three sales variables

### Preprocessing
- **Outliers:** detected with the IQR method (thousands flagged per column) and treated by capping (clipping) at the bounds
- **Skewness:** compared original vs. log and square-root transforms. All three sales variables are highly right-skewed (≈ 20–26 originally)

---

## 4. Models and Results

Features: `RETAIL SALES`, `RETAIL TRANSFERS` → Target: `WAREHOUSE SALES` (80/20 train–test split, `random_state=42`).

| Model | MAE | RMSE | R² |
|---|---|---|---|
| Linear Regression | – | ≈ 221.1 | 0.122 |
| Ridge Regression (α=1) | 41.23 | 221.12 | 0.122 |
| Lasso Regression (α=1) | 40.98 | 220.98 | 0.123 |
| Polynomial Regression (degree 2) | 38.77 | 219.04 | 0.139 |

The notebook selects **Linear Regression** as the best model for this dataset.

### Other ML task types evaluated

| Task | Method | Result |
|---|---|---|
| Regression | Linear Regression | MAE 37.37, RMSE 189.60, R² 0.082 |
| Classification (Low/Medium/High warehouse sales) | Decision Tree | Accuracy 0.570, F1 0.544, ROC-AUC 0.674 |
| Clustering | K-Means (k=3) | Silhouette 0.915, Davies-Bouldin 0.800 |
| Time series | Exponential Smoothing | Not run – too few monthly observations |
| Recommendation | Nearest Neighbours | Similar records found; no ground-truth labels, so no precision/recall |

---

## 5. Key Findings

- Retail sales and retail transfers move together almost one-for-one.
- Sales values are heavily skewed, with many zero or near-zero records and a few very large values.
- Retail sales and transfers explain only a small share of warehouse sales (R² ≈ 0.08–0.14), so other factors (item type, supplier, seasonality) likely matter.

### Limitations
- Only about three months of data, so trends and forecasting are limited
- Only two input features were used; categorical features (supplier, item type) are not yet encoded in the models
- Negative values (returns/adjustments) exist and were not investigated separately
- Outlier capping affects a large share of rows, since sales data is naturally skewed

### Possible next steps
- Encode `ITEM TYPE` and `SUPPLIER` and add them as features
- Try tree-based models (Random Forest, Gradient Boosting) and a log-transformed target
- Add more months of data to support time-series forecasting
- Cross-validate instead of using a single train–test split

---

## 6. Tech Stack

Python · pandas · NumPy · Matplotlib · Seaborn · scikit-learn · statsmodels

## 7. How to Run

```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels openpyxl
jupyter notebook Srinithi_DS_Mini_Project_Warehouse_and_Retail_.ipynb
```

Run all cells in order. The dataset is read directly from the GitHub URL in the notebook, so an internet connection is required.

## 8. Project Structure

```
├── Srinithi_DS_Mini_Project_Warehouse_and_Retail_.ipynb   # Full analysis and models
└── README.md
```
