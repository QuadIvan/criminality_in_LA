# Key Findings

This section consolidates insights from both the **Power BI dashboard** and the **Python EDA** to provide a clear, actionable summary of crime dynamics in Los Angeles (2023–2024).

---

## Temporal Patterns
- **Monthly trend:** Crime peaked in **October 2023** and declined thereafter, with the steepest drop during **March–May 2024**.
- **Weekly pattern:** **Friday** shows the highest counts, though differences vs. other weekdays are modest.
- **Hourly pattern:** Peak incidence at **12:00 p.m.** (noon). At that time the most frequent offense is **Theft of Identity**, while the overall top offense is **Vehicle — Stolen**.
- **Year-over-year:** Total crimes fell by **45.10%** from 2023 to 2024. - **Vehicle — Stolen**, the most frequent offense, decreased by **12.11%** from 2023 to 2024.
- **Note on reporting:** Apparent increases in vehicle thefts after **5:00 p.m.** may be influenced by **delayed discovery/reporting** (e.g., victims notice theft after work).

---

## Victim Demographics
- **Age:** The distribution is stable; **30–39** remains the most affected group, closely followed by **20–29**.
- **Descent:** Victims are predominantly **Hispanic/Latin/Mexican**, followed by **White**.
- **Data quality caveats:** High “unknown” rates (e.g., victim sex) limit gender-based conclusions. Age and descent patterns should be validated against **population baselines** (census) to assess over/under-representation.

---

## Geography
- **Overall change:** **Newton** posted the largest **percentage decrease** in crime. **Central**, the area with the **highest crime counts** in both years, ranked **near the bottom** in percentage decrease (i.e., one of the smallest declines).
- **Area profiles:** In **Central**, the top offenses are **Burglary from Vehicle**, **Trespassing**, and **Vehicle — Stolen**.
- **Vehicle — Stolen hotspots:** Highest incidence in **Newton**, **77th Street**, and **Southeast** (in that order).  
  – **77th Street**: highest **absolute** vehicle thefts (**3,598** cases).  
  – **Newton**: highest **proportional** share (**18.94%** of its total crimes).  
- **Safest area:** **Foothill** remains the lowest-crime area across both years and sits mid-table in the percentage-decrease ranking.

---

## Crime Types & Severity
- **Violence rate:** **28.48%** of crimes involved a weapon (note: **verbal threats** are counted as a weapon category).
- **Most common weapon (violent crimes):** **Strong-arm** (hands, fists, feet, bodily force).
- **Most common violent offense:** **Battery — Simple Assault**.
- **Offense shifts (YoY):**  
  – **Increases:** **Trespassing** (+**24.85%**); **Vehicle Stolen — Other (Motorized Scooters, Bikes, etc.)** also increased.  
  – **Decreases:** **Vehicle — Stolen** (−**12.11%**) but remains the top offense. **Battery — Simple Assault** fell by **−71.32%**, dropping from **2nd** in 2023 to **11th** in 2024.

---

## Case Status
- **Investigation Continues:** **83%** of cases.  
- **Violence share by status:** **Adult Other** (**71.60%** violent), **Juvenile Arrest** (**65.62%**), **Adult Arrest** (**57.13%**).

---

## Crime Locations
- **Dominant setting:** **Street** is by far the most common location, with nearly double the cases of **Single Family Dwelling**.
- **Actionability:** “Street” is a broad category; further granularity (e.g., street type, commercial vs. residential corridors) would improve operational value.

---

## Limitations
- Only two years (2023–2024): insufficient to establish **long-term seasonality** or structural trends.  
- High “unknown” categories (e.g., victim sex) constrain demographic inference.  
- Demographic findings are **not population-adjusted**; risk of misinterpreting representation without census baselines.  
- Classification/reporting practices may vary over time and across areas.

---

## Suggested Next Steps
- **Normalize by population:** Report **rates per 100k** by area and demographic group.  
- **Benchmark demographics:** Compare victim profiles against **census baselines** to test disproportionality.  
- **Extend time horizon:** Incorporate **≥5 years** to analyze seasonality and trend stability.  
- **Contextualize patterns:** Add covariates (economic indicators, special events, policing operations) to explain deviations.  
- **Refine locations:** Break down “Street” into more granular, actionable categories.  
- **Quality checks:** Reduce “unknown” categories and review reporting lags (e.g., vehicle theft timing).