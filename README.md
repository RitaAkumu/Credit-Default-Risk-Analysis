# Credit Default Risk Analysis (EDA + Baseline Model)

**Goal:** identify the main drivers of credit default, quantify risk by customer segments, and build a baseline model that estimates **probability of default** for screening, policy, and monitoring.

---

## 1) Business context
Default risk impacts:
- **Profitability & cash flow** (losses + recovery/collections costs)
- **Operational workload** (review queues, collections prioritization)
- **Portfolio health** (concentration risk, consistent policy enforcement)

Because default is a **rare event**, this project focuses on **risk segmentation** and **precision/recall trade-offs** rather than accuracy alone.

---

## 2) Dataset
- **Rows:** 10,000 customers  
- **Columns:** `ID`, `default`, `student`, `balance`, `income`  
- **Target:** `default` (binary)  
- **Default prevalence:** 333 / 10,000 (~3%)

**Preprocessing notes**
- Encoded `default` and `student` from Yes/No to 1/0
- No missing values or duplicates reported

---

## 3) Key questions
1. Is **balance** a strong driver of default?
2. Is **income** protective against default?
3. Does **student status** affect default likelihood?
4. How do decision thresholds change **precision vs recall** (and therefore review workload)?

---

## 4) Exploratory Data Analysis (highlights)

### Default prevalence
- Default is low (~3%), meaning a naive “no default” classifier would appear highly accurate (~97%) but be useless for detection.

### Segment-level insights
- **Balance is the strongest driver**: default rates increase sharply in the highest balance bands.
- **Student status:** students default at a higher rate than non-students (~4.3% vs ~2.9%).
- **Income:** default probability generally decreases with income, but the relationship is weaker than balance in this dataset.

### Defaulters vs non-defaulters (averages)
| Group | Avg. balance | Avg. income |
|------:|-------------:|------------:|
| No default | 803.94 | 33,566.17 |
| Default | 1,747.82 | 32,089.15 |

**Interpretation:** default risk appears more tied to **exposure/outstanding debt** (balance) than income alone.

---

## 5) Modeling

### Baseline model
- **Logistic Regression** using: `student`, `balance`, `income`
- Addressed class imbalance using **class weighting**
- Output: **probability of default** (risk score)

### Performance
- **ROC-AUC:** 0.951 (strong ranking ability)

---

## 6) Threshold trade-offs (precision vs recall)
Operationally, you can tune the decision threshold to balance:
- **Higher recall** → catch more defaulters, but flag more non-defaulters (more reviews)
- **Higher precision** → fewer false alarms, but miss more defaulters

Selected thresholds:

| Threshold | Precision | Recall | TP | FP | FN | TN |
|----------:|----------:|-------:|---:|---:|---:|---:|
| 0.10 | 0.080 | 1.00 | 100 | 1,144 | 0 | 1,756 |
| 0.30 | 0.129 | 0.94 | 94 | 632 | 6 | 2,268 |
| 0.50 | 0.182 | 0.89 | 89 | 400 | 11 | 2,500 |
| 0.60 | 0.222 | 0.87 | 87 | 305 | 13 | 2,595 |

**Example takeaway:** at **0.50**, recall is high (0.89) but precision is low (0.182), reflecting many false positives—useful for monitoring, but threshold tuning is needed to match review capacity.

---

## 7) Recommendations (business actions)
- **Credit limit policy:** tighter limits or manual review for customers entering the highest balance bands.
- **Monitoring:** build an “early warning” list for customers whose balance moves into higher-risk bands.
- **Collections prioritization:** prioritize outreach for the highest balance bands (highest expected risk).
- **Better affordability metric:** add **balance-to-income ratio** to refine risk beyond balance alone.

---

## 8) Limitations
- Only 3 predictors (no credit history, delinquencies, tenure, loan amount, etc.)
- Class imbalance (~3% default) makes accuracy misleading
- Correlation/association ≠ causation

---

## 9) Next steps
- Add more predictors (behavioral + credit history)
- Compare with alternative models (e.g., tree-based methods)
- Calibrate predicted probabilities
- Use cost-sensitive thresholding aligned to business constraints (review cost vs default loss)

---



