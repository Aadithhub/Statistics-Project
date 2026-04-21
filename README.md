# Data-Driven Credit Card Launch Strategy Using Statistical Modelling

**Domain:** Financial Analytics / Marketing Analytics  
**Course:** Statistical Modelling — Term Project  
**Author:** Aadith P | Department of Artificial Intelligence

---

## Overview

This project presents an end-to-end statistical modelling pipeline designed to support the strategic launch of a new credit card product. Rather than relying on broad demographic intuition, the pipeline uses rigorous statistical methods to identify the right target audience, validate campaign effectiveness, detect spending heterogeneity across income groups, and discover actionable customer sub-segments — all without requiring any labelled outcome data.

The analysis is built on three synthetic but realistic datasets covering customer demographics, credit profiles, and transaction histories across 500 customers, designed to closely replicate real-world retail banking data.

---

## Pipeline Overview

```
Data Collection, Sanity Check       →    Target Market Identification
& Preprocessing                          (Exploratory Data Analysis)
                                                      ↓
Customer Segmentation               ←    Heterogeneity Analysis       ←    Pilot Campaign & A/B Testing
& Insights (K-Means Clustering)          using ANOVA (Income Tiers)        (Control vs Treatment)
```

---

## Directory Structure

```
├── Code/
│   └── Statistics_project_code_file.ipynb   # Main project notebook
│
├── data/
│   ├── customers.csv                         # Customer demographics (500 records)
│   ├── credit_profiles.csv                   # Credit scores, limits, utilisation
│   ├── transactions.csv                      # Transaction history across platforms
│   └── avg_transactions_after_campaign.csv   # Post-campaign A/B test data (n=40 per group)
│
├── output/
│   └── Statistics_output_results.pdf         # Problem statement, methodology & results
│
├── README.md
└── requirements.txt                          # (coming soon)
```

---

## Dataset Description

| File | Records | Key Features |
|---|---|---|
| `customers.csv` | 500 | age, gender, location, occupation, annual\_income, marital\_status |
| `credit_profiles.csv` | 500+ | credit\_score (300–800), credit\_utilisation, credit\_limit, outstanding\_debt |
| `transactions.csv` | 2000+ | tran\_amount, payment\_type, product\_category, platform, tran\_date |
| `avg_transactions_after_campaign.csv` | 80 (40+40) | control\_group\_avg\_tran, test\_group\_avg\_tran, campaign\_date |

> All datasets are synthetic and designed to replicate real-world statistical properties including right-skewed income distributions, FICO-aligned credit score ranges, and heavy-tailed transaction amounts.

---

## Project Phases

### Phase 1 — Data Collection, Sanity Check & Preprocessing
- Validated data authenticity: checked income distributions, credit score ranges (300–800), credit utilisation bounds (0–1), and referential integrity across all three datasets to confirm synthetic data mimics real-world behaviour
- Imputed missing `annual_income` values using occupation-wise median; corrected erroneous entries below \$100
- Resolved duplicate credit profile records; imputed missing `credit_limit` using mode within credit score range bands
- Capped outstanding debt values exceeding credit limit for logical consistency
- Replaced zero-value transactions with median amount from a representative sub-group
- Identified and handled outliers using the 2×IQR rule on transaction amounts

### Phase 2 — Target Market Identification (EDA)
- Analysed spending behaviour, payment type preferences, and product categories across three age groups: 18–25, 26–48, 49–65
- Found that the **26–48 cohort** generates the highest absolute transaction volume
- Identified the **18–25 group** as the primary untapped segment — consistent digital platform usage (Amazon, Flipkart), dominant spending in Electronics and Fashion, but proportionally lower credit card adoption
- Computed correlation heatmap across credit score, utilisation, income, credit limit, outstanding debt, and age

### Phase 3 — Pilot Campaign & A/B Testing (Z-Test)
- Conducted power analysis: minimum 197 customers per group required (α=0.05, power=0.8, Cohen's d=0.2)
- Launched campaign to 100 customers from the 18–25 pool; achieved ~40% conversion rate → 40 test group customers
- Formed an independent control group of 40 customers

**Hypotheses:**
```
H₀: μ_test ≤ μ_control  (campaign does not increase avg transaction amount)
H₁: μ_test > μ_control  (campaign increases avg transaction amount)
```

**Results:**
- Z-score > 1.645 (critical value, right-tailed, α=0.05) → H₀ rejected
- p-value < 0.05 → statistically significant at 5% level
- 95% CI of test group mean does not contain control mean → third line of evidence
- **Conclusion:** Campaign produced a statistically significant lift in average transaction amounts among the 18–25 target group

### Phase 4 — Heterogeneity Analysis using ANOVA (Income Tiers)
- Stratified 18–25 customers into three income tiers:
  - **Low:** ≤ \$22,930
  - **Medium:** \$22,930 – \$47,665
  - **High:** > \$47,665
- One-way ANOVA confirmed significant spending differences across tiers (p < 0.05)
- Tukey HSD post-hoc analysis (FWER = 0.05):

| Group 1 | Group 2 | Mean Diff | p-adj | Reject |
|---|---|---|---|---|
| High | Low | -8,490.48 | 0.9916 | No |
| High | Medium | +4,03,492.64 | 0.0000 | Yes |
| Low | Medium | +4,11,983.12 | 0.0000 | Yes |

- **Key finding:** Medium income tier spends significantly more than both Low and High — indicating a financially active sub-population and making it the **highest-priority acquisition target**

### Phase 5 — Customer Segmentation (K-Means Clustering)
- Applied K-Means clustering exclusively to the 18–25 age group using 9 features: age, annual income, credit score, credit utilisation, credit limit, outstanding debt, total spending, avg transaction amount, transaction count
- Features standardised using z-score normalisation
- Optimal K=2 selected via Elbow method and Silhouette score maximisation
- PCA used for 2D visualisation of cluster separation

**Cluster Profiles:**

| Feature | Cluster 0 (140 customers) | Cluster 1 (100 customers) |
|---|---|---|
| Dominant Income Tier | Medium | Medium |
| % Low / Medium / High | 22.1% / 49.3% / 28.6% | 30.0% / 51.0% / 19.0% |
| Avg Credit Score | 407 | 587 |
| Credit Utilisation | 0.48 | 0.56 |
| Avg Total Spending | \$1,589,896 | \$1,620,345 |
| Avg Txn Amount | \$3,197 | \$3,239 |
| Avg Txn Count | 497 | 500 |

**Key insight:** Despite near-identical spending volumes, clusters show a **180-point credit score gap** — indicating two fundamentally different financial profiles requiring different product strategies.

**Business Recommendations:**
- **Cluster 0** → Starter / income-verified card (higher income, low credit history)
- **Cluster 1** → Rewards / cashback card (credit-established, financially disciplined)

---

## Key Results Summary

| Phase | Method | Key Outcome |
|---|---|---|
| EDA | Descriptive Statistics | 18–25 identified as untapped target segment |
| A/B Testing | Two-sample Z-Test | Z > 1.645, p < 0.05 → campaign validated |
| Heterogeneity | One-way ANOVA + Tukey HSD | Medium tier spends significantly more (p ≈ 0) |
| Segmentation | K-Means (K=2) | 180-point credit score gap between clusters |

---

## Technologies & Libraries

| Library | Usage |
|---|---|
| `pandas` | Data loading, cleaning, merging, feature engineering |
| `numpy` | Numerical computations, Z-score calculation |
| `matplotlib` | Histograms, bar charts, pie charts, scatter plots |
| `seaborn` | Heatmaps, boxplots, violin plots, count plots |
| `scipy.stats` | One-way ANOVA (f\_oneway), Z-test, confidence intervals |
| `statsmodels` | Power analysis (tt\_ind\_solve\_power), Tukey HSD (pairwise\_tukeyhsd) |
| `sklearn.cluster` | K-Means clustering |
| `sklearn.preprocessing` | StandardScaler for feature normalisation |
| `sklearn.decomposition` | PCA for cluster visualisation |
| `sklearn.metrics` | Silhouette score for optimal K selection |

---

## Skills Demonstrated

- **Descriptive Statistics** — EDA across demographics, credit profiles, and transaction data
- **Inferential Statistics** — ANOVA with Tukey HSD post-hoc for population-level conclusions
- **Hypothesis Testing** — Two-sample Z-test with power analysis and confidence intervals
- **Unsupervised Machine Learning** — K-Means clustering with PCA visualisation
- **Data Preprocessing** — Missing value imputation, outlier treatment, data merging

---

## How to Run

1. Clone the repository
```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

2. Install dependencies *(requirements.txt coming soon)*
```bash
pip install pandas numpy matplotlib seaborn scipy statsmodels scikit-learn
```

3. Open the notebook
```bash
jupyter notebook Code/Statistics_project_code_file.ipynb
```

4. Ensure all four CSV files are present inside the `data/` folder before running

> **Note:** Update the file paths in the notebook's data loading cells to point to `../data/` if running from the `Code/` directory.

---

## Output

The `output/` folder contains `Statistics_output_results.pdf` which covers:
- Problem statement and project motivation
- Complete methodology with statistical framework descriptions
- Results from all five pipeline phases with plots and tables

---

## License

This project is submitted as a term project for academic purposes.
