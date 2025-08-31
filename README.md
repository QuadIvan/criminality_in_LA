# Criminality in LA: A Comparison Between 2023 and 2024

This project analyzes crime trends in Los Angeles using data from the official open data portal of the City of LA. With over one million records available, the study focuses on comparing criminal activity between 2023 and 2024. It identifies the most dangerous areas, crime types with the highest increase or decrease, and patterns based on geography, time of day, and day of the week.

Using **SQL**, **Python**, and **Power BI**, the goal is to extract actionable insights that could support public safety strategies — while demonstrating a complete data analysis workflow from raw data to interactive visual storytelling.

## Project Overview
- **Goal:** Compare 2023 vs 2024 criminal activity and surface actionable insights (hotspots, crime type shifts, temporal patterns).
- **Scope:** City of Los Angeles, reported crimes (open data portal), ~360k records for 2023–2024.
- **Outputs:** Jupyter EDA, SQL scripts, and a Power BI dashboard with drillthrough, tooltips, and a star schema.


## Analytical Questions
- Has criminal activity increased in Los Angeles from 2023 to 2024?
- Which types of crimes have become more or less frequent?
- What are the most dangerous areas based on reported incidents?
- Are there specific days of the week or hours with higher crime rates?
- How do crime patterns vary across police reporting districts?

##  Tech Stack
- **SQL (PostgreSQL):** Filtering, aggregation, and data exploration on large datasets. Full documentation in: ['SQL notes'](docs/notes_SQL.md)
  
- **Python (pandas, matplotlib, seaborn):** Data cleaning, feature engineering, and EDA. Full analysis in: [Python EDA](LA_crime_analysis_python.ipynb)
  
- **Power BI:** Interactive dashboards, geospatial and temporal visualizations. Full documentation in: [Power BI analysis](docs/notes Power BI.md)
- Power BI charts as a PDF in: [PBI LA Crimes](```docs/LA crime PBI pdf.pdf```)
- Power BI complete interactive file in: [Power BI LA Crimes Analysis](docs/LA crime PBI.pbix) **Preview unavailable, only to be downloaded**


## Key Insights
A concise selection is listed here; full notes in [`docs/KEY_FINDINGS.md`](docs/KEY_FINDINGS.md).
- Crimes decreased **~45%** from 2023 to 2024.
- **Vehicle — Stolen** remained the top offense; **Battery — Simple Assault** dropped sharply YoY.
- Peak hour: **12:00 p.m.**; peak month: **October 2023**.
- **30–39** is the most affected age group; victims are predominantly **Hispanic/Latin/Mexican**.
  
