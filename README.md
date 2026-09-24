# 🏥 Healthcare Analytics & Patient Network Insights

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg?logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-2.0%2B-150458.svg?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-3.7%2B-informational.svg)](https://matplotlib.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626.svg?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![IBM SkillsBuild](https://img.shields.io/badge/IBM-SkillsBuild%20Project-052FAD.svg?logo=ibm&logoColor=white)](https://skillsbuild.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An end-to-end healthcare data analytics and clinical informatics project developed under the **IBM SkillsBuild Data Analytics Internship Program**. This project delivers a thorough diagnostic evaluation of **55,500 patient encounters** across an integrated multi-hospital network, analyzing disease burden, demographics, longitudinal billing stability, insurance market share, emergency department capacity, and clinical risk triage.

---

## 📋 Table of Contents
1. [Executive Summary](#-executive-summary)
2. [Project Architecture & Objectives](#-project-architecture--objectives)
3. [Dataset Description & Schema](#-dataset-description--schema)
4. [Data Cleaning & Transformation Pipeline](#-data-cleaning--transformation-pipeline)
5. [Key Analytics & Business Questions](#-key-analytics--business-questions)
   - [Question 1: Disease Burden & Condition Prevalence](#question-1-disease-burden--condition-prevalence)
   - [Question 2: Patient Age Distribution & Demographics](#question-2-patient-age-distribution--demographics)
   - [Question 3: Long-term Treatment Cost Stability](#question-3-long-term-treatment-cost-stability)
   - [Question 4: Age vs. Billing Correlation](#question-4-age-vs-billing-correlation)
   - [Question 5: Insurance Provider Market Share](#question-5-insurance-provider-market-share)
6. [Ad-Hoc Strategic Healthcare Scenarios](#-ad-hoc-strategic-healthcare-scenarios)
   - [Scenario I: Emergency Department Capacity Planning](#scenario-i-emergency-department-capacity-planning)
   - [Scenario II: High-Cost Patient Cohort (Top 10%)](#scenario-ii-high-cost-patient-cohort-top-10)
   - [Scenario III: Urgent Abnormal Diagnostic Flagging](#scenario-iii-urgent-abnormal-diagnostic-flagging)
7. [Interactive Analytics Dashboard](#-interactive-analytics-dashboard)
8. [Project File Structure](#-project-file-structure)
9. [Installation & Execution Guide](#-installation--execution-guide)
10. [Key Strategic Recommendations](#-key-strategic-recommendations)
11. [Author & Acknowledgments](#-author--acknowledgments)

---

## 📌 Executive Summary

Modern hospital networks require real-time data transparency to balance clinical quality, emergency bed availability, and revenue cycle integrity. This study processes a 5-year longitudinal medical dataset (May 2019 – May 2024), uncovering vital operational benchmarks:

| Key Performance Metric | Benchmark Value | Operational Implication |
| :--- | :--- | :--- |
| **Validated Admissions** | **54,966 records** | De-duplicated from 55,500 raw logs (-534 duplicates). |
| **Mean Treatment Billing** | **$25,594.54** | Stable fee-for-service chargemaster across 61 months. |
| **Average Length of Stay (LOS)** | **15.50 days** | Derived metric via admission-to-discharge delta. |
| **Median Patient Age** | **52.00 years** | Broad demographic range (13 to 89 years). |
| **High-Cost Outlier Accounts** | **5,497 cases** | Top 10% accounts exceeding **$45,160.26** billing. |
| **High-Risk Clinical Reviews** | **6,038 cases** | Emergency admissions flagged with **Abnormal** lab tests. |
| **Insurance Diversification** | **~20% per payer** | Symmetrically balanced risk across 5 major insurers. |

---

## 🎯 Project Architecture & Objectives

The project adheres to the standard **CRISP-DM (Cross-Industry Standard Process for Data Mining)** framework:

```
[Raw Registry Data] ➔ [Data Audit & Cleaning] ➔ [Feature Engineering] ➔ [Exploratory Data Analysis] ➔ [Business Scenarios] ➔ [Executive Dashboard & Report]
```

1. **Data Ingestion & Integrity Auditing:** Detect duplicate entries, erroneous negative billing figures, and casing disparities.
2. **Clinical Inflow Analysis:** Measure chronic disease prevalence (Arthritis, Diabetes, Hypertension, Obesity, Cancer, Asthma).
3. **Demographic & Pricing Elasticity:** Test whether chronological age dictates medical expenses.
4. **Capacity & Emergency Operations:** Quantify monthly emergency department admissions to guide nurse and doctor scheduling.
5. **Quality & Financial Risk Mitigation:** Isolate top billing quantiles and triage high-acuity abnormal emergency cases.
6. **Executive Reporting:** Compile findings into an executive `.docx` report and an interactive standalone HTML dashboard.

---

## 🗃️ Dataset Description & Schema

The dataset resides in [`Dataset/healthcare_dataset.csv`](Dataset/healthcare_dataset.csv) and contains 15 initial attributes:

| Column Name | Data Type | Description | Sample Values |
| :--- | :--- | :--- | :--- |
| `Name` | String | Patient full name (cleaned via Title-casing) | `Bobby Jackson`, `Leslie Terry` |
| `Age` | Integer | Patient age in years (13 to 89) | `30`, `62`, `76` |
| `Gender` | String | Biological gender category | `Male`, `Female` |
| `Blood Type` | String | ABO / Rh blood group | `B-`, `A+`, `O+`, `AB+` |
| `Medical Condition` | String | Primary diagnosed chronic condition | `Cancer`, `Diabetes`, `Arthritis` |
| `Date of Admission`| Datetime | Date the patient checked into the facility | `2024-01-31`, `2019-08-20` |
| `Doctor` | String | Attending physician name | `Matthew Smith`, `Kevin Wells` |
| `Hospital` | String | Admitting hospital facility or clinic | `Sons and Miller`, `Cook PLC` |
| `Insurance Provider`| String | Commercial or public healthcare payer | `Blue Cross`, `Medicare`, `Cigna` |
| `Billing Amount` | Float | Total invoiced medical bill in USD | `$18,856.28`, `$33,643.33` |
| `Room Number` | Integer | Allocated inpatient room number (101–500) | `328`, `265`, `450` |
| `Admission Type` | String | Care classification | `Elective`, `Urgent`, `Emergency` |
| `Discharge Date` | Datetime | Date the patient left the facility | `2024-02-02`, `2019-08-26` |
| `Medication` | String | Prescribed pharmaceutical therapy | `Paracetamol`, `Ibuprofen`, `Aspirin` |
| `Test Results` | String | Diagnostic laboratory test outcome | `Normal`, `Abnormal`, `Inconclusive` |
| **`Length_of_stay`** | Integer | **Engineered feature**: `Discharge Date - Admission Date` | `2`, `6`, `15`, `30` days |

---

## 🧹 Data Cleaning & Transformation Pipeline

All transformations are reproducibly implemented in [`healthcare.ipynb`](healthcare.ipynb):

1. **Deduplication:**
   ```python
   df = df.drop_duplicates()
   ```
   - 534 redundant entries identified and excised, reducing row count from 55,500 to **54,966**.
2. **Text Normalization:**
   ```python
   df['Name'] = df['Name'].str.strip().str.title()
   ```
   - Resolves irregular capitalization (e.g., `DaNnY sMitH` ➔ `Danny Smith`).
3. **Negative Billing Correction:**
   ```python
   df.loc[df['Billing Amount'] < 0, 'Billing Amount'] = df['Billing Amount'].mean()
   ```
   - 106 erroneous negative billing entries (down to -$2,008.49) replaced with the baseline population mean.
4. **Temporal Transformation & Feature Engineering:**
   ```python
   df['Date of Admission'] = pd.to_datetime(df['Date of Admission'])
   df['Discharge Date'] = pd.to_datetime(df['Discharge Date'])
   df['Length_of_stay'] = (df['Discharge Date'] - df['Date of Admission']).dt.days
   ```
   - Yields valid hospital length-of-stay metrics (mean: **15.50 days**).

---

## 📊 Key Analytics & Business Questions

### Question 1: Disease Burden & Condition Prevalence
> **"Which medical condition affects the most patients across the network?"**

| Medical Condition | Patient Count | Percentage Share |
| :--- | :---: | :---: |
| **Arthritis** | 9,218 | 16.77% |
| **Diabetes** | 9,216 | 16.77% |
| **Hypertension** | 9,151 | 16.65% |
| **Obesity** | 9,146 | 16.64% |
| **Cancer** | 9,140 | 16.63% |
| **Asthma** | 9,095 | 16.55% |
| **Total** | **54,966** | **100.00%** |

*Insight:* Patient volume is remarkably balanced across all conditions (spread < 0.22%), indicating a balanced multi-specialty regional network requiring cross-functional clinician staffing.

---

### Question 2: Patient Age Distribution & Demographics
> **"How is patient age distributed across the network?"**

- **Range:** 13 to 89 years
- **Mean Age:** 51.54 years | **Median Age:** 52.00 years | **Std Dev:** 19.60 years
- **Senior Cohort (>60 years):** 21,053 patients (**38.30%** of total admissions)
- *Insight:* Admissions form a rectangular, uniform demographic distribution across adult age brackets, requiring robust comorbidity management for middle-aged and elderly cohorts.

---

### Question 3: Long-term Treatment Cost Stability
> **"How has average treatment cost changed over time?"**

- Across 61 continuous months (May 2019 to May 2024), monthly mean billing remained tightly range-bound between **$24,800** and **$26,400**.
- Overall population billing average: **$25,594.54**.
- *Insight:* Demonstrates strict fee-for-service chargemaster standardization with absence of sudden inflationary swings or procedural pricing shocks.

---

### Question 4: Age vs. Billing Correlation
> **"Is there a relationship between patient age and treatment cost?"**

- **Correlation Coefficient ($r$):** $\approx 0.00$
- **Senior Citizen Average Bill (>60 yrs):** **$25,601.78**
- **Under-60 Population Average Bill:** **$25,590.04**
- *Insight:* Chronological age does not drive healthcare charges; procedural intervention, room allocation, and duration of stay dictate billing.

---

### Question 5: Insurance Provider Market Share
> **"Which insurance covers the most patients?"**

| Insurance Provider | Covered Patients | Share |
| :--- | :---: | :---: |
| **Cigna** | 11,139 | 20.27% |
| **Medicare** | 11,039 | 20.08% |
| **UnitedHealthcare** | 11,014 | 20.04% |
| **Blue Cross** | 10,952 | 19.93% |
| **Aetna** | 10,822 | 19.69% |

*Insight:* The payer mix is evenly distributed (~20% each), insulating the hospital system against individual payer default or hostile contract renegotiations.

---

## 🚨 Ad-Hoc Strategic Healthcare Scenarios

### Scenario I: Emergency Department Capacity Planning
- **Objective:** Plan physician and nursing rosters for emergency cases across calendar months.
- **Findings:** Out of 18,102 emergency admissions, monthly volume averages **1,508 cases/month** (range: 1,440 to 1,570).
- **Recommendation:** Maintain steady baseline staffing year-round rather than volatile seasonal scaling.

### Scenario II: High-Cost Patient Cohort (Top 10%)
- **Objective:** Identify outlier billing accounts for financial audits and pre-authorization reviews.
- **90th Percentile Cutoff:** **$45,160.26**
- **High-Cost Cohort:** **5,497 accounts** (peak billing: $52,764.28).
- **Recommendation:** Mandatory pre-bill audits for accounts over $45,000 to minimize insurer denials.

### Scenario III: Urgent Abnormal Diagnostic Flagging
- **Objective:** Triage high-acuity emergency cases with abnormal diagnostic test results.
- **Identified Cohort:** **6,038 patients** (33.35% of emergency admissions, 10.98% of total network volume).
- **Recommendation:** Establish automatic electronic health record (EHR) priority alerts for same-day specialist follow-ups and discharge tracking.

---

## 💻 Interactive Analytics Dashboard

The project features a full-featured, responsive web dashboard located in [`Healthcare Analytics Dashboard.html`](Healthcare%20Analytics%20Dashboard.html):

- **Key Highlights:**
  - Modern StarAdmin-style UI with teal/navy medical color palette.
  - 5 Interactive KPI Metric Cards (Validated Encounters, Avg Cost, Avg Stay, High-Cost Cases, Critical Review Cases).
  - Highcharts/Canvas visual modules for Condition Inflow, Admission Breakdown, and Insurance Portfolios.
  - Searchable, sortable interactive patient table.
  - Zero external dependencies: open directly in any browser (Chrome, Edge, Firefox).

```bash
# View dashboard in your default browser:
start "Healthcare Analytics Dashboard.html"   # Windows
open "Healthcare Analytics Dashboard.html"    # macOS
xdg-open "Healthcare Analytics Dashboard.html" # Linux
```

---

## 📂 Project File Structure

```
IBM Skillbuild Project/
│
├── Dataset/
│   └── healthcare_dataset.csv              # Raw medical dataset (55,500 records)
│
├── assets/
│   ├── fig1_medical_conditions.png         # Chart: Patient volume by condition
│   ├── fig2_age_distribution.png           # Chart: Patient age demographics
│   ├── fig3_cost_trend.png                 # Chart: Longitudinal cost trend
│   ├── fig4_insurance_share.png            # Chart: Insurance portfolio breakdown
│   └── fig5_emergency_monthly.png          # Chart: Monthly emergency capacity
│
├── healthcare.ipynb                        # Primary Jupyter Notebook with all analytics
├── Healthcare Analytics Dashboard.html     # Interactive responsive web dashboard
├── Healthcare_Analytics_Project_Report.docx# Formal executive report document
├── requirements.txt                        # Python environment dependencies
└── README.md                               # Project documentation & summary
```

---

## ⚙️ Installation & Execution Guide

### 1. Clone or Open Workspace
```bash
git clone <repository-url>
cd "IBM Skillbuild Project"
```

### 2. Set Up Virtual Environment & Dependencies
```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# Linux/macOS:
source .venv/bin/activate

pip install -r requirements.txt
```

### 3. Launch the Jupyter Notebook
```bash
jupyter notebook healthcare.ipynb
```

---

## 💡 Key Strategic Recommendations

1. **Multidisciplinary Chronic Care Pathways:** Deploy shared care protocols across Orthopedics, Endocrinology, and Cardiology to streamline treatment for the six equally prevalent conditions.
2. **Automated Abnormal Lab Emergency Protocols:** Institute priority clinical alerts for the 6,038 emergency patients presenting abnormal test results.
3. **Targeted Utilization Management for High-Cost Claims:** Focus financial review on the 5,497 cases exceeding $45,160.26 to ensure coding compliance and protect hospital operating margins.
4. **Stable Emergency Staffing Model:** Rely on continuous core nursing and doctor staffing models, as emergency demand remains uniform month over month.

---

## 👥 Author & Acknowledgments

- **Author / Analyst:** Project Intern & Contributor
- **Program:** IBM SkillsBuild for Academia & Data Analytics Internship
- **Tools:** Python 3, Pandas, NumPy, Matplotlib, Jupyter Notebook, python-docx, HTML5/CSS3/JavaScript.

*Submitted in fulfillment of the IBM SkillsBuild Project Milestone.*
