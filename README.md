# 🎓 Students' Academic Performance: GPA Regression Analysis

What affects the academic performance of high-school students? This project uses statistical and machine learning techniques to identify which factors drive student GPA, from OLS regression and ANOVA to propensity score matching and Bayesian regression.

*Group 4 project for Statistics for Data Science.*

---

## 📋 Project Overview

Using a Kaggle dataset of 2,392 high-school students, we tested the following hypotheses:

- **H₀:** There is no linear relationship between GPA and any of the independent variables (β₁ = β₂ = … = βᵢ = 0)
- **H₁:** At least one regression coefficient is non-zero, indicating a significant relationship with GPA

**Key Finding:** Absences have by far the strongest (negative) effect on GPA, while parental support and weekly study time have positive effects. Every model we ran supports rejecting the null hypothesis.

---

## 📊 Dataset

**Source:** [Students Performance Dataset, Kaggle (R. El Kharoua, 2024)](https://www.kaggle.com/datasets/rabieelkharoua/students-performance-dataset)
**Size:** 2,392 students, 15 variables. No null or duplicate values; all variables are numerically encoded.

| Section | Variable | Description |
|---|---|---|
| Student ID | `StudentID` | Unique identifier (1001 to 3392) |
| Demographics | `Age` | 15–18 |
| | `Gender` | 0 = male, 1 = female |
| | `Ethnicity` | 0 = Caucasian, 1 = African American, 2 = Asian, 3 = Other |
| Parental Education | `ParentalEducation` | 0 = None, 1 = High School, 2 = Some College, 3 = Bachelor's, 4 = Higher |
| Study Habits | `StudyTimeWeekly` | Weekly study time in hours (0–20) |
| | `Absences` | Absences in a school year (0–30) |
| | `Tutoring` | 0 = No, 1 = Yes |
| Parental Involvement | `ParentalSupport` | 0 = None, 1 = Low, 2 = Moderate, 3 = High, 4 = Very High |
| Extracurricular Activities | `Extracurricular`, `Sports`, `Music`, `Volunteering` | 0 = No, 1 = Yes |
| Academic Performance | `GPA` ⭐ | **Target variable**, 0.0–4.0 |
| | `GradeClass` | 0 = A (≥ 3.5), 1 = B (3.0–3.5), 2 = C (2.5–3.0), 3 = D (2.0–2.5), 4 = F (< 2.0) |

> ⚠️ The CSV is excluded from this repo via `.gitignore`. Download it from Kaggle and place it next to the notebook (see [Getting Started](#-getting-started)).

---

## 🛠️ Methods & Tools

**Libraries used:**
- `pandas`, `numpy`: data manipulation
- `matplotlib`, `seaborn`: visualization
- `scipy`, `statsmodels`: statistical tests, ANOVA, OLS, Bayesian-style Gaussian GLM
- `scikit-learn`: scaling, train/test split, logistic regression, nearest-neighbor matching, learning curves
- `linearmodels`: additional modelling utilities (imported in the notebook)

**Analysis pipeline:**
1. **Preprocessing & EDA**: null/duplicate checks, variable typing, histograms, pie charts, box plots, scatter plots, correlation heatmap
2. **ANOVA**: one-way (categorical variables and binned `StudyTimeWeekly` / `Absences`) and two-way (study time × absences, parental support × tutoring)
3. **OLS regression**: unscaled and scaled (80/20 split, `StandardScaler`), assumption checks (linearity, homoscedasticity, normality via Q-Q plot), multicollinearity checks (VIF), power analysis (1,000 simulations), learning curves
4. **Backward elimination**: p-value based variable selection, compared by AIC
5. **Feature importance**: standardized coefficients
6. **Logistic regression**: multinomial model predicting `GradeClass`
7. **Causal inference**: generalized propensity scores with nearest-neighbor matching on `Absences`
8. **Bayesian regression**: Gaussian GLM (`statsmodels`) on standardized predictors, with residual and posterior predictive plots

---

## 📈 Results

### Exploratory Data Analysis
| Feature | Pearson r with GPA |
|---|---|
| `Absences` | **−0.92** |
| `ParentalSupport` | 0.19 |
| `StudyTimeWeekly` | 0.18 |
| `Tutoring` | 0.15 |

### Model Performance
| Model | Result | Notes |
|---|---|---|
| OLS (all variables) | R² = **0.985** (Adj. R² = 0.985) | p-value (F-stat) ≈ 0.00 |
| OLS (scaled, train/test) | R² = **0.954**, MSE = 0.04 | `Absences` β = −0.8442 (standardized) |
| OLS (binned `Absences`, `StudyTimeWeekly`, `Age`) | R² = 0.802, MSE = 0.17 | `Absences` (low) β = −0.7675 |
| Backward elimination | AIC: −772.3 → **−781.4** | Same R² as the full model |
| Multinomial logistic regression (`GradeClass`) | Accuracy ≈ **0.68** | Weak on minority classes (e.g. class 0, F1 = 0.00) |
| Bayesian regression (Gaussian GLM) | `Absences` β = −0.841 | Posterior predictive closely matches actual GPA |

### Causal Inference (Propensity Score Matching)
- **Low absences:** estimated treatment effect of **+1.496** GPA points
- **High absences:** estimated treatment effect of **−1.448** GPA points

---

## 🔍 Key Insights

- **Absences** is the most important predictor of GPA by a wide margin, confirmed by OLS, feature importance, propensity scores, and Bayesian regression
- **Parental support**, **weekly study time**, and **tutoring** have positive effects (Bayesian β ≈ 0.17, 0.162, and 0.115 respectively; tutoring ≈ +0.26 GPA points in the unscaled OLS)
- **Extracurricular activities**, **sports**, and **music** also show significant effects on GPA
- **Gender**, **ethnicity**, **parental education**, **volunteering**, and **age** showed no significant direct effect
- Backward elimination retained: `StudyTimeWeekly`, `Absences`, `Tutoring`, `ParentalSupport`, `Extracurricular`, `Sports`, `Music`

### ⚠️ Limitations
- Results show **associations**; propensity score matching reduces confounding but does not definitively establish causality
- Possible self-reported or inconsistent data, undetected multicollinearity, and omitted variables
- Findings are specific to this dataset and may not generalize beyond it

### 🔭 Future Work
- Non-linear relationships and interaction effects
- Additional predictors (e.g. location, socioeconomic data)
- Subject-level analysis (math, language, science)
- More advanced ML models and class-imbalance handling for `GradeClass`

---

## 📁 Repository Structure

```text
├── regression analysis.ipynb            # Full analysis notebook
├── report.pdf                           # Detailed project report
├── Summary_Paper.pdf                    # Short summary paper
├── Stats_Appendix-08-07-2024.pdf        # Appendix (distributions, ANOVA table)
├── requirements.txt                     # Python dependencies
├── .gitignore                           # Excludes *.csv
└── README.md
```

---

## 🚀 Getting Started

```powershell
# Clone the repo
git clone https://github.com/IgaMeddie/gpa-regression-analysis.git
cd gpa-regression-analysis

# (Optional) create and activate a virtual environment
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install dependencies
pip install -r requirements.txt
```

1. Download the dataset from [Kaggle](https://www.kaggle.com/datasets/rabieelkharoua/students-performance-dataset).
2. Place the CSV in the repo root and make sure the filename matches the one in the notebook's `pd.read_csv(...)` call (the notebook expects `Student_performance_data _.csv`).
3. Launch the notebook:

```powershell
jupyter notebook "regression analysis.ipynb"
```

---

## 👥 Authors

Ilana Berger Fridman, Jiahua Cui, Iga Meddie, Jiayi Song, Jaymilee Thompson

---

## 📚 References

1. R. El Kharoua, 2024. [Students Performance Dataset, Kaggle](https://www.kaggle.com/datasets/rabieelkharoua/students-performance-dataset)
