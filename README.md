# Superstore Sales & Customer Analytics — Capstone Project

## Project Overview

This capstone project delivers an end-to-end data analytics and machine learning pipeline built on the **Sample Superstore** retail dataset. Starting from raw transactional data, the project works through data cleaning, feature enrichment, exploratory data analysis (EDA), sales visualisation, customer segmentation, and a binary churn-prediction model — producing actionable business insights at each stage.

---

## Business Problem

Retail businesses accumulate large volumes of transactional data but often struggle to extract timely, decision-ready insights. Two critical challenges addressed here are:

1. **Revenue concentration** — understanding which products, categories, regions, and customer segments drive (or erode) profitability.
2. **Customer churn** — identifying customers at risk of disengaging before they are lost, so retention efforts can be targeted efficiently.

---

## Objectives

- Clean and enrich raw transactional data to make it analysis-ready.
- Conduct exploratory analysis to surface key sales and profit drivers.
- Segment customers by value and behaviour.
- Build and evaluate a Logistic Regression model to predict customer churn.
- Apply a **historical snapshot** methodology to prevent target leakage during model training.

---

## Dataset Information

| Property | Detail |
|---|---|
| **File** | `Data/Raw/sample_-_superstore.xls` |
| **Domain** | US Retail — Office Supplies, Furniture, Technology |
| **Records** | ~9,000+ order line items |
| **Time span** | 2023 (order dates) |
| **Granularity** | One row per order line item |

### Key Columns

| Column | Description |
|---|---|
| `order_id` | Unique order identifier |
| `order_date` / `ship_date` | Transaction and fulfilment dates |
| `ship_mode` | Shipping tier (Standard Class, etc.) |
| `customer_id` / `customer_name` | Customer identifiers |
| `segment` | Customer business segment (Consumer, Corporate, Home Office) |
| `region` / `state/province` / `city` | Geographic hierarchy |
| `category` / `sub-category` / `product_name` | Product hierarchy |
| `sales` / `quantity` / `discount` / `profit` | Transaction financials |
| `order_month` / `order_year` | Derived time fields |
| `revenue_category` | Binned sales tier (Low → Very High) |
| `customer_value` | Total spend per customer (enriched) |
| `customer_segment` | RFM-style value tier (Low / Mid / High Value) |

**Dataset source:** [_placeholder — add Kaggle / UCI / official link here_]

---

## Data Cleaning & Preprocessing

Performed in the data preparation stage; cleaned file saved to `Data/Cleaned/superstore_cleaned.csv`.

- Standardised column names (lower-case, snake_case).
- Parsed `order_date` and `ship_date` to `datetime` types.
- Handled missing values and removed duplicate rows.
- Validated and cast numeric columns (`sales`, `profit`, `quantity`, `discount`).

### Feature Enrichment

Additional features derived and saved to `Data/Cleaned/superstore_cleaned_enriched.csv`:

| Feature | Description |
|---|---|
| `order_month` | Month extracted from `order_date` |
| `order_year` | Year extracted from `order_date` |
| `revenue_category` | Sales amount bucketed into Low / Medium / High / Very High |
| `customer_value` | Aggregate lifetime spend per customer |
| `customer_segment` | Rule-based value tier assigned from `customer_value` |

---

## EDA & Key Analyses

Exploratory analysis covers five dimensions, each backed by a saved chart.

| Analysis | Output file |
|---|---|
| Monthly sales trend | `Outputs/Charts/monthly_sales_trend.png` |
| Sales by product category | `Outputs/Charts/category_sales.png` |
| Sales by customer segment | `Outputs/Charts/customer_segment_sales.png` |
| Sales by region | `Outputs/Charts/regional_sales.png` |
| Top products by profit | `Outputs/Charts/top_products_profit.png` |

Key themes investigated:
- Seasonal revenue patterns across months.
- Relative contribution of Furniture, Office Supplies, and Technology.
- Regional performance disparities (Central, East, South, West).
- High-discount SKUs and their impact on profit margins.
- Which customer segments (Consumer, Corporate, Home Office) generate the most revenue.

---

## Machine Learning Approach

### Customer Segmentation

Customers are grouped into **Low Value**, **Mid Value**, and **High Value** tiers based on their total historical spend (`customer_value`). This feeds directly into the churn model as a behavioural feature.

### Churn Prediction — Logistic Regression

**Target definition:** A customer is labelled as *churned* if they have not placed any order in a defined trailing window relative to the snapshot date.

**Historical snapshot approach:**  
To prevent **target leakage**, the feature set for each customer is computed exclusively from transactions that occurred *before* the snapshot cutoff date. Labels are derived from activity *after* the cutoff. This ensures the model only sees information that would genuinely be available at prediction time.

**Feature engineering highlights:**
- Recency (days since last order relative to snapshot)
- Frequency (number of orders before snapshot)
- Monetary value (`customer_value`)
- Customer segment tier
- Preferred category and region

**Pipeline:**
1. Build historical snapshots per customer.
2. Engineer features from pre-cutoff window.
3. Assign churn labels from post-cutoff window.
4. Train/test split (stratified).
5. Fit `LogisticRegression` (scikit-learn).
6. Evaluate on held-out test set.

---

## Model Evaluation

The model is assessed using four metrics to give a balanced view of performance:

| Metric | Description |
|---|---|
| **Accuracy** | Overall fraction of correct predictions |
| **Precision** | Of predicted churners, how many actually churned |
| **Recall** | Of actual churners, how many were correctly identified |
| **Confusion Matrix** | Full breakdown of TP / TN / FP / FN |

> **Results:** _[placeholder — run the notebook and record your Accuracy, Precision, Recall, and Confusion Matrix values here]_

---

## Technologies & Libraries Used

| Library | Version (installed) | Purpose |
|---|---|---|
| `pandas` | 3.0.6 | Data manipulation and cleaning |
| `numpy` | 2.5.3 | Numerical operations |
| `matplotlib` | — | Charting and visualisation |
| `scikit-learn` | — | Machine learning (Logistic Regression, metrics) |
| `xlrd` | 2.0.2 | Reading legacy `.xls` files |
| `openpyxl` | — | Reading/writing `.xlsx` files |
| Python | 3.14 | Runtime |

---

## Project Folder Structure

```
IBM project/
│
├── Data/
│   ├── Raw/
│   │   └── sample_-_superstore.xls          # Original dataset
│   └── Cleaned/
│       ├── superstore_cleaned.csv            # After data cleaning
│       └── superstore_cleaned_enriched.csv   # After feature enrichment
│
├── Notebooks/                                # Jupyter notebooks (add here)
│
├── Outputs/
│   ├── Charts/
│   │   ├── category_sales.png
│   │   ├── customer_segment_sales.png
│   │   ├── monthly_sales_trend.png
│   │   ├── regional_sales.png
│   │   └── top_products_profit.png
│   ├── Model/                                # Saved model artefacts
│   └── Tables/                               # Exported summary tables
│
├── Reports/                                  # Final reports / presentations
│
├── requirements.txt                          # Python dependencies
└── README.md
```

---

## Setup & Run Instructions

### 1. Clone / download the repository

```bash
git clone <repository-url>
cd "IBM project"
```

### 2. Create and activate a virtual environment

```bash
# Windows
python -m venv .venv
.venv\Scripts\activate

# macOS / Linux
python -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the notebooks

Open the notebooks inside `Notebooks/` in order:

| Step | Notebook | Description |
|---|---|---|
| 1 | `01_data_cleaning.ipynb` | Clean raw data |
| 2 | `02_eda.ipynb` | Exploratory analysis & charts |
| 3 | `03_customer_segmentation.ipynb` | Customer value tiers |
| 4 | `04_churn_prediction.ipynb` | Model training & evaluation |

> _Notebook filenames are placeholders — rename to match your actual files._

---

## Key Outputs

| Output | Location | Description |
|---|---|---|
| Cleaned dataset | `Data/Cleaned/superstore_cleaned.csv` | Validated, typed, de-duplicated |
| Enriched dataset | `Data/Cleaned/superstore_cleaned_enriched.csv` | Cleaned + engineered features |
| Monthly sales chart | `Outputs/Charts/monthly_sales_trend.png` | Revenue over time |
| Category sales chart | `Outputs/Charts/category_sales.png` | Furniture vs Office Supplies vs Technology |
| Regional sales chart | `Outputs/Charts/regional_sales.png` | Performance by US region |
| Segment sales chart | `Outputs/Charts/customer_segment_sales.png` | Consumer / Corporate / Home Office |
| Top products chart | `Outputs/Charts/top_products_profit.png` | Most and least profitable SKUs |
| Churn model | `Outputs/Model/` | Serialised Logistic Regression model |

---

## Future Improvements

- **Advanced ML models** — evaluate Random Forest, XGBoost, or gradient boosting for improved churn prediction accuracy.
- **Hyperparameter tuning** — apply `GridSearchCV` or `RandomizedSearchCV` to optimise the Logistic Regression regularisation parameter.
- **Class imbalance handling** — if churned customers are a minority, apply SMOTE or class weighting.
- **Richer RFM segmentation** — replace rule-based tiers with K-Means clustering for data-driven segments.
- **Time-series forecasting** — build a sales forecasting model (e.g., Prophet or ARIMA) on top of the monthly trend data.
- **Interactive dashboard** — surface insights via a Streamlit or Dash application.
- **Automated pipeline** — wrap the full workflow in an Airflow DAG or a Makefile for reproducible execution.

---

## Dataset Source

> _[placeholder — add the full dataset URL or citation here, e.g., Kaggle Superstore dataset link]_

---

*Capstone project — IBM Data Analytics / Data Science Programme*
