# Diabetic Patient Encounter Analytics

An advanced Power BI analytical solution examining hospital readmission risk, clinical complexity, and operational performance across 130 US hospitals from 1999 to 2008.

---

## Student Information

| | |
|---|---|
| **Name** | Nathan Orang'o Omenge |
| **Admission Number** | 670637 |
| **Course Code** | DSA 3050A |
| **Examination** | Advanced Power BI Examination, SS 2026 |
| **Institution** | United States International University — Africa |

---

## Project Overview

This project is a complete Business Intelligence solution built in Power BI analysing 10 years of diabetic patient encounter data from 130 US hospitals. The dashboard surfaces readmission risk patterns, clinical complexity trends, and operational performance insights for hospital management stakeholders.

Every stage of the BI development process was executed from scratch — from raw data acquisition and Power Query transformation, through relational data modeling and DAX measure development, to a three-page interactive dashboard and written analytical interpretation.

---

## Problem Statement

Hospital readmissions within 30 days of discharge represent a major quality indicator and a significant cost driver for healthcare systems. In the US, 30-day readmissions cost the healthcare system over $26 billion annually.

This analysis addresses three core questions for a hospital network:

1. Which patient groups face the highest 30-day readmission risk?
2. Which clinical factors are associated with prolonged hospital stays?
3. Where can operational performance be improved to reduce preventable readmissions?

---

## Dataset Description

| | |
|---|---|
| **Source** | UCI Machine Learning Repository |
| **Citation** | Strack, B., DeShazo, J.P., Gennings, C., et al. (2014). Impact of HbA1c Measurement on Hospital Readmission Rates. BioMed Research International. DOI: 10.24432/C5230J |
| **Main file** | `diabetic_data.csv` — 101,766 rows, 50 columns |
| **Lookup file** | `IDS_mapping.csv` — admission type, discharge disposition, and admission source IDs |
| **Period** | 1999 to 2008 (10 years) |
| **Scope** | 130 US hospitals and integrated delivery networks |
| **Unique patients** | 71,518 (30,248 with more than one encounter) |

The dataset contains both categorical variables (race, gender, age bracket, admission type, diagnosis codes) and numerical variables (length of stay, lab procedures, medications, diagnoses), supporting advanced analysis and time intelligence.

---

## Tools Used

- **Power BI Desktop** — report development and dashboard design
- **Power Query (M)** — data cleaning and transformation
- **DAX** — measures, calculated columns, and time intelligence
- **GitHub** — version control and documentation
- **Microsoft Word / PDF** — report authoring and export

---

## Steps Followed

1. Loaded `diabetic_data.csv` and `IDS_mapping.csv` into Power BI Desktop
2. Cleaned and shaped the data in Power Query through 10 documented transformation steps
3. Built a star schema with one fact table and five dimension tables
4. Created 10 DAX measures and 2 calculated columns
5. Designed a three-page interactive dashboard with consistent dark theme
6. Derived five analytical insights and three business recommendations
7. Documented every stage in a detailed PDF report

---

## Dashboard Features

### Page 1 — Executive Summary
High-level performance snapshot for stakeholders.
- Four KPI cards: Total Encounters, Distinct Patients, Readmission Rate %, Avg Length of Stay
- Year slicer (tile-style) filtering all visuals
- Encounter trend line chart (1999–2008)
- Encounters by age group (horizontal bar)
- Admission type breakdown (donut chart)
- Readmission rate by race (bar chart surfacing health equity disparities)

### Page 2 — Clinical Deep Dive
Granular analysis for clinical and operational decision-makers.
- Readmission matrix by diagnosis category × age group with conditional formatting
- Decomposition tree for interactive exploration of drivers
- Scatter plot — medications vs length of stay
- Diagnosis hierarchy bar chart
- High-risk encounter detail table (filtered to readmitted < 30)

### Page 3 — Performance Monitoring
Time-based analysis and performance benchmarking.
- YTD vs PY encounters line chart
- Readmission rate by year (column chart)
- Top diagnoses by readmission rate (Top N filter)
- Medication change by readmission status (100% stacked bar)
- Emergency Admission % KPI card (35.37%)
- Waterfall chart — encounter volume change by year

---

## Key DAX Measures

```DAX
Total Encounters = COUNTROWS(diabetic_data)

Distinct Patients = DISTINCTCOUNT(diabetic_data[patient_nbr])

Readmission Rate % =
DIVIDE(
    SUM(diabetic_data[readmit_30day_flag]),
    [Total Encounters], 0
) * 100

Avg Length of Stay = AVERAGE(diabetic_data[time_in_hospital])

Emergency Admission % =
DIVIDE(
    CALCULATE([Total Encounters],
        dim_admission_type[description] = "Emergency"),
    [Total Encounters], 0
) * 100

YTD Encounters =
CALCULATE(
    [Total Encounters],
    FILTER(dim_date, dim_date[Year] <= MAX(dim_date[Year]))
)

PY Encounters =
CALCULATE(
    [Total Encounters],
    FILTER(dim_date, dim_date[Year] = MAX(dim_date[Year]) - 1)
)

YoY Growth % =
DIVIDE(
    [Total Encounters] - [PY Encounters],
    [PY Encounters], 0
) * 100

Diagnosis Rank =
RANKX(
    ALL(diabetic_data[diagnosis_category]),
    [Total Encounters], , DESC, DENSE
)
```

Two calculated columns were created in Power Query:

- `readmit_30day_flag` — binary flag (1 if readmitted within 30 days, else 0)
- `diagnosis_category` — maps raw ICD-9 codes to readable clinical categories

---

## Key Insights

1. **Young respiratory patients carry disproportionately high readmission risk.** Patients in the 20–30 age group with respiratory diagnoses show a 67% readmission rate — more than six times the dataset average of 11.2%.

2. **The 70–80 cohort drives both the highest volume and above-average risk.** This group contributes 26,068 encounters and sustains elevated readmission rates across multiple diagnosis categories, placing the greatest strain on inpatient capacity.

3. **Emergency admissions make up 35.37% of all encounters.** For a manageable chronic condition like diabetes, this indicates a significant gap in community-based disease management.

4. **Encounter volumes declined after 2004 but readmission rates did not.** Volume reduction did not translate into proportional quality improvement — the two require different interventions.

5. **The "Other" diagnosis category has the highest readmission rate across all age groups.** Clinically complex cases falling outside standard ICD-9 categories appear to receive less targeted discharge planning.

---

## Challenges Encountered

- **Stacked lookup tables.** `IDS_mapping.csv` contained all three dimension lookups in a single CSV with blank separator rows and repeated headers. Required manual splitting into three reference queries with error-row filtering.
- **No date column in the dataset.** The dataset has no explicit timestamp, so the Year column was derived by mapping `encounter_id` proportionally across the 10-year period (IDs are assigned sequentially).
- **Many-to-many cardinality errors.** Initial relationship creation produced M:M cardinality due to duplicate ID values in the lookup tables. Resolved by deduplicating IDs before creating relationships.
- **Missing values encoded as "?".** The dataset used "?" instead of null across multiple columns. Required a global Replace Values step in Power Query to convert them to proper nulls before the data model would treat missingness correctly.
- **Five columns with heavy missingness.** `weight` (97%), `max_glu_serum` (95%), `A1Cresult` (83%), `medical_specialty` (49%), and `payer_code` (40%) were removed rather than imputed, as imputation at those rates would produce misleading visuals.

---

## Conclusion

This analysis demonstrates that readmission risk in diabetic patients is not uniformly distributed — it concentrates in specific age-diagnosis combinations, is elevated for emergency admissions, and has not improved proportionally despite declining encounter volumes.

Three interventions offer the highest expected return for the hospital network: a targeted post-discharge monitoring programme for young respiratory patients, expanded specialist geriatric outpatient capacity for the 70–80 cohort, and a comprehensive discharge planning protocol for patients with complex or unclassified diagnoses.

---

## Repository Structure

```
DSA3050A-Advanced-PowerBI-Exam/
│
├── data/
│   ├── diabetic_data.csv
│   └── IDS_mapping.csv
│
├── screenshots/
│   ├── Dataset Source and Description/
│   ├── Data Transformation and Power Query/
│   ├── Data Modelling/
│   ├── DAX Measures/
│   └── Dashboard/
│
├── powerbi/
│   └── DSA3050A_Healthcare_BI.pbix
│
├── report/
│   └── DSA3050A_Nathan_Omenge_670637_Report.pdf
│
└── README.md
```

---

*Submitted April 2026 · United States International University — Africa*
