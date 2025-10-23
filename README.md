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
- **Power BI:** DAX & Visualization, Data modeling, and reporting  
- **Data Model:** Relational model connecting encounters, patients, payers, and procedures

---

## 🧹 Data Cleaning & Preparation (Power Query)

### 🔍 Initial Data Review
- **Data Upload:** Imported multiple CSV files into Power Query, saved in the Power BI data model.  
- **Data Preview and Validation:**  
  - Check **“Value distribution and count, distinct count, Null value, Min and Max”** under the *View → Data Preview → Column Statistics* menu.  
  - Used **Column Statistics**, **Column Quality**, or **Statistics under Transform** to validate completeness, distinct counts, and null values.  

---

## 🧠 Data Modeling
Established relationships between tables:
- `Patients` (Dimension)  
- `Encounters` (Fact Table)  
- `Payers` (Dimension)  
- `Procedures` (Fact Table)

Each table connected via appropriate foreign keys (e.g., PatientID, PayerID, ProcedureID).

---

## 📊 Power BI Analysis & Visualization

### 🔹 Encounter Analysis

#### Encounter Volume & Trends
- Visualizations:**Matrix Table**
  - **Rows:** Date  
  - **Values:** Count of Encounter IDs  
- Added **Quick Measure:** *Count of Encounters*  
- Applied *Conditional Formatting* to highlight monthly variations.

🖼️ *Placeholder for visual:*  
`![Encounter Volume Trend](images/encounter_volume.png)`

---

#### Encounter Class Distribution
- Visualizations: **Matrix visual**
  - **Rows:** Encounter Class  
  - **Values:** Count of IDs and Count of IDs show as % of Grand Total

🧩 **Key Power BI Features Used:**
- *Show Values As → % of Grand Total*  
- *Conditional Formatting → Background Color Scales*

🖼️ *Placeholder for visual:*  
`![Encounter Class Distribution](images/encounter_class_distribution.png)`

---

#### Age & Gender Breakdown
Converted **Birthday → Age → Duration: Day → Age Range** in Power Query.

#### Power Query: Create Age Range Column
- 🧮 Method 1: **M code**
     ```
      = if [Age] >= 100 then "100+"
        else Text.From(Number.RoundDown([Age]/10)*10) & "-" & Text.From(Number.RoundDown([Age]/10)*10 + 9)
     ```
- 🧮 Method 2: 
    **Conditional Column**

- Visualizations: **Matrix Table**:
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
- Visualizations: **Matrix visuals**
  - **Rows:** Procedure Description  
  - **Values:** Count of IDs and Average of total claim cost 
- Applied **Top N Filter (10)** on Procedure Description by value (count of Id).

🖼️ *Placeholder for visual:*  
`![Top Procedures](images/top_procedures.png)`

---

### ⏱️ Length of Stay (LOS) Analysis

Created a **measure** to calculate average length of stay:

```DAX
Average_Length_Of_Stay =
DATEDIFF('Encounters'[START], 'Encounters'[STOP], HOUR)
```

- Visualizations:  **Matrix**:
  - **Rows:** Age Range, Gender  
  - **Columns:** Encounter Class  
  - **Values:** Average LOS (in Hours)  

🖼️ *Placeholder for visual:*  
`![Average LOS](images/los_analysis.png)`

---

### 🔁 Readmissions & Follow-up Visits(within 30 days) (Power Query Workflow)

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
5. **Create a new file with previous and next values:**
   - Duplicate the query → name it PrevTable.
   - In PrevTable, add a Column shifting index by +1: Subtract or Add 1 to Index using “Standard” under Add column
6. **Merge two tables:**   
   - Merge on both CustomerID and Index
   - Expand only the Start Date column from the merged table→ rename it to Prev/Next Encounter Date
7. **Calculate Readmission Interval:**  
   - Use **Add Column → Date → Subtract Dates** to compute interval between `Prev Encounter` and `Current Stop Date`

#### DAX Measure for 30-Day Readmissions:
```DAX
ReadmissionWithin30Days =
CALCULATE(
    DISTINCTCOUNT(Encounters_Sorted[Patient]),
    Encounters_Sorted[Encounter_Date_Delta] < 30
)
```
- Visualizations:  **Card**

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
| **Power Query** | Column Profile, Conditional Column, Group By, M language, Merge | Data preparation and structuring |
| **DAX** | `Variables (VAR)`, `CALCULATE`, `Aggregation(SUM, AVG, COUNT, DISTINCT COUNT)`, `TIME INTELLIGENCE(DATEDIFF)`,  | Custom KPI and measure calculations |
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

