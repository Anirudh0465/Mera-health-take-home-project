# Decisions & Assumptions

## 1. Defining "Diabetic"
The dataset does not contain a simple boolean flag for diabetes. Relying on just one data source would leave patients out. I defined a patient as "diabetic" if they met **at least one** of the following criteria across three separate files:
* **`visits.csv`**: The doctor's notes contain keywords matching diabetes (e.g., `DM`, `T2DM`, `diabet*`). 
* **`prescriptions.csv`**: The patient was prescribed known diabetic medications (e.g., Metformin, Insulin, Glimepiride, Sitagliptin).
* **`lab_results.csv`**: The patient had a lab result crossing the standard clinical threshold for diabetes (HbA1c >= 6.5, Fasting Glucose >= 126, or Post-Prandial Glucose >= 200). 

## 2. Defining "Overdue"
I defined "overdue" by anchoring to the latest interaction date in the dataset (October 2026) as "today". A diabetic patient is overdue if:
1. They had a `next_appointment` scheduled that is now in the past (Missed Appointment).
2. They did not have a `next_appointment` scheduled, and their last visit was over 180 days (6 months) ago (Standard chronic care window).
*Crucial Safety Check*: Any patient with a future appointment booked anywhere in the system was strictly excluded to prevent the front desk from bothering patients who are already scheduled.

## 3. Data Problems & Resolutions
* **Date Parsing Quirks**: The dates in `lab_results.csv` and `visits.csv` mixed UK (`DD/MM/YYYY`) and US standard formats (e.g., `12/08/2026` meant August 12 in some places and December 8 in others). I forced `dayfirst=True` in Pandas to prevent dates from being dangerously swapped, which would corrupt the "overdue" calculation.
* **Corrupted Phone Numbers**: Missing phone numbers (`NaN`) in `patients.csv` caused Pandas to cast the entire column as scientific notation floats (e.g., `9.000085e+09`). I cast them back to clean integers, then to strings, and replaced missing data with explicit "No Phone on File" text so the front desk isn't confused by `0` or `NaN`.
* **Missing/Unparseable Visit Dates**: When calculating the latest visit for each patient, I encountered rows in `visits.csv` where the `visit_date` was missing or invalid. I chose to drop these specific visit records (dropna) from the timeline calculation so the system wouldn't crash. (If a patient only had invalid visit dates, they would be excluded from the overdue calculation—this is something the data team should look into).

## 4. Limitations (What this cannot be trusted for)
* **Pre-diabetic edge cases**: The lab threshold strictly grabs HbA1c >= 6.5. Pre-diabetics (HbA1c 5.7 - 6.4) are excluded unless they were already put on medication or flagged in the doctor's notes.
* **External Care**: If a patient saw a different endocrinologist outside of this hospital system in the last 6 months, they will falsely appear as "overdue" on our list.