# Power BI Project – Objective and Purpose

## Objective
The primary objective of this stage is to **transition from Python-based exploratory data analysis (EDA) to interactive data visualization** using **Power BI**.  
While Python allowed for detailed statistical exploration and data cleaning, Power BI will enable the creation of a **dynamic and user-friendly dashboard** to communicate insights more effectively.

## Purpose of Using Power BI
- **Interactive Visualization:** Power BI allows stakeholders to interact with the data, filter by categories such as year, crime type, or area, and drill down into specific dimensions.  
- **Dashboard Creation:** Unlike static charts in Python, Power BI dashboards provide a central hub for monitoring multiple metrics simultaneously.  
- **Comparative Analysis:** Enables quick year-over-year and area-based comparisons, offering a deeper understanding of temporal and spatial crime trends.  
- **Storytelling with Data:** The visualizations will make complex crime patterns more accessible and actionable, supporting better decision-making.  

## Next Steps
The following sections will cover:
1. **Data Preparation for Power BI** – exporting and cleaning datasets ready for import.  
2. **Building the Data Model** – ensuring relationships between tables (if multiple datasets are used).  
3. **Visual Exploration** – starting with key variables (year, area, crime type, victim demographics).  
4. **Dashboard Development** – assembling visuals into a cohesive and interactive dashboard for presentation.  


## Power BI – Initial Data Preparation

- **Data Import:**  
  Imported the cleaned dataset (`LA_crimes_cleaned.csv`) previously exported from Python into Power BI.  

- **Power Query Review:**  
  Opened **Power Query** to check data quality and perform the first round of transformations.  

- **Column Removal:**  
  - Removed the column **`crime_code_4`** since it was completely empty (likely a byproduct of rows eliminated during Python cleaning).  
  - Removed the column **`cross_street`** as it provided little analytical value and its removal reduces the overall data model size.  

- **Data Cleaning:**  
  Applied the **Trim** function to all text columns to ensure no leading or trailing spaces remained in the dataset.  

---

## Cleaning Extra Spaces in `crime_location`

- **Problem:**  
  The column `crime_location` contained addresses with inconsistent spacing, e.g.  
  `1600  N poisseta              PL`  
  Standard Power Query functions such as **Trim** only remove spaces at the beginning and end of text strings, while **Clean** removes non-printable characters. Neither function solves the problem of multiple internal spaces.

- **Solution:**  
  A **custom formula** was applied in Power Query to normalize spaces within the text:

  ```m
  Text.Combine(List.Select(Text.SplitAny([crime_location], " "), each _ <> ""), " ")
Text.SplitAny → splits the string by spaces.

List.Select → removes empty elements (caused by multiple spaces).

Text.Combine → rebuilds the string using a single space between words.

Result:

Before: 1600 N poisseta PL

After: 1600 N poisseta PL

Action Taken:

Created a new column crime_location_clean with the normalized text.

Removed the original column crime_location and renamed the cleaned version for consistency.

This ensures all addresses are consistently formatted and avoids issues in future grouping, filtering, or geocoding tasks.

## Dimension Tables Creation

- **Created Dimension Tables:**
  - `DimArea`
  - `DimCrimeCommitted`
  - `DimLocation`
  - `DimCaseStatus`
  - `DimVictimSex`
  - `DimWeaponUsed`
  - `DimVictimDescent`
  - `Dim Age Group`

- **Keys:**  
  Each dimension was created with its corresponding key column to establish relationships with the fact table.

  - **Relationships:**
  - Each dimension table connects to the fact table using its corresponding key column.  
  - The schema follows a 1-to-many structure, with the fact table at the center.  


- **Fact Table Adjustments (`Fact LA Crimes`):**  
  - Removed descriptive columns (e.g., crime descriptions, location descriptions, etc.) from the fact table.  
  - Only the code keys remain in the fact table, ensuring that descriptive attributes are stored in their respective dimension tables.

- **Objective:**  
  These transformations were made to design a **star schema** and optimize the data model for better performance, clarity, and scalability within Power BI.


### Justification for Creating `DimAgeGroup`

- **Clarity & Consistency:**  
  Age groups provide a categorical abstraction of continuous age data, making analysis and visualization more interpretable.

- **Reusability:**  
  A dedicated dimension table allows age groups to be reused across multiple reports, filters, and dashboards without redefining bins.

- **Flexibility:**  
  Enables easier adjustment of group definitions (e.g., by decade, 5-year intervals, or custom ranges) without altering the fact table.

- **Star Schema Best Practice:**  
  Keeping age groups as a dimension improves the data model structure, ensuring the fact table remains focused on events while dimensions hold descriptive attributes.

## Creation of `DimDate`

- **Method Used:**  
  Created the `DimDate` table using the **DAX function `CALENDAR()`**, defining the date range from the **earliest** to the **latest** value found in the column `date_reported` of the fact table.  

- **Reason for Column Selection:**  
  Although the dataset only includes crimes committed in **2023 and 2024**, the `date_reported` field was chosen because:
  - Crimes are often reported **after the date of occurrence**.  
  - Some crimes committed in late 2023 or 2024 were officially **reported in 2025**.  
  - Using `date_reported` ensures that the calendar covers the **entire reporting timeline**, avoiding mismatches with fact records.  

- **Objective:**  
  This approach guarantees that the **time dimension** fully captures all reported crime events, even when the reporting date falls outside the year of occurrence.

###  DimDate Table (Power BI)

- **Created Table:** `DimDate`  
- **Configured As:** Marked as Date Table  

**Columns Created with DAX:**
- `Date` → base column  
- `Year`  
- `Month Number`  
- `Day`  
- `Quarter`  
- `Day of Week` (numeric)  
- `Day of Week Name` (e.g., Monday, Tuesday)  
- `Day of Week Abbr` (e.g., Mon, Tue)  
- `Month` (full name)  
- `Month Abbr` (e.g., Jan, Feb)  
- `Is Weekend` (boolean flag)  
- `Week of Year`  
- `Year-Month` (e.g., 2023-01)  

### DimDate & Temporal Relationships
- The model keeps only one date dimension (`DimDate`).
- If a second relationship with another date field (e.g., `date_reported`) is required, the DAX function `USERELATIONSHIP()` will be used in measures.
- This approach keeps the model lightweight and avoids duplicate date tables.

---

### Semantic Model Verification
- Power BI automatically detected most relationships between the fact table and the dimension tables.
- Manual relationships created:
  - `DimAgeGroup` ↔ `Fact_LA_Crimes`
  - `DimDate[Date]` ↔ `Fact_LA_Crimes[date_occurred]`
- In the future, `USERELATIONSHIP()` may be used to temporarily activate the relationship with `date_reported`.
- The star schema is correct, with one-to-many relationships and proper filter directions.

---

### Data Category Configuration
- In the fact table, the `longitude` and `latitude` columns were assigned to the correct data categories in Table View.

# Dashboard Design

## Overview Page – KPI Cards

- **Total Crimes**  
  A card showing the overall count of crimes across 2023 and 2024, using the measure:  
  ```DAX
  Total Crimes = COUNTROWS ( 'Fact LA Crimes' )

- **Crimes in 2023 and 2024**
A multi-row card with two separate measures, each filtering the fact table by year:

DAX
Crimes in 2023 = CALCULATE ( COUNTROWS ( 'Fact LA Crimes' ), DimDate[Year] = 2023 )
Crimes in 2024 = CALCULATE ( COUNTROWS ( 'Fact LA Crimes' ), DimDate[Year] = 2024 )

- **2023 to 2024 Change (YoY)**
A card showing the relative year-over-year change in crime counts:

DAX
2023 to 2024 Change =
( [Crimes in 2024] - [Crimes in 2023], [Crimes in 2023] )
This measure returns a negative percentage when crimes decrease.

- **Most Committed Crime**
A multi-row card filtered by Top N = 1 using [Crimes Count].
This shows the crime description with the highest number of occurrences and its count.

## Victim Demographic Page

- **Year Slicer**: Allows filtering the page by year.  
- **Total Crimes Card**: Displays the total number of crimes.  
- **Stacked Column Chart**: Shows the total number of crimes by victim age group, divided by sex (male/female as legend). The chart excludes victims with age equal to 0, as explained previously.  
- **Treemap**: Displays the distribution of victim descent, highlighting the most frequent ethnic groups among victims.  

## Geography Page

 ### Choropleth Map (Shape Map)

- **Objective:** Show crime distribution across Los Angeles areas using a choropleth map (colored polygons instead of individual points).  

- **Dataset:** LAPD Divisions boundaries obtained from **Kaggle** (GeoJSON format)  https://www.kaggle.com/datasets/chaitanyakck/lapd-divisions?resource=download

- **Steps:**
  1. Enabled the **Shape Map visual** in Power BI  
     (*File → Options → Preview features → Shape Map Visual*, then restart Power BI).  
  2. Inserted a **Shape Map** visual.  
  3. Loaded the LAPD Divisions **GeoJSON file** as the custom shape.  


- **Result:** Each LAPD area is displayed as a polygon, colored by the number of crimes.  
  This provides a clear territorial overview of crime incidence without overlapping points or bubbles.

### Visuals
- **Stacked Column Chart**: Total crimes by LAPD divisions (e.g., Central, Pacific, Southwest, etc.).
- **Table**: Area-level YoY change in crimes, showing % decrease for each division.
- **Key Insight**: All divisions show decline, ranging from -36.65% to -54.86%.
- **Choropleth Map (Shape Map)**: Shows every LA division.

 **Tooltip Page (Geo tt)**:
  - A tooltip page named **"Geo tt"** was created and enabled in the **Geography** page.
  - When hovering over the bar chart of LA areas, the tooltip displays the **Top 5 location types** where crimes were committed in the selected area.

## Temporal Page
- **Line Chart**: Crimes by year and month (2023 vs 2024).
- **Column Chart**: Crimes by day of the week.
- **Column Chart**: Crimes by hour of day (0–23).
- **Heatmap**: Crimes by day of the week × hour.
- **Observation**: Allows analysis of seasonal, daily, and hourly crime patterns.

## Crimes Page
- **Table**: Top crimes with metrics:
  - Total count, % of total, breakdown by year, YoY change, violent crime indicator.
  - Example: Vehicle Stolen (45,979), Battery – Simple Assault (24,231), Burglary from Vehicle (23,090).
- **KPI**: 28.48% of crimes classified as violent.
- **Charts**:
  - Weapon used distribution.
  - Case status breakdown (e.g., investigation continuing, adult arrest, juvenile arrest, unknown).

- **Drillthrough Setup**:
  - The *Crimes* page was enabled as a drillthrough page.
  - A drillthrough button was created and added to the other report pages.
    - When disabled, the button displays the text **"Make a Selection"**.
    - When enabled, the button displays the text **"View Details"**.

  - **Back Button**:
  - A *Back* button was added to the *Crimes* page to allow users to easily return to the originating page after performing a drillthrough.

- **Independent Page Behavior**:
  - Since the *Crimes* page also works as a standalone page, an image was added (sourced from the internet) with a bookmark action.
  - This bookmark resets all filters, allowing the user to view an unfiltered breakdown of all crimes without geographic, demographic, or temporal restrictions.

- **Tooltip Page (Areas tt)**:
  - A tooltip page named **"Areas tt"** was created and enabled for the *Crimes* page.
  - When hovering over a crime, the tooltip displays a table with:
    - The **Top 5 areas** where that crime is most frequently committed (absolute values).
    - A column showing the **percentage of that crime relative to all crimes in the same area**.


- **Page Navigation**:
  - A page navigator was added to each report page to allow users to easily switch between pages.

  ## DAX Measures and Calculated Columns

| Name | Type | Description | Formula |
|------|------|-------------|---------|
| Total Crimes | Measure | Counts the total number of crimes. Used as a base measure for cards and other calculations. | `COUNTROWS('Fact LA Crimes')` |
| Crimes in 2023 | Measure | Counts the number of crimes committed in 2023. Used in a KPI card. | `CALCULATE(COUNTROWS('Fact LA Crimes'), DimDate[year] = 2023)` |
| Crimes in 2024 | Measure | Counts the number of crimes committed in 2024. Used in a KPI card. | `CALCULATE(COUNTROWS('Fact LA Crimes'), DimDate[year] = 2024)` |
| Same Period Last Year | Measure | Calculates the number of crimes committed in the same period of the previous year. Useful for comparing 2024 against the same period in 2023. | `CALCULATE([Total Crimes], SAMEPERIODLASTYEAR(DimDate[Date]))` |
| 2023 to 2024 Change | Measure | Calculates the year-over-year percentage change in crimes between 2023 and 2024. | `([Crimes in 2024] - [Crimes in 2023]) / [Crimes in 2023]` |
| % of Area Crimes | Measure | Calculates the percentage of crimes committed in a location type relative to all crimes, ignoring the Top N filter. Used in a tooltip table to show the top 5 location types by percentage. | `DIVIDE([Total Crimes], CALCULATE([Total Crimes], REMOVEFILTERS('DimLocation'[location_type_desc])))` |
| % of Selected Crime within Area | Measure | Calculates the percentage of a specific crime within an area, i.e., what proportion of crimes in the area correspond to the selected crime. | `DIVIDE([Total Crimes], CALCULATE([Total Crimes], REMOVEFILTERS(DimCrimeCommitted)))` |
| Was Violent | Calculated Column | Classifies crimes as violent or non-violent based on the weapon code. Crimes with `weapon_used_code = 0` are non-violent (no weapon used). | `IF('Fact LA Crimes'[weapon_used_code] = 0, FALSE(), TRUE())` |
| Was Violent Count | Calculated Column | Converts the Boolean value of *Was Violent* into 0 (non-violent) or 1 (violent) for aggregation. | `IF('Fact LA Crimes'[Was Violent] = FALSE(), 0, 1)` |
| Violent Crimes | Measure | Counts the number of violent crimes by summing the *Was Violent Count* column. | `SUM('Fact LA Crimes'[Was Violent Count])` |
| % of Violent Crimes | Measure | Percentage of violent crimes relative to total crimes (overall or filtered by crime type). | `[Violent Crimes] / [Total Crimes]` |
| Name | Type | Description | Formula |
|------|------|-------------|---------|
| Crime Peak Hour | Measure | Identifies the hour of the day when the most crimes occurred. | See formula below |
| Tooltip Title Crime | Measure | Creates a dynamic title for the tooltip in the *Crimes* page. The title changes depending on the selected crime. | See formula below |

### Crime Peak Hour (DAX)

DAX
VAR crimes_by_hour =
    SUMMARIZE(
        'Fact LA Crimes',
        'Fact LA Crimes'[hour_occurred],
        "Crimes", COUNTROWS('Fact LA Crimes')
    )
VAR top1 = TOPN(1, crimes_by_hour, "Crimes", DESC)
RETURN
MAXX(top1, 'Fact LA Crimes'[hour_occurred])

### Tooltip Title Crime (DAX)
VAR crimename =
    COALESCE(
        SELECTEDVALUE(DimCrimeCommitted[crime_committed]),
        "All Crimes"
    )
RETURN
"Top 5 areas for " & crimename & " and % of total crimes"

## Key Findings

### Temporal Patterns
- **Monthly trend:** October 2023 was the peak month for total crimes. From that point, crime volume declined, with the sharpest drop between **March–May 2024**.
- **Weekly pattern:** **Friday** recorded the highest number of crimes, though differences vs. other weekdays were not substantial.
- **Hourly pattern:** The peak hour was **12:00 p.m.** (noon). At that time, the most frequent offense was **Theft of Identity**, whereas the overall top crime across the dataset was **Vehicle — Stolen**.
- **Caveat:** Despite a clear reduction from 2023 to 2024, more data (additional years) is needed to make strong temporal inferences.

### Victim Demographics
- **Age:** No material shifts in age distribution; **30–39** remained the most affected group, closely followed by **20–29**.
- **Descent:** Victims were predominantly **Hispanic/Latin/Mexican**, followed by **White**.
- **Caveat:** Both age and descent patterns should be validated against **LA population baselines** (e.g., census data) to assess over/under-representation.

### Geography
- **Area-level change:** **Newton** showed the largest percentage decrease in crimes. **Central**—the area with the highest crime count in both years—ranked **near the bottom** for percentage decrease (i.e., one of the smallest declines).
- **Top offenses by area:** In **Central**, the three most frequent crimes were **Burglary from Vehicle**, **Trespassing**, and **Vehicle — Stolen**.
- **Vehicle — Stolen hotspots:** The three areas with the highest incidence were **Newton**, **77th Street**, and **Southeast** (in that order). Among them, **77th Street** experienced the largest percentage decline, followed by **Newton** and then **Southeast**.
- **Safest area:** **Foothill** remained the lowest-crime area across both years and sits mid-table in percentage decrease rankings.

### Crime Types & Severity
- **Violence rate:** **28.48%** of crimes involved a weapon (note: **verbal threats** are counted as a weapon category).
- **Most common weapon in violent crimes:** **Strong-arm** (hands, fists, feet, bodily force).
- **Most common violent offense:** **Battery — Simple Assault**.

### Case Status
- **Investigation Continues:** **83%** of cases are in this status.
- **Violence share by status:** **Adult Other** (71.60% violent), **Juvenile Arrest** (65.62% violent), **Adult Arrest** (57.13% violent).

### Notable Increases/Decreases by Offense
- **Increases:** **Trespassing** rose by **24.85%**; **Vehicle Stolen — Other (Motorized Scooters, Bikes, etc.)** also increased.
- **Decreases:** **Vehicle — Stolen** (the most frequent offense) fell by **12.11%**, remaining the top crime in both years.  
  **Battery — Simple Assault** declined by **71.32%**; it dropped from the **second** most common offense in 2023 to **11th** in 2024.

### Additional Notes & Limitations
- Age **0** records were excluded from age-based visuals, as documented in the dashboard.
- Year-over-year comparisons can be influenced by reporting practices, enforcement intensity, and classification changes.

### Suggested Next Steps
- Normalize comparisons using **population-adjusted rates** (per 100k residents) by area and demographic group.
- Compare victim demographics with **census baselines** to test for disproportionality.
- Extend the time horizon (≥5 years) to test **seasonality** and stability of trends.
- Consider **contextual covariates** (e.g., economic indicators, special events, policing operations) to explain deviations.
