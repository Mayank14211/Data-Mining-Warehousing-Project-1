# Online Retail Analytics and Customer Churn Analysis

This project analyzes the UCI Online Retail dataset in
`customer-churn-and-analysis.ipynb`. The notebook combines exploratory data
analysis, data cleaning, a star-schema data warehouse, OLAP operations,
customer segmentation, market-basket analysis, and a temporal churn model.

## Dataset

- Source: UCI Online Retail dataset
- Kaggle dataset: `carrie1/ecommerce-data`
- Expected file: `data.csv`
- Encoding: `ISO-8859-1`
- Original size: 541,909 rows and 8 columns
- Columns: `InvoiceNo`, `StockCode`, `Description`, `Quantity`,
  `InvoiceDate`, `UnitPrice`, `CustomerID`, and `Country`

## Notebook Workflow

1. **Setup and loading**
   - Locates `data.csv` from `DATA_PATH`, a Kaggle input path, or the current
     directory.
   - Loads invoice data with invoice and stock identifiers preserved as strings.

2. **Data exploration**
   - Reviews data types, descriptive statistics, missing values, and duplicates.
   - The original data contains 5,268 duplicate records.

3. **Cleaning**
   - Removes duplicate records.
   - Converts dates and numeric fields.
   - Standardizes stock codes and fills missing descriptions where possible.
   - Removes non-product stock codes and identifies cancelled invoices.
   - Nets matching cancellation quantities against earlier purchases.

4. **Outlier detection**
   - Uses IQR flags for unit price and quantity.
   - Outlier rows remain in the fact data because large orders and high-value
     items may represent valid transactions.

5. **Star schema**
   - Creates a fact table and dimensions for customer, product, date, and country.
   - Assigns customer key `0` to transactions with an unknown customer ID.

6. **OLAP operations**
   - Demonstrates roll-up, drill-down, slice, dice, and pivot operations.

7. **Sales analysis**
   - Reports monthly and yearly revenue.
   - Identifies top products and countries.
   - Visualizes invoice activity by weekday and hour.

8. **Customer analysis**
   - Creates R, F, M scores for recency, frequency, and monetary value.
   - Assigns customers to business segments such as Champions, Loyal, and At Risk.
   - Applies standardized log-transformed features and K-Means clustering.
   - Measures cohort retention by month.

9. **Market-basket analysis**
   - Evaluates pairs among the 200 most frequently purchased products.
   - Reports support, confidence, and lift.

10. **Churn prediction**
    - Defines churn as no purchase in the 90 days after a cutoff date.
    - Builds features only from transactions occurring before the cutoff.
    - Compares baseline, logistic regression, random forest, and histogram
      gradient boosting models using stratified cross-validation.
    - Performs an out-of-time validation check and creates customer-level churn scores.

11. **Data quality checks and export**
    - Checks missing values and duplicate rows in generated tables.
    - Exports the main analytical results as CSV files.

## Verified Results

The executed notebook reports:

- Gross product revenue: **10,247,219.32**
- Net product revenue after matched cancellations: **9,804,875.19**
- Clean sales rows: **519,815**
- Matched cancelled units: **246,441** of 267,711
- Cancellation rows without a customer ID: **162**
- Unit-price IQR outliers: **35,289**
- Quantity IQR outliers: **26,653**
- Market-basket rules meeting the thresholds: **555**
- Churn cutoff: **2011-09-11**
- Customers in the churn dataset: **3,363**
- Churned customers: **1,448**
- Active customers: **1,915**
- Selected model: **Logistic Regression**
- Test accuracy: **67%**
- Holdout-churned precision/recall: **60% / 73%**
- Holdout active precision/recall: **76% / 62%**
- Out-of-time ROC-AUC: **0.7434**
- Out-of-time accuracy: **0.6711**

The star schema contains:

| Table | Rows | Columns |
|---|---:|---:|
| `fact_sales` | 519,815 | 10 |
| `dim_customer` | 4,325 | 3 |
| `dim_product` | 3,793 | 4 |
| `dim_date` | 374 | 10 |
| `dim_country` | 38 | 2 |

The best model is selected using training cross-validation; the holdout and
temporal checks provide separate estimates of generalization performance.

## Requirements

The notebook uses Python with:

- pandas
- NumPy
- Matplotlib
- seaborn
- SciPy
- IPython
- scikit-learn

Install the packages in a Python environment, for example:

```bash
pip install pandas numpy matplotlib seaborn scipy ipython scikit-learn jupyter
```

The saved notebook metadata identifies Python 3.13.16. A compatible 64-bit
Python environment is recommended.

## Running the Notebook

Place `data.csv` beside the notebook or set its path:

```bash
export DATA_PATH=/absolute/path/to/data.csv
jupyter notebook customer-churn-and-analysis.ipynb
```

On Windows PowerShell:

```powershell
$env:DATA_PATH="C:\absolute\path\to\data.csv"
jupyter notebook customer-churn-and-analysis.ipynb
```

The notebook can also run on Kaggle when `data.csv` is available in the
configured dataset input path.

## Exported CSV Files

The final notebook section writes eight files to `/kaggle/working` on Kaggle or
to `output/` locally:

- `fact_sales.csv`
- `dim_customer.csv`
- `dim_product.csv`
- `dim_date.csv`
- `dim_country.csv`
- `customer_rfm.csv`
- `market_basket_rules.csv`
- `churn_scores.csv`
