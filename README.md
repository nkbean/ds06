# Student Performance Analysis

**Team:** _add names_, _add names_, _add names_
**Brief:** DS-06 (Open Brief)  ·  **Dataset:** UCI Student Performance, Mathematics file (`student-mat.csv`), 395 students × 33 columns. DOI 10.24432/C5TG7T

## The question
A school wants to know which students may need extra maths support before the final exam. **How do study habits, absences, previous failures, parental education and school support relate to final maths performance (G3, 0–20)?** Regression task; success metric: **MAE in grade points**, compared with a predict-the-average baseline.

## Key results
- **Data-quality finding:** 38 students (9.6%) have G3 = 0 and 0 absences: they did not sit the final exam. Removing them from the grade analysis reveals the real absence effect (correlation +0.03 → **−0.21**).
- **Past failures** are the strongest warning sign: average grade 11.9 (0 failures) → 8.3 (3), and 20–31% of students with failures skipped the final vs 6% without.
- Absences: 0 absences → 12.6, 11+ absences → 10.2. Study time helps only above 5 h/week (12.6 vs ~11.1).
- School support looks negative (9.6 vs 11.8) only because weaker students get it; they improve more during the year (+0.50 vs +0.21).
- Random Forest MAE **2.19** vs baseline **2.57** (15% better; CV 2.34 vs 2.63), R² 0.24: good for flagging, not for exact grades.
- Error analysis: failing students are predicted ~3 points too high → flag everyone predicted below ~12.

![Average grade by number of absences (students who sat the exam)](reports/figures/05_absences.png)
*Average grade by number of absences (students who sat the exam)*

![Average grade by number of past failures](reports/figures/04_failures.png)
*Average grade by number of past failures*

## Approach
1. **Cleaning:** no missing values or duplicates; the G3 = 0 group flagged (not deleted) and analysed separately; absence outliers kept and grouped into bands; parental education level 0 (2–3 students) merged with level 1.
2. **EDA:** 8 charts (G3 = 0 audit, grade distribution, study time, failures, absences, parental education, support, correlations), each with a written insight.
3. **Model:** G1/G2 excluded (leakage for early warning); 80/20 split with `random_state=42`; baseline = average grade; Random Forest in a one-hot pipeline; evaluated with MAE/RMSE/R², 5-fold CV, residual plot, error analysis by grade band and permutation importance.

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebooks/analysis.ipynb
```
Open `notebooks/analysis.ipynb` and choose **Run All** (Kernel → Restart & Run All). The notebook reads `data/student-mat.csv` and saves the charts to `reports/figures/`.

**Google Colab:** open the notebook in Colab and choose Runtime → Run all; a **Choose Files** button appears, so upload `data/student-mat.csv`.

## Repository structure
```
ds-06-student-performance-analysis/
├── README.md
├── data/
│   └── student-mat.csv
├── notebooks/
│   └── analysis.ipynb      # full notebook, outputs visible
├── reports/
│   ├── figures/            # key charts saved with plt.savefig()
│   └── presentation.pdf    # demo-day slides
└── requirements.txt
```

## Limitations
- Observational data from two Portuguese schools (2005–06): associations, not causes.
- Many variables are self-reported survey answers.
- "Did not sit the exam" for G3 = 0 is an assumption the school should confirm.
- 357 students and R² 0.24; the model uses sex, so fairness must be checked before real use.

## AI usage
Claude (Anthropic) was used as an assistant to draft notebook code, chart code and documentation text, and to suggest checks (for example data-quality and leakage checks). Every team member re-ran the notebook, checked each result against the outputs, and can explain every cell and decision in their own words. The team is responsible for the final content.
