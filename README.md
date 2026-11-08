# Education Insights Lab: Diagnosing Educational Opportunity and Outcomes

**Individual Capstone, Semester 4 | Data Science Programme**
**Author:** [Your Name] | **Date:** [Month Year]

---

## 1. Project Overview

This project analyses a dataset of 5,000 United States educational institutions to understand how institutional resources and socioeconomic conditions are associated with academic outcomes. It follows the full data science workflow: problem definition, data understanding, cleaning, exploratory analysis, statistical reasoning, machine learning, interpretation, and evidence-based recommendations.

## 2. The Problem

Educational outcomes differ widely across institutions in the United States. Policymakers often respond by increasing per-pupil funding, but it is unclear how much of the apparent link between spending and outcomes is genuinely about money, and how much simply reflects the socioeconomic conditions of the communities that schools serve. Without separating these effects, funding decisions risk targeting the wrong institutions or expecting results that spending alone cannot deliver.

## 3. Stakeholder

**A state education funding agency** deciding where additional funding and support are most likely to improve student outcomes. The analysis is written for decision-makers who need clear, evidence-based answers rather than technical detail.

## 4. Why It Matters

- Public education budgets are limited, so understanding which factors are most strongly associated with outcomes helps direct resources where they can have the most effect.
- Institutions serving high-poverty communities (for example Title I Schoolwide schools) may need different support than spending alone can provide.
- Evidence that separates spending from socioeconomic context prevents misleading conclusions such as "high-spending schools perform better, so spending causes performance."

## 5. Research Questions

### Primary research question

> Is per-pupil expenditure associated with math proficiency after accounting for socioeconomic conditions, and how well can institutional characteristics predict math proficiency?

### Supporting questions

1. How do socioeconomic conditions, funding, staffing and broadband access differ across institutions and Title I groups?
2. How does math proficiency differ across Title I status groups?
3. Is STEM certification associated with Title I status? (Chi-square test of independence)
4. Which institutional characteristics matter most for predicting math proficiency (permutation importance)?
5. Does a more complex model (random forest) outperform a simple linear baseline?

## 6. Dataset

| Item | Detail |
|---|---|
| File | `us_education_system_dataset.csv` |
| Rows | 5,000 institutions (one row each, unique `institution_id`) |
| Columns | 35 (25 numeric, 10 text) |
| Levels | Elementary (2,225), High School (1,209), Middle (996), K-12 Combined (212), 4-Year University (204), 2-Year College (154) |
| Control | Public (3,547), Public Charter (577), Private Non-Profit (499), Private Religious (256), Private For-Profit (121) |
| Title I status | Schoolwide (2,180), Not Applicable (1,108), Targeted (945), Not Eligible (767) |
| STEM certified | Yes: 752 (15.0%), No: 4,248 (85.0%) |

**Variable groups:** location and identity, school level and ownership, Title I status, enrollment and staffing, socioeconomic and demographic indicators, spending and funding sources, academic outcomes (math, reading, graduation, absenteeism), STEM certification, broadband access, and postsecondary indicators (SAT, ACT, AP/IB, postsecondary enrollment).

The full data dictionary is in `data/data_dictionary.csv` (see Data Understanding notebook).

### Known data characteristics (from the initial audit)

- **Structural missing values:** `avg_sat_score` and `avg_act_score` (3,375 missing) exist only for High School, K-12 Combined and 4-Year University rows. `ap_ib_participation_pct` and `postsecondary_enrollment_pct` (3,579 missing) exist only for High School and K-12 Combined rows. There are no missing values inside the levels where these measures apply.
- **Graduation rate** is populated for every row, including elementary and middle schools, where it is not meaningful.
- **Colleges** (358 rows) behave very differently from K-12 institutions and require a scope decision.
- **No duplicate IDs or fully duplicated rows.** 117 name + state + county combinations repeat but have different values, so they are treated as different institutions with generic names.
- **Ceilings at 98** appear in several percentage columns (for example `local_funding_pct`, `reading_proficiency_pct`).

## 7. Assumptions

- The dataset is a reasonably accurate snapshot of institutions at a single point in time.
- Reported percentages and scores were measured consistently across institutions.
- Missing SAT, ACT, AP/IB and postsecondary values reflect levels where those measures do not apply, not data collection failures, so they are retained as missing rather than imputed.
- Institutions that share a name, state and county but differ in their values are distinct institutions.
- Free/reduced lunch percentage is a reasonable proxy for socioeconomic conditions.

## 8. Limitations

- **Association, not causation.** The data is cross-sectional, so results describe associations and cannot show that spending or any other factor causes changes in outcomes.
- **Proxy measures.** Free/reduced lunch captures only part of socioeconomic context. Per-pupil expenditure says nothing about how money is spent.
- **Institution-level data.** No student-level data, so individual outcomes and within-school differences cannot be examined.
- **Mixed institution types.** Colleges and K-12 schools differ greatly; conclusions are checked with and without colleges.
- **Data quality issues.** Graduation rate appears for levels where it is illogical, and several columns are capped at 98.
- **Limited postsecondary coverage.** Postsecondary indicators exist for only 1,421 institutions.
- **No unmeasured factors.** Curriculum, teacher quality, programme details and local policy are not in the data.

## 9. Methodology

| Stage | Approach |
|---|---|
| Data understanding | Structure audit, variable types, missingness report, data dictionary |
| Data cleaning | Missing-data reasoning by institution level, validation checks (impossible percentages, duplicates, categories, outliers, level appropriateness), reproducible cleaning script |
| EDA | Univariate distributions, bivariate relationships (spending, broadband, staffing, poverty, Title I), multivariate analysis of spending versus math proficiency controlling for poverty |
| Statistics | Chi-square test of independence (Title I vs STEM certification) with Cramer's V; bootstrap confidence intervals and/or ANOVA-Kruskal-Wallis across Title I groups |
| Machine learning | Regression predicting `math_proficiency_pct`: Linear Regression baseline versus Random Forest, using scikit-learn Pipelines to avoid data leakage |
| Evaluation | MAE, RMSE and R-squared on a held-out test set |
| Interpretation | Permutation importance, model coefficients, partial dependence |
| Insights | 3-5 evidence-based insights and recommendations |

**Leakage control:** SAT, ACT, reading proficiency, AP/IB participation, postsecondary enrollment and graduation rate are excluded as predictors of math proficiency because they measure closely related achievement.

## 10. Repository Structure

```
education-insights-lab/
|-- README.md
|-- data/
|   |-- README.md
|   |-- raw/                 # original dataset (never edited by hand)
|   `-- processed/           # cleaned data produced by src/data_cleaning.py
|-- notebooks/
|   |-- 01_data_understanding.ipynb
|   |-- 02_data_cleaning.ipynb
|   |-- 03_eda.ipynb
|   |-- 04_statistical_analysis.ipynb
|   `-- 05_machine_learning.ipynb
|-- src/
|   `-- data_cleaning.py
|-- figures/
|-- reports/
|   `-- final_report.pdf
|-- requirements.txt
`-- .gitignore
```

## 11. How to Reproduce

```bash
# 1. clone the repository
git clone https://github.com/<your-username>/education-insights-lab.git
cd education-insights-lab

# 2. create an environment and install dependencies
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. place the raw data at data/raw/us_education_system_dataset.csv

# 4. generate the cleaned dataset
python src/data_cleaning.py

# 5. run the notebooks in order (01 to 05)
jupyter notebook
```

## 12. Deliverables

- [ ] GitHub repository with the complete project
- [ ] Notebooks: data understanding, cleaning, EDA, statistical analysis, machine learning
- [ ] Final report (`reports/final_report.pdf`)
- [ ] 10-15 minute presentation
- [ ] Individual reflection

## 13. Key Findings

*To be completed after the analysis.*

## 14. Recommendations

*To be completed after the analysis.*
