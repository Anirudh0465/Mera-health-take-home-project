# Diabetic Patient Follow-up Generator

This lightweight script parses hospital export data to generate a clean, safe CSV call list for the front desk. 

## Requirements
* Python 3.8+
* Pandas

## Run Instructions
1. Ensure your terminal is in the root directory of this repository.
2. Ensure the hospital's export files are located inside the `exports/` folder (e.g., `exports/patients.csv`, `exports/visits.csv`, etc.).
3. Install the required dependency:
   ```bash
   pip install pandas