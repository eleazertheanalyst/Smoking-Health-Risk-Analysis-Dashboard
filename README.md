
# Smoking Health Risk Analysis Dashboard
### Power BI | DAX | Data Modeling | Healthcare Analytics

[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?logo=powerbi)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/DAX-Data%20Analysis-blue)](https://learn.microsoft.com/en-us/dax/)
[![Healthcare Analytics](https://img.shields.io/badge/Domain-Healthcare%20Analytics-green)]()

### [View Interactive Power BI Report](https://app.fabric.microsoft.com/groups/48a813aa-92c7-4a26-8dc5-f4f9ebb5db61/reports/861d1911-9487-430b-97ed-ac47a924a7fe?experience=fabric-developer)

---

## Project Overview

This project delivers an interactive healthcare analytics dashboard designed to analyze the physiological impact of smoking behaviors across specific anatomical organs, clinical risk markers, and demographic cohorts.

The objective was to evaluate how smoking duration, daily cigarette intake, and lifestyle factors correlate with localized organ damage (`Heart`, `Lungs`, `Kidney`, `Liver`, and `Human Body`).

To create an engaging and intuitive analytical experience, the dashboard incorporates dynamic visual rendering through web-hosted image URLs, relational data modeling, and interactive cross-filtering.

Users can select specific anatomical targets and toggle between `Damaged` and `Healthy` clinical states. These selections dynamically update high-resolution 3D anatomical models and re-evaluate the associated clinical and demographic metrics.

---

## Dashboard in Action

The following demonstration showcases the interactive functionality of the Power BI dashboard, including organ selection, clinical condition toggles, and dynamic visual updates.

![Interactive Dashboard Demo](assets/dashboard_demo.gif)

---

## Dashboard Overview

The dashboard combines healthcare KPIs, smoking behavior analysis, demographic comparisons, and clinical risk indicators into a single interactive reporting interface.

<img width="1445" height="811" alt="Image" src="https://github.com/user-attachments/assets/24e15ead-81b2-4b4b-a67b-4fa446c284fc" />

---

## Report Views: Healthy vs. Damaged Analysis

One of the key features of this project is the ability to dynamically compare healthy and damaged anatomical conditions.

The following screenshots demonstrate how the dashboard changes when the selected organ's clinical condition is toggled.

| Healthy Lung State | Damaged Lung State |
|:---:|:---:|
<img width="1447" height="810" alt="Image" src="https://github.com/user-attachments/assets/688dce99-2ec6-457e-a856-c07953950ba2" /> | <img width="1446" height="810" alt="Image" src="https://github.com/user-attachments/assets/2f608200-71ee-4591-91c7-d3ed6cb1771e" /> |

---

## Data Model & Schema Architecture

The Power BI data model was designed to support dynamic filtering across patient clinical records, anatomical organs, pathological conditions, and image assets.

<img width="1917" height="975" alt="Image" src="https://github.com/user-attachments/assets/a71f8b86-afaf-43c2-8b65-4d747637fddc" />

### Data Modeling Approach

- Designed a relational schema connecting patient clinical records (`health_dataset`) with lookup dimensions for organs (`Organs`) and pathological states (`condition`).
- Created an `Image Dataset` bridge table pairing composite keys (`Organ` + `Organ_Condition`) with web-hosted anatomical image assets.
- Configured image URL data categories to enable dynamic rendering of high-resolution anatomical models.
- Established one-to-many and bidirectional relationships to propagate filter contexts across the core fact table and dynamic media visuals.

---

## Clinical & Business Objectives

The primary objectives of this analysis were to:

- Quantify patient distributions across smoking categories (`Never`, `Current`, and `Former` smokers).
- Track biometric deviations by comparing filtered cohort averages (`vs Avg Age` and `vs Avg BMI`) against population-wide baselines.
- Measure exposure patterns by analyzing Years of Smoking (`YOS`) versus Cigarettes Per Day (`CPD`) across standardized age cohorts.
- Evaluate cardiovascular comorbidities by examining cholesterol and hypertension risk tiers across life stages.
- Examine demographic variations across biological sex and age groups.
- Develop an interactive analytical interface featuring dynamic anatomical visual feedback.

---

## Steps Followed

### Phase 1: Data Modeling & Architecture

- Designed a relational data model linking patient clinical records with organ and condition lookup tables.
- Created an image mapping table to dynamically associate anatomical organs with their corresponding healthy or damaged visual states.
- Configured image URL data categories for dynamic anatomical image rendering.
- Established relationships to enable interactive cross-filtering across dashboard components.

### Phase 2: Feature Engineering & DAX Calculations

#### 1. Demographic Cohort Bins (`Age Group` - Calculated Column)

Created age groups to analyze demographic and health risk patterns across standardized age cohorts.

```dax
Age Group =
SWITCH(
    TRUE(),
    health_dataset[Age] <= 28, "18-28",
    health_dataset[Age] <= 38, "29-38",
    health_dataset[Age] <= 48, "39-48",
    health_dataset[Age] <= 58, "49-58",
    health_dataset[Age] <= 68, "59-68",
    "69+"
)
```

#### 2. Dynamic Age Benchmark Variance (`vs Avg Age` - Measure)

Evaluates the active cohort's average age against the overall population average using directional indicators.

```dax
vs Avg Age =
VAR _CurrentAge =
    AVERAGE(health_dataset[Age])
VAR _OverallAge =
    CALCULATE(
        AVERAGE(health_dataset[Age]),
        ALL(health_dataset)
    )
VAR _Diff = _CurrentAge - _OverallAge

RETURN
    SWITCH(
        TRUE(),
        _Diff > 0, UNICHAR(9650) & " " & FORMAT(_CurrentAge, "0.0"),
        _Diff < 0, UNICHAR(9660) & " " & FORMAT(_CurrentAge, "0.0"),
        FORMAT(_CurrentAge, "0.0")
    )
```

#### 3. Dynamic BMI Benchmark Variance (`vs Avg BMI` - Measure)

Compares the active cohort's average Body Mass Index against the overall dataset average.

```dax
vs Avg BMI =
VAR _CurrentBMI =
    AVERAGE(health_dataset[BMI])
VAR _OverallBMI =
    CALCULATE(
        AVERAGE(health_dataset[BMI]),
        ALL(health_dataset)
    )
VAR _Diff = _CurrentBMI - _OverallBMI

RETURN
    SWITCH(
        TRUE(),
        _Diff > 0, UNICHAR(9650) & " " & FORMAT(_CurrentBMI, "0.0"),
        _Diff < 0, UNICHAR(9660) & " " & FORMAT(_CurrentBMI, "0.0"),
        FORMAT(_CurrentBMI, "0.0")
    )
```

### Phase 3: Power BI Dashboard Development

#### Navigation & Interactive Controls

- Vertical icon navigation for selecting anatomical organs (`Heart`, `Human Body`, `Kidney`, `Liver`, and `Lungs`).
- Segmented condition toggle for switching between `Damaged` and `Healthy` states.
- Interactive slicers and cross-filtering for exploring patient demographics and clinical indicators.

#### Visual Implementations

- **Dynamic Anatomical Display:** Central visual rendering the selected organ based on the active organ and clinical condition filters.
- **Cohort Summary KPI Card:** Displays total patient count alongside dynamic average age and BMI benchmarks.
- **Smoking Status Share:** Donut chart showing the distribution of `Never`, `Current`, and `Former` smokers.
- **Exposure Trajectory:** Dual-line chart plotting Years of Smoking (`YOS`) and Cigarettes Per Day (`CPD`) across age groups.
- **Smoking Status by Gender:** Ribbon chart illustrating male and female distributions across smoking categories.
- **Cholesterol & Hypertension Risk:** Stacked column chart displaying risk distribution (`High`, `Low`, and `Normal`) across age groups.

## Key Insights & Analytical Findings

### 1. Smoking Status & Organ Damage Correlation

The analyzed dataset reveals a higher proportion of current smokers within several organ-damage cohorts.

- **Damaged Kidney Cohort:** Current smokers account for **51.72%** of patients, former smokers represent **26.44%**, and never-smokers account for **21.84%**.
- **Damaged Full Body Cohort:** Current smokers represent **50.97%** of patients.
- **Damaged Lungs Cohort:** Current smokers comprise **48.81%** of cases.
- In healthy cohorts, never-smokers and former smokers account for approximately 60% or more combined.

**Key Insight:** Active smoking is associated with a larger share of patients in the organ-damage cohorts examined in this dataset.

### 2. Biometric Deviations (Age & BMI Benchmarks)

The analysis reveals variations in average age across organ-damage categories.

- Damaged Heart cohort average age: **54.9**.
- Damaged Human Body cohort average age: **57.5**.
- Damaged Liver cohort average age: **52.5**.
- Damaged Lung cohort average age: **51.1**.

**Key Insight:** The differences in average age across organ-damage categories demonstrate the value of examining demographic characteristics alongside smoking exposure.

### 3. Exposure Intensity (Years of Smoking vs. Daily Intake)

The analysis identifies different patterns in daily cigarette consumption and cumulative smoking duration.

- Daily consumption (`CPD`) peaks in the **39-48** and **49-58** age cohorts.
- Cumulative smoking duration (`YOS`) increases with age, reaching its highest levels in the **59-68** and **69+** cohorts.

**Key Insight:** Middle-aged cohorts exhibit higher daily cigarette consumption, while older cohorts tend to have longer cumulative smoking histories.

### 4. Cardiovascular & Hypertension Comorbidity

- Elevated cholesterol and hypertension risk categories are represented across the analyzed age groups.
- Higher volumes of elevated cardiovascular risk are observed in the **29-38**, **49-58**, and **69+** age groups.

**Key Insight:** Examining smoking patterns alongside cholesterol and hypertension provides a broader understanding of potential cardiovascular health concerns.

---

## Clinical Recommendations

Based on the observed patterns in the dataset, the following areas may warrant further investigation:

- **Targeted Screening:** Examine pulmonary and hepatic health among smokers aged 35-50, particularly where daily cigarette consumption is elevated.
- **Cardiovascular Monitoring:** Evaluate blood pressure and lipid profiles among individuals with extended smoking histories, particularly those with elevated BMI.
- **Smoking Cessation Awareness:** Use the dashboard to communicate smoking-related health risks and support smoking cessation awareness initiatives.

> **Disclaimer:** This project is intended for educational and analytical purposes only. The findings represent associations observed in the analyzed dataset and do not establish causation or constitute clinical recommendations. The dashboard is not intended for medical diagnosis or treatment decisions.

---

## Technical Skills Demonstrated

| Category | Skills & Tools |
|---|---|
| Data Modeling | Relational Data Modeling, Star/Snowflake Schema, Bridge Tables |
| DAX Calculations | CALCULATE, ALL, SWITCH, UNICHAR, AVERAGE, FORMAT |
| Power BI | Interactive Dashboards, KPI Cards, Slicers, Cross-Filtering |
| Data Visualization | Donut Charts, Line Charts, Ribbon Charts, Stacked Column Charts |
| Dynamic Media | Image URL Integration, Dynamic Anatomical Visualization |
| Data Storytelling | Healthcare Analytics, Demographic Analysis, Risk Pattern Identification |

---

## Project Takeaways

This project demonstrates how Power BI can be used beyond traditional reporting to build an interactive healthcare analytics experience.

By combining relational data modeling, DAX calculations, dynamic image rendering, and interactive visualizations, the dashboard provides an engaging way to explore smoking behaviors, demographic characteristics, and organ-related health indicators.

The project strengthened my practical experience in:

- Designing relational data models.
- Developing dynamic DAX measures.
- Building interactive Power BI dashboards.
- Integrating external image assets into Power BI.
- Creating dynamic visual experiences.
- Translating healthcare datasets into meaningful analytical insights.

