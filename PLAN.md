# Project Plan: Diabetic Patient Follow-up List

## 1. Goal
Generate a safe, reliable, and easy-to-read list of overdue diabetic patients for the front desk to call. The output must require zero technical setup from the hospital staff.

## 2. Unknowns & Initial Assumptions (To be validated)
- **"Diabetic"**: The data likely doesn't have a simple boolean `is_diabetic` flag. I will need to infer this by looking across `visits.csv` (diagnosis codes), `prescriptions.csv` (diabetes medications), and `lab_results.csv` (HbA1c tests).
- **"Overdue"**: Without strict clinical guidelines provided, I will assume a standard 6-month window since the last visit. If a patient hasn't been seen in 6 months, they are overdue. 
- **Future Visits**: A patient isn't overdue if they already have an appointment scheduled for next week. I need to check `visits.csv` for future dates.

## 3. Step-by-Step Execution
1. **Data Exploration & Sanitization:** 
   - Load all four CSVs. 
   - Check for missing data (e.g., missing phone numbers), duplicate patient IDs, and inconsistent date formats. 
2. **Cohort Identification (Who is diabetic?):**
   - Create a unified set of Patient IDs who meet the criteria across labs, meds, or visit diagnoses.
3. **Timeline Analysis (Who is overdue?):**
   - For the diabetic cohort, find the `MAX(visit_date)`. 
   - Filter out anyone with a visit in the last X months or a visit in the future.
4. **Formatting the Output:**
   - Join the final list with `patients.csv` to get Names and Phone Numbers.
   - Output a clean `overdue_patients.csv` containing only actionable columns: Name, Phone Number, Last Visit Date, and a "Reason Identified" column (so the front desk has context for the call).
5. **Documentation:**
   - Document all data quirks, dropped rows, and clinical assumptions in `DECISIONS.md`.