# AtliQo Bank Credit Card Launch: EDA and A/B Test

A two-phase data analysis project for a new AtliQo Bank credit card. Phase 1 cleans the data and explores it to pick a target customer group. Phase 2 plans a trial campaign and tests statistically whether the new card increases average transaction amounts.

## Project Structure

| File | Description |
|------|-------------|
| `rhuma_phase_1_bank.ipynb` | Phase 1: data import, cleaning, EDA and target-group selection |
| `rhuma_phase_2_atliqo_bank.ipynb` | Phase 2: A/B test design, hypothesis testing and confidence interval |
| `AtliQo Bank Project Task Breakdown.docx` | Task breakdown by phase |

## Data

The notebooks expect these files (not included in this repo):

- `dataset/customers.csv`, `dataset/credit_profiles.csv` and `dataset/transactions.csv` for Phase 1. They can also be read from a local MySQL database, `e_master_card`.
- `data/avg_transactions_after_campaign.csv` for Phase 2: daily average transaction amounts for the control and test groups.
- `analysis.png`, an image embedded in the Phase 2 notebook.

| Table | Contents |
|-------|----------|
| Customers | `cust_id`, age, gender, location, occupation, marital status, annual income |
| Credit profiles | credit score, credit utilisation, outstanding debt, credit limit |
| Transactions | transaction amount, platform, product category, payment type |

## Phase 1: Data Cleaning and EDA

**Customers**
- Missing and implausible annual income (< $100) replaced with the occupation-wise median.
- Ages outside 15–80 replaced with the occupation-wise median age.
- Customers grouped into age bands: 18–25, 26–48 and 49–65.
- Income analysed by occupation, gender, location and marital status.

**Credit profiles**
- Duplicate `cust_id` rows removed.
- Missing credit limits filled with the most common limit within the customer's credit-score range.
- Outstanding debt capped at the credit limit.
- Correlations checked between credit score, utilisation, debt, limit, income and age.

**Transactions**
- Missing `platform` filled with the mode.
- Zero-value transactions replaced with the median for Amazon, Electronics and Credit Card transactions.
- Outliers found with an IQR rule and replaced with the per-category mean.
- Payment type, platform and product category compared across age groups.

**Finding: the untapped segment is customers aged 18–25**
- They are about 25% of the customer base.
- Their average annual income is under $50k.
- They have limited credit history, so their credit scores and limits are low.
- They use credit cards less than other groups.
- Their top product categories are Electronics, Fashion & Apparel and Beauty & Personal Care.

## Phase 2: A/B Test

**Pre-campaign**
- Sample size calculated with `statsmodels` (`tt_ind_solve_power`), at α = 0.05 and power = 0.8.
- A minimum detectable effect size of 0.4 needs about 100 customers, which fit the business budget.
- About 246 customers fall in the 18–25 group. 100 were targeted, of whom about 40 (a ~40% conversion rate) started using the card.
- The test group is those 40 customers. A control group of 40 customers is drawn from outside the 100, and both groups are compared on average transaction amount.
- The campaign ran for two months, from 09-10-23 to 11-10-23.

| Effect size | Customers needed per group |
|-------------|---------------------------:|
| 0.1 | 1571 |
| 0.2 | 393 |
| 0.3 | 175 |
| 0.4 | 99 |
| 0.5 | 64 |

**Post-campaign: one-sided two-sample Z-test**
- H₀: the new card's average transaction amount is no higher than the old card's.
- H₁: the new card's average transaction amount is higher.
- The test uses 62 daily observations per group. The notebook computes the Z-score by hand and checks it with `statsmodels.stats.ztest`.

| Metric | Control | Test |
|--------|--------:|-----:|
| Mean daily avg. transaction | 221.18 | 235.98 |
| Std. deviation | 21.36 | 36.66 |

- **Z-score:** 2.75, against a critical value of 1.645.
- **p-value:** 0.003, below α = 0.05, so H₀ is rejected.
- **95% confidence interval for the test group:** roughly 226.86 to 245.11 average spend.

**Conclusion:** the new card gives a statistically significant increase in average transaction amount among 18–25 year olds.

## Tech Stack

Python, pandas, NumPy, seaborn, matplotlib, SciPy, statsmodels, mysql-connector-python (optional, for the MySQL import) and Jupyter.

## Getting Started

```bash
pip install pandas numpy seaborn matplotlib scipy statsmodels mysql-connector-python jupyter
jupyter notebook
```

Place the data files in `dataset/` and `data/` next to the notebooks, then run Phase 1 followed by Phase 2.

## Author

RHuma
