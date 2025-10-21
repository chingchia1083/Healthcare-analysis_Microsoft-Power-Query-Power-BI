# 🏥 Healthcare Analytics Case Study — Power Query & Power BI

## 📘 Introduction
This case study demonstrates a complete **Power Query and Power BI analytics workflow** for a healthcare consulting project.  
The goal of this analysis is to help hospitals leverage data to **improve patient care**, **reduce operational costs**, and **enhance efficiency** through data-driven insights.

---

## 🎯 Business Objective
**Task:** Analyze patient encounters, costs, coverage, and behavioral trends to support hospital operations and decision-making.

**Key Stakeholders:**  
- Healthcare Operations & Strategy Teams  
- Financial Planning Analysts  
- Hospital Administration

---

## 🧩 Data Source & Tools
**Data Source:**  
[Data Drills – Maven Analytics](https://mavenanalytics.io/data-drills)

**Tools & Environment:**
- **Power Query:** Data cleaning, transformation, and preparation  
- **Power BI:** Visualization, DAX modeling, and reporting  
- **Data Model:** Relational model connecting encounters, patients, payers, and procedures

---

## 🧹 Data Cleaning & Preparation (Power Query)

### 🔍 Initial Data Review
- **Data Upload:** Imported multiple CSV files into Power Query, saved in the Power BI data model.  
- **Column Profiling:**  
  - Enabled **“Column profiling based on entire dataset”** under the *View → Data Preview* menu.  
  - Used **Column Statistics**, **Column Quality**, and **Value Distribution** to validate completeness, distinct counts, and null values.  

### ⚙️ Data Validation
- Used **Column Quality** indicators to detect *empty* or *error* values.  
- Applied **Transform → Statistics** to check *min, max, mean,* and *distribution* for numerical fields.  
- Identified and confirmed primary keys and foreign key relationships between tables.

### 🧮 Power Query Functions Applied
| Purpose | Function / Tool | Description |
|----------|------------------|-------------|
| Column Quality Check | `Table.Profile()` | Evaluates nulls, errors, and unique values |
| Convert Birthdate to Age | `Duration.Days()` + `DateTime.LocalNow()` | Calculates age dynamically |
| Create Age Range | `Number.RoundDown()` + `if...then...else` | Groups age into 10-year intervals |
| Sort & Group | `Table.Sort()` + `Table.Group()` | Prepares dataset for readmission analysis |
| Index Creation | `Table.AddIndexColumn()` | Generates sequential index per patient |
| Merge Queries | `Table.NestedJoin()` | Merges encounters to create lead/lag comparisons |

---

## 🧠 Data Modeling
Established relationships between tables:
- `Patients` (Dimension)  
- `Encounters` (Fact Table)  
- `Payers` (Dimension)  
- `Procedures` (Dimension)

Each table connected via appropriate foreign keys (e.g., PatientID, PayerID, ProcedureID).

---

## 📊 Power BI Analysis & Visualization

### 🔹 Encounter Analysis

#### Encounter Volume & Trends
- Built a **Matrix Table**:  
  - **Rows:** Date hierarchy (Year → Month → Day)  
  - **Values:** Count of Encounter IDs  
- Added **Quick Measure:** *Count of Encounters*  
- Applied *Conditional Formatting* to highlight monthly variations.

🖼️ *Placeholder for visual:*  
`![Encounter Volume Trend](images/encounter_volume.png)`

---

#### Encounter Class Distribution
- Built a **Matrix visual** with *Encounter Class* in Rows and *Count of IDs* in Values.  
- Added % of Grand Total to show share of each class.  

🧩 **Key Power BI Features Used:**
- *Show Values As → % of Grand Total*  
- *Conditional Formatting → Background Color Scales*

🖼️ *Placeholder for visual:*  
`![Encounter Class Distribution](images/encounter_class_distribution.png)`

---

#### Age & Gender Breakdown
Converted **Birthday → Age → Age Range** in Power Query.

**Power Query Formula (M Code):**
```m
= if [Age] >= 100 then "100+"
  else Text.From(Number.RoundDown([Age]/10)*10) & "-" & Text.From(Number.RoundDown([Age]/10)*10 + 9)
```

Built a **Matrix Table**:
- **Rows:** Encounter Class, Gender  
- **Columns:** Age Range  
- **Values:** Count of Encounters  

🖼️ *Placeholder for visual:*  
`![Encounter by Age & Gender](images/encounter_age_gender.png)`

---

### 💰 Cost & Coverage Insights

#### Zero Payer Coverage Analysis
Created a **DAX Measure**:

```DAX
Zero_Payer_Coverage% =
VAR total_Id = COUNTROWS(Encounters)
VAR zero_payer = CALCULATE(COUNTROWS(Encounters), Encounters[PAYER_COVERAGE] = 0)
RETURN DIVIDE(zero_payer, total_Id)
```

Displayed using a **Card Visual** for overall percentage of zero-payer encounters.

🖼️ *Placeholder for visual:*  
`![Zero Payer Coverage Card](images/zero_payer_card.png)`

---

#### Top 10 Procedures by Frequency & Cost
Created **Matrix visuals**:
- **Rows:** Procedure Description  
- **Values:** Count of IDs and Average Base Cost  
- Applied **Top N Filter (10)** on Procedure Description by value count.

🖼️ *Placeholder for visual:*  
`![Top Procedures](images/top_procedures.png)`

---

### ⏱️ Length of Stay (LOS) Analysis

Created a **measure** to calculate average length of stay:

```DAX
Average_Length_Of_Stay =
DATEDIFF('Encounters'[START], 'Encounters'[STOP], HOUR)
```

Built a **Matrix**:
- **Rows:** Age Range, Gender  
- **Columns:** Encounter Class  
- **Values:** Average LOS (in Hours)  

🖼️ *Placeholder for visual:*  
`![Average LOS](images/los_analysis.png)`

---

### 🔁 Readmissions & Follow-up Visits (Power Query Workflow)

#### Step-by-step in Power Query
1. **Sort Data:**  
   - Sort by `Patient` and `Start Date`
2. **Group By:**  
   - Group by `Patient`, operation = “All Rows”
3. **Add Index:**  
   ```m
   Table.AddIndexColumn([GroupedTable], "Index", 0, 1)
   ```
4. **Expand Back:**  
   - Expand nested table to flatten grouped structure
5. **Shift Index:**  
   - Create `PrevTable` with index +1  
   - Merge with original to get previous encounter date
6. **Calculate Readmission Interval:**  
   - Use **Add Column → Date → Subtract Dates** to compute interval between `Prev Encounter` and `Current Stop Date`

#### DAX Measure for 30-Day Readmissions:
```DAX
ReadmissionWithin30Days =
CALCULATE(
    DISTINCTCOUNT(Encounters_Sorted[Patient]),
    Encounters_Sorted[Encounter_Date_Delta] < 30
)
```

🖼️ *Placeholder for visual:*  
`![Readmission Analysis](images/readmission_30days.png)`

---

## 📈 Key Insights
- **Ambulatory encounters** account for **45% of total**, followed by **Outpatient (23%)**.  
- **Females (14,924)** slightly exceed **Males (12,967)** in total encounters.  
- **Peak Age Group:** 90–99 years (11,030 encounters).  
- **49%** of encounters show **zero payer coverage**.  
- **Males 30–39** have the **longest average LOS (Ambulatory)**.  
- **Females 70–79** have the **highest LOS in Inpatient class**.

---

## 🛠️ Power Platform Features Highlighted
| Area | Feature | Purpose |
|------|----------|----------|
| **Power Query** | Column Profiling, Conditional Column, Group By, Add Index | Data validation and structuring |
| **DAX** | `VAR`, `CALCULATE`, `DIVIDE`, `DATEDIFF`, `DISTINCTCOUNT` | Custom KPI and measure calculations |
| **Power BI Visuals** | Matrix, Card, Conditional Formatting, Top N Filter | Dynamic insight presentation |

---

## 📎 Author
👤 **Chingchia Yu**  
Senior Financial Analyst | Power BI & Data Analytics Specialist  
📫 [LinkedIn](https://www.linkedin.com/in/your-linkedin-profile)  

---

## 🖼️ Visualization Placeholders
- `images/encounter_volume.png` – Encounter volume trend  
- `images/encounter_class_distribution.png` – Encounter class % by year  
- `images/encounter_age_gender.png` – Encounters by age and gender  
- `images/zero_payer_card.png` – Zero payer percentage  
- `images/top_procedures.png` – Top 10 procedures  
- `images/los_analysis.png` – Average length of stay  
- `images/readmission_30days.png` – Readmission analysis

---

