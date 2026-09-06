Enterprise HR Analytics

From messy HR data to decision-ready workforce insights.

📌 Business Context

HR teams need reliable workforce data to understand employee engagement, turnover, training investment, management effectiveness, and workforce composition. In this portfolio project, I treated the dataset as an internal HR analytics case where leadership needs a cleaner and more trustworthy view of its workforce to support retention, L&D, management, and workforce-planning decisions. The original dataset contained duplicates, inconsistent text/date formats, missing termination information, and data-quality issues that could distort downstream HR reporting. The goal was to transform the raw employee records into a validated, analysis-ready dataset and use it to identify actionable workforce patterns.

Data source: Kaggle — IBM HR Analytics Employee Attrition & Performance
Project dataset: Modified/messy HR dataset used for this portfolio case.

🎯 Objectives
Clean and standardize a messy HR dataset before analytical use.
Validate employee, date, score, and employment-status fields.
Perform exploratory analysis across workforce, turnover, engagement, satisfaction, training, management, and demographics.
Identify HR patterns that can support retention, L&D, management, and workforce-planning decisions.
Prepare a production-ready dataset suitable for SQL analysis and future BI reporting.
❓ Business Questions

The analysis focused on the following questions:

Which job roles require the longest average training duration?
What are the primary departure classifications among voluntarily separated staff?
Which supervisors lead the highest-engaged teams?
How does employee headcount per location relate to satisfaction?
Which training programs represent the largest total investment?
Which business divisions have the highest turnover rates?
Are employee engagement, satisfaction, performance ratings, and training costs correlated?
How is gender representation distributed across departments?
📊 Data
Source

Kaggle: IBM HR Analytics Employee Attrition & Performance

Dataset Grain

One row represents one employee record.

Volume
Stage	Rows	Columns
Raw dataset	3,150	39
After removing duplicates	3,000	38
Final cleaned dataset	3,000	40

The raw dataset contained 150 exact duplicate rows, representing approximately 4.76% data inflation. These were removed before analysis so that workforce and training metrics would not be artificially overstated.

Time Coverage
Field	Coverage
Employee Start Date	2018-08-07 → 2023-08-06
Employee Exit Date	2018-11-19 → 2023-08-06
Survey Date	2022-01-09 → 2023-12-07
Training Date	2022-08-05 → 2023-08-05
Workforce Status

The cleaned dataset contains:

2,458 Active
321 Voluntarily Terminated
86 Leave of Absence
69 Future Start
66 Terminated for Cause

Total: 3,000 employee records

🧩 Schema
Category	Fields
Employee Identity	FirstName, LastName, Employee ID, ADEmail
Employment	StartDate, ExitDate, EmployeeStatus, EmployeeType, EmployeeClassificationType, TerminationType, TerminationDescription
Organization	BusinessUnit, DepartmentType, Division, Title, Supervisor, JobFunctionDescription
Compensation Structure	PayZone
Demographics	DOB, State, GenderCode, RaceDesc, MaritalDesc
Location	LocationCode, Location
Performance	Performance Score, Current Employee Rating
Employee Experience	Engagement Score, Satisfaction Score, Work-Life Balance Score
Training	Training Date, Training Program Name, Training Type, Training Outcome, Trainer, Training Duration(Days), Training Cost
Derived Analytics	IsActive, AgeAtStart

The final production dataset contains 40 columns, including the two derived analytical fields IsActive and AgeAtStart.

🛠️ Methodology
1. Data Audit

I first profiled the raw dataset to understand:

Dataset dimensions
Missing values
Duplicate records
Data types
Potential quality issues

This was necessary before analysis because calculating HR metrics on unclean records could produce misleading results.

2. Duplicate Removal

Problem: 150 exact duplicate records were present.

Action: Removed exact duplicates using drop_duplicates().

Why this approach?

Exact duplicate rows provide no additional information and would artificially inflate headcount, training attendance, training spend, and other aggregate metrics. Deduplication was therefore preferred over retaining or arbitrarily aggregating these records.

Result:
3,150 → 3,000 records

3. Data Type Standardization

Date fields were converted into proper datetime values, including:

StartDate
ExitDate
DOB
Survey Date
Training Date

Numeric fields were explicitly converted for:

Engagement Score
Satisfaction Score
Work-Life Balance Score
Current Employee Rating
Training Duration
Training Cost

Categorical fields were converted to appropriate categorical types.

This was preferred over leaving values as strings because HR analysis requires reliable date comparisons, numerical aggregation, and grouping.

The notebook also reduced memory usage by approximately 29.03% through data-type optimization.

4. Missing-Value Treatment

Missing ExitDate values were interpreted in the context of employee status rather than blindly replacing them with a statistical value.

For active employees:

TerminationType → N/A - Active
TerminationDescription → N/A - Active

A derived IsActive flag was also created using employment status and exit-date information.

Why?

Blank termination dates are meaningful in HR data: they can represent employees who have not exited rather than missing numerical information. Therefore, imputation with mean/median or arbitrary dates would have introduced false employment events.

5. Text Standardization

Text fields were:

Trimmed for unnecessary whitespace
Names/titles normalized to title case
Email addresses converted to lowercase

For example, employee names, supervisors, trainers, and job titles were standardized for consistent grouping and reporting.

6. Data Validation

I performed rule-based validation instead of relying only on missing-value checks.

Validation included:

Age validation

Employees were checked for an age below 18 at the start of employment.

Detected: 5 records.

Date sequence validation

Checked whether an employee's exit date occurred before their start date.

Detected: 0 violations.

Score validation

Checked whether:

Engagement Score
Satisfaction Score
Work-Life Balance Score
Current Employee Rating

fell outside the expected 1–5 range.

Detected: 0 score violations.

🔎 Exploratory Data Analysis

Rather than generating charts without a decision purpose, the analysis was structured around specific HR business questions.

1. Training Duration by Job Role

The roles with the highest average training duration included:

Role	Avg. Training Duration
Principal Data Architect	3.75 days
President & CEO	3.73 days
Enterprise Architect	3.60 days
CIO	3.45 days
Sr. Accountant	3.38 days

Insight: Executive and specialized technical architecture roles require longer average training periods, suggesting that L&D planning should account for role complexity rather than applying a uniform training-duration assumption.

2. Termination Classification

Among records classified as Voluntarily Terminated, the termination-type distribution showed:

Involuntary — 86
Voluntary — 85
Retirement — 76
Resignation — 74

This indicates that employee separation is not represented by a single dominant category and should be analyzed by termination type rather than treating every exit identically.

3. Managerial Engagement

Several supervisors recorded an average engagement score of 5.0/5.0.

The analysis identified high-engagement supervisory groups that can potentially be used as internal benchmarks for lower-performing management teams.

4. Location vs. Satisfaction

The analysis compared employee counts and average satisfaction across locations.

The results showed that employee concentration at a location did not consistently correspond to higher satisfaction. Smaller locations displayed substantially different satisfaction scores.

Implication: Location headcount alone is insufficient to explain employee satisfaction; local management and team-level factors may be more relevant.

5. Training Investment

The largest training investments were:

Training Program	Total Spend	Attendees
Communication Skills	$365,023.24	673
Project Management	$343,313.17	609
Leadership Development	$323,902.03	574
Technical Skills	$323,072.61	579
Customer Service	$320,575.04	565

Insight: Communication Skills represents the largest training expenditure, followed by Project Management.

6. Turnover by Division

The highest turnover rates included:

Division	Total	Active	Turnover
Corp Operations	2	0	100.00%
Billable Consultants	24	5	79.17%
Catv	58	25	56.90%
Project Management - Eng	16	7	56.25%
Finance & Accounting	70	31	55.71%

The 100% Corp Operations rate is based on only two records, so it should not be interpreted as a stable organizational trend.

Billable Consultants, however, show a much larger exposure with 19 non-active records out of 24, making the 79.17% turnover rate more operationally relevant.

7. Correlation Analysis

Correlation analysis was used to test whether employee experience, performance, and training metrics moved together.

The observed coefficients ranged approximately from -0.03 to +0.03, indicating essentially no linear relationship among:

Engagement
Satisfaction
Work-Life Balance
Employee Rating
Training Duration
Training Cost

Key takeaway: Higher training expenditure or duration does not automatically correspond to higher engagement, satisfaction, or employee ratings in this dataset.

8. Gender Representation

Department-level gender distribution showed:

Department	Female	Male
Admin Offices	55.00%	45.00%
Executive Office	4.17%	95.83%
IT/IS	49.07%	50.93%
Production	62.48%	37.52%
Sales	34.74%	65.26%
Software Engineering	42.61%	57.39%

The largest representation gap appears in the Executive Office, while Sales also shows a notable male majority.

💡 Key Findings
01 — Retention Risk

Billable Consultants show a 79.17% turnover rate, with 19 non-active employees out of 24 records.

02 — Training Spend

Communication Skills has the highest training investment at $365K, with 673 attendees.

03 — Training ≠ Engagement

Training cost, duration, engagement, satisfaction, and performance ratings show near-zero linear correlations.

04 — Management Benchmarking

Multiple supervisors achieved an average 5.0/5.0 engagement score, creating an opportunity to identify and replicate effective management practices.

05 — Workforce Representation

The Executive Office is 95.83% male, while Production is 62.48% female, highlighting substantial differences in workforce composition.

06 — Data Quality Matters

Removing 150 duplicate records eliminated approximately 4.76% data inflation before downstream analysis.

🎯 Recommendations
Owner	Recommendation	Why
HR / Talent Management	Prioritize retention analysis for Billable Consultants and other high-turnover divisions.	Billable Consultants show 79.17% turnover.
Business Unit Leaders	Review workload, incentives, career progression, and team structure within high-turnover groups.	High turnover represents potential talent and continuity risk.
L&D Team	Introduce pre/post-training assessments and track performance outcomes by program.	Training spend has near-zero correlation with performance and satisfaction.
People Managers / HRBP	Identify practices used by consistently high-engagement supervisors and share them across lower-engagement teams.	Several supervisors show 5.0/5.0 average engagement.
HR Leadership / DEI Team	Review hiring, promotion, sponsorship, and leadership-development pipelines in departments with large gender imbalances.	Executive Office and Sales show substantial gender representation gaps.
HR Operations	Consolidate and review reporting structures before using supervisor-level metrics for decision-making.	The dataset contains 2,952 unique supervisors across 3,000 records.
⚠️ Limitations & Assumptions

This analysis is intended for portfolio/analytical demonstration and should not be interpreted as a complete causal HR study.

Data limitations
The dataset does not establish causality between employee experience, training, and turnover.
Correlation analysis only identifies linear relationships; it does not prove that variables are independent in every possible way.
The dataset does not provide direct information on compensation amounts, workload, promotion history, manager tenure, or detailed employee feedback.
Location-level conclusions should be treated cautiously because many locations have very small employee counts.
The 100% turnover rate for Corp Operations is based on only two records.
The five age-at-start violations were identified during validation but were not automatically removed or altered; they require business confirmation before production use.
Employment status and exit-date fields contain combinations that require business-rule confirmation before being treated as definitive historical employment events.
Analytical assumptions
A missing ExitDate for an active employee represents an ongoing employment relationship.
IsActive is used as the analytical flag for turnover calculations.
Turnover is calculated at the division level as the proportion of records that are not active.
Training cost represents recorded program expenditure and is not necessarily equivalent to training ROI.
📁 Repository Structure
enterprise-hr-analytics/
│
├── data/
│   ├── Messy_HR_Dataset_Detailed.csv
│   └── Cleaned_HR_Dataset_Production.csv
│
├── notebooks/
│   └── HR_prep.ipynb
│
├── sql/
│   └── HR_Analytics.sql
│
├── screenshots/
│   └── sql-results/
│       ├── table-01.png
│       ├── table-02.png
│       └── ...
│
├── requirements.txt
│
└── README.md
Suggested repository workflow
Raw HR Data
     ↓
Data Cleaning
     ↓
Data Validation
     ↓
Clean Production Dataset
     ↓
SQL Analysis
     ↓
EDA & Business Insights
     ↓
HR Recommendations
     ↓
Future BI Dashboard
⚙️ Tools & Technologies
Python
Pandas
NumPy
Matplotlib
Jupyter Notebook
SQL
Data querying and analytical aggregation
GitHub
Version control and project documentation
Power BI
Planned/future visualization layer
