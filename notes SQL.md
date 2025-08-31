

# 📝 Project Log: Criminality in LA (2023 vs 2024)

This document tracks the thought process, progress, issues, and decisions taken throughout the project. It helps keep a record of the full workflow — from raw data to final insights and visualizations.

---

## 📅 Date: 31/07/2025

### ✅ What I did today:
- Downloaded the dataset from https://data.lacity.org/Public-Safety/Crime-Data-from-2020-to-Present/2nrs-mtv8/about_data
- Inspected column names and data types
- Created PostgreSQL database and imported CSV
- Cleaned the datetime columns 
- Extracted the relevant data for 2023 and 2024

### 💭 Observations / Notes:
- The column `date_rptd` is in the following format: MM/DD/YYYY. I should change it to the standard format YYYY/MM/DD
- There is one column called part_1_2 which seems useless

## 🧪 Data Loading Challenges

- While attempting to import the dataset into PostgreSQL, an error occurred related to the `date_rptd` column:
ERROR: invalid input syntax for type timestamp: «31»
CONTEXT: COPY crimes, line 2, column date_rptd: «31»


This error suggested that the CSV had inconsistently formatted date fields, possibly due to corrupted or malformed values.

### ✅ Resolution Strategy

To maintain a SQL-first workflow and demonstrate strong problem-solving in SQL alone:

1. Created the table `crimes_raw` with `TEXT` data type for the `date_rptd` and `date_occ` columns.
2. Deferred casting to `TIMESTAMP` until after import.
3. Created a cleaned version of the table using `TO_TIMESTAMP` and regular expressions to filter properly formatted rows.

This approach ensures full control over error handling and data quality directly within the database.


### Data Cleaning Decisions
✅ Objective:
Prepare the dataset by selecting only relevant, interpretable, and clean columns for analysis. Remove columns with missing documentation or unclear meaning.

### 🗑️ Dropped Columns and Justification

| Column Name            | Reason for Removal |
|------------------------|--------------------|
| `mocodes`              | Contains numerical codes for Modus Operandi without any accompanying legend or documentation. The provided link to the PDF containing the code descriptions is broken and returns a "not found" error. Therefore, the values in this column cannot be interpreted meaningfully. |
| `rpt_dist_no`          | A 4-digit code representing sub-areas within LAPD zones. The metadata link requires login credentials and is not publicly accessible, making this field unusable for analysis. |
| `part_1_2`             | Provides no meaningful insight and lacks clear documentation. |
| `date_rptd`, `date_occ` | Removed to avoid redundancy after creating cleaned timestamp versions (`date_rptd_ts` and `date_occ_ts`). |




---

## 🔍 SQL Work
-Query to create the 'crimes_raw' table, according to the dataset data types and considering the two problematic columns:

CREATE TABLE crimes_raw (
    dr_no TEXT,
    date_rptd TEXT,
    date_occ TEXT,
    time_occ TEXT,
    area INTEGER,
    area_name TEXT,
    rpt_dist_no TEXT,
    part_1_2 INTEGER,
    crm_cd TEXT,
    crm_cd_desc TEXT,
    mocodes TEXT,
    vict_age TEXT,
    vict_sex TEXT,
    vict_descent TEXT,
    premis_cd INTEGER,
    premis_desc TEXT,
    weapon_used_cd TEXT,
    weapon_desc TEXT,
    status TEXT,
    status_desc TEXT,
    crm_cd_1 TEXT,
    crm_cd_2 TEXT,
    crm_cd_3 TEXT,
    crm_cd_4 TEXT,
    location TEXT,
    cross_street TEXT,
    lat NUMERIC,
    lon NUMERIC
);


- Query to take a look into the first rows of the data:

SELECT *
FROM crimes_raw
LIMIT 10;


- Query to create a cleaned table with cleaned columns date_rptd_ts and date_occ_ts, using regular expressions to ensure correct data parsing

CREATE TABLE crimes_clean AS
SELECT *,
    TO_TIMESTAMP(date_rptd, 'MM/DD/YYYY HH12:MI:SS AM') AS date_rptd_ts,
    TO_TIMESTAMP(date_occ, 'MM/DD/YYYY HH12:MI:SS AM') AS date_occ_ts
FROM crimes_raw
WHERE date_rptd ~ '^\d{2}/\d{2}/\d{4} \d{2}:\d{2}:\d{2} [AP]M$'
  AND date_occ ~ '^\d{2}/\d{2}/\d{4} \d{2}:\d{2}:\d{2} [AP]M$';


- Queries to compare both datasets rows
SELECT COUNT(*) AS total_rows
FROM crimes_raw
total_rows = 1004991

SELECT COUNT(*) AS total_rows
FROM crimes_clean
total_rows = 1004991

There is no difference between the clean and raw datasets in number of rows, so there is not data lost. 


- Query to extract data for 2023 and 2024:
SELECT dr_no AS file_id,
	   date_rptd_ts AS date_reported,
	   date_occ_ts AS date_occurred,
	   time_occ AS time_occurred,
	   area AS area_code,
	   area_name,
	   crm_cd AS crime_committed_code,
	   crm_cd_desc AS crime_committed,
	   vict_age AS victim_age,
	   vict_sex AS victim_sex,
	   vict_descent AS victim_descent,
	   weapon_used_cd AS weapon_used_code,
	   weapon_desc,
	   status AS case_status,
	   status_desc,
	   crm_cd_1 AS crime_code_1,
	   crm_cd_2 AS crime_code_2,
	   crm_cd_3 AS crime_code_3,
	   crm_cd_4 AS crime_code_4,
	   location AS crime_location,
	   premis_cd AS location_type_code,
	   premis_desc AS location_type_desc,
	   cross_street,
	   lat AS latitude,
	   lon AS longitude
FROM crimes_clean
WHERE EXTRACT(YEAR FROM date_occ_ts) IN (2023, 2024)

## Objective Achieved 