![R](https://img.shields.io/badge/R-276DC3?logo=r&logoColor=white)
![Shiny](https://img.shields.io/badge/Shiny-2A50A1?logo=rstudio&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

# War and Money in Africa  
*Analyzing the Relationship Between Economic Indicators and Conflict Events (1997–2018)*  

![Project Overview](images/overview.png)

---

## 📌 Project Overview
This project explores the relationship between **economic indicators** and **conflict events** across African regions between **1997 and 2018**. Using Kaggle datasets, I developed an **interactive Shiny dashboard** to visualize how GDP growth, inflation, and regional conflict patterns overlap.  

The motivation for this research is to understand how economic stability—or lack thereof—relates to the frequency and severity of conflict across African nations.  

---

## 📊 Data Sources
- **Conflicts Dataset:** [Political Conflicts in Africa (1997–2018)](https://www.kaggle.com/datasets/robertoberwa/conflicts-in-africa-from-19972018) by Roberto Berwa  
- **Economic Dataset:** [African Economy (1980–2022)](https://www.kaggle.com/datasets/mahmoudsaeed99/african-economy-from-1980-to-2022) by Mahmoud Saeed  

Both datasets were cleaned and harmonized (standardized country names, handled missing values, assigned regions, aggregated by **decade**).

---

## ⚙️ Methodology
- **Data Cleaning:**  
  - Replaced empty strings with NA  
  - Removed duplicates and missing values  
  - Standardized country names and grouped into regions  
- **Aggregation:**  
  - Summarized conflicts by **region & decade**  
  - Calculated average GDP growth and inflation by **region & decade**  
- **Visualization:**  
  - Plots created using `ggplot2`  
  - Interactive maps built with `leaflet`  
  - Deployed as a multi-tab Shiny app  

---

## 📈 Key Visualizations

### Total Conflict Fatalities
![Fatalities](images/fatalities.png)  
Fatalities rose sharply in **East Africa** post-2000, while **West Africa** and **Central Africa** also saw significant increases by the 2010s.

---

### Total Conflict Events
![Events](images/events.png)  
East and West Africa experienced the largest growth in recorded events, with **East Africa peaking after 2010**.

---

### GDP Growth Trends
![GDP Growth](images/gdp_growth.png)  
Regions with relatively strong economic performance (e.g., **East Africa**) still experienced high conflict activity, highlighting a **paradox between growth and stability**.

---

### Inflation Trends
![Inflation](images/inflation.png)  
Economic volatility is evident in **East and West Africa**, where inflation spikes often aligned with higher conflict intensity.

---

### Correlations
- **GDP Growth vs Conflict Fatalities**  
  ![GDP vs Conflicts](images/gdp_conflict.png)  
  East Africa shows a **positive correlation** (growth coincides with conflict), while West Africa trends negative.

- **Inflation vs Conflict Events**  
  ![Inflation vs Conflicts](images/inflation_conflict.png)  
  Higher inflation rates correlate with more conflict events in several regions.

---

### Geospatial Mapping
![Map](images/map.png)  
An interactive **Leaflet map** visualizing conflict locations by region and severity.  

---

## 🔍 Key Insights
- **East Africa**: Strong GDP growth but highest conflict fatalities → economic growth does not guarantee peace.  
- **West Africa**: Inflation and conflict tightly linked → economic instability as a conflict driver.  
- **North Africa**: Moderate inflation, but spikes coincide with conflict surges (e.g., Arab Spring).  
- **South Africa**: Relatively stable, but still affected by regional shocks.  

---

## 🚀 Running the Shiny App
Clone the repo and run:

```r
# Install required packages
install.packages(c("shiny", "ggplot2", "leaflet", "dplyr", "readxl", "stringr"))

# Run the app
shiny::runApp("R/app.R")
