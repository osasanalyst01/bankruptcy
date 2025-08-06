## 📁 Dataset Overview

The dataset consists of over 7,000 company-year observations, each containing detailed financial data for up to three years per company. Each company has a `status_label` that identifies whether it remained operational (`0 = alive`) or failed (`1 = bankrupt`).

Start ups (less than three years) or companies we had less than three years of their financial data were not included in this project.

Key features include:

- 'current_assets','cost_of_goods_sold', depreciation_amort','ebitda','inventory', net_income','total_receivables', 'market_value', 'net_sales', 'total_assets','long_term_debt', 'ebit',
    'gross_profit', 'current_liabilities', 'retained_earnings', 'total_revenue', 'total_liabilities', 'operating_expenses'
---

## 🔧 Feature Engineering

Since most companies have financial data spanning three years, we engineered trend-based features to reflect their financial trajectory over time. This involved:

### 1. Sorting and Structuring
- Sorted data by `company_name` and `year` to extract the latest three years of data.
- Assigned a `relative_year` variable (`1 = most recent`, `2 = previous year`, etc.).

### 2. Financial Ratio Construction

| Feature Name              | Description                                                                 | Formula                                               |
|--------------------------|-----------------------------------------------------------------------------|-------------------------------------------------------|
| Gross Profit Margin       | Proportion of revenue left after deducting COGS                           | `gross_profit / net_sales`                            |
| Net Profit Margin         | Proportion of revenue converted into profit                               | `net_income / net_sales`                              |
| Return on Assets (ROA)    | Efficiency in using assets to generate profits                            | `net_income / total_assets`                           |
| Return on Equity (ROE)    | Profitability based on shareholder equity                                 | `net_income / (total_assets - total_liabilities)`     |
| Liquidity Ratio           | Ability to cover short-term obligations                                   | `current_assets / current_liabilities`                |
| Quick Ratio               | Immediate short-term liquidity without relying on inventory               | `(current_assets - inventory) / current_liabilities`  |
| Debt to Asset Ratio       | Proportion of assets financed by liabilities                              | `total_liabilities / total_assets`                    |
| Debt to Equity Ratio      | Level of financial leverage                                                | `total_liabilities / (total_assets - total_liabilities)` |
| Asset Turnover            | Efficiency of using assets to generate sales                              | `net_sales / total_assets`                            |

### 3. Trend Features (Delta)
For each ratio and key metric, we computed deltas:
- **Delta from Year 3 to Year 1**
- **Delta from Year 2 to Year 1**

These allow us to detect financial deterioration or improvement over time.

### 4. Market Value Categorization
Companies were categorized by size:
- `3 = Large` (above 75th percentile of `market_value_year1`)
- `2 = Medium` (between 25th–75th percentile)
- `1 = Small` (below 25th percentile)

---

## 🧹 Data Cleaning & Preprocessing

- **Missing values** were dropped after pivoting.
- **Highly correlated features** were identified and dropped to avoid multicollinearity:
  - `quick_ratio`, `gross_profit_margin`, and all delta-from-year-2 columns were removed.
- `status_label` was encoded (`0 = alive`, `1 = failed`).
- **Imbalanced classes** were oversampled using `SMOTE` to improve model learning.

---

## 📊 Exploratory Data Analysis

- Companies with **negative retained earnings** and **negative net income** showed a high likelihood of failure.
- Surprisingly, companies with **higher COGS** were more likely to survive — indicating ongoing business activity and sales.
- Correlation heatmaps showed clusters between solvency and profitability indicators.

---

## 🤖 Model Building

We trained a **Decision Tree and Random Forest Classifier** using the processed features. Model evaluation was done using:

- **Confusion Matrix**
- **Precision, Recall, F1-Score**
- **ROC-AUC**

---

## ✅ Model Evaluation

 ## 📈 Model Performance Comparison

| Model             | Precision | Recall  | F1 Score | Accuracy | AUC      |
|------------------|-----------|---------|----------|----------|----------|
| Decision Tree CV | 0.9065    | 0.9996  | 0.9508   | 0.9482   | 0.9484   |
| Random Forest CV | 0.9470    | 1.0000  | 0.9728   | 0.9720   | 0.9999   |



