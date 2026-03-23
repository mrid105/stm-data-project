# Montreal STM Fare Equity & Ridership Disparity Analysis

A data analytics project examining transit access equity across Montreal's boroughs by combining STM ridership data with Statistics Canada income data. Built as a consulting-style analysis with policy recommendations for the City of Montreal and STM.

---

## Key Findings

- **2.5x fare burden gap** between the highest-burden borough (Hochelaga-Maisonneuve, 3.06% of median income) and lowest (Outremont, 1.23%)
- Montreal's flat monthly pass ($97/month) creates a **regressive pricing structure** — all residents pay the same absolute cost, but lower-income boroughs pay a disproportionate share of their income
- **5 boroughs classified as Underserved**: Hochelaga-Maisonneuve, Côte-des-Neiges, Bordeaux-Cartierville, Rivière-des-Prairies, and LaSalle — all carrying above-median fare burden and below-average daily trip frequency
- k-means clustering (silhouette score: 0.499) confirmed 3 meaningful equity tiers across 12 boroughs

---

## Dashboard Preview

---

## Policy Recommendations

Based on the analysis, three targeted interventions are proposed:

1. **Means-tested OPUS card pricing** — introduce income-based fare tiers for households below the city median income, similar to programs in London (Zip Oyster) and San Francisco (Lifeline Pass)
2. **Frequency increases on underserved lines** — prioritize service improvements on routes serving the 5 underserved boroughs, particularly during peak commute hours
3. **Feeder bus expansion** — extend coverage in bottom-quartile boroughs where average daily trips per stop fall below 700

---

## Methodology

### Fare Burden Index

```
fare_burden (%) = (monthly_pass × 12) / median_household_income × 100
```

Using STM monthly pass price of $97.00 (2026) and Statistics Canada 2021 census median household income by borough.

### Borough Assignment

STM GTFS stops were assigned to boroughs using GPS bounding box mapping (stop_lat, stop_lon). 91.1% of stops (8,106 out of 8,897) were successfully mapped to a borough. Stops falling outside defined boundaries were excluded from the borough-level aggregation.

### Trip Frequency

Average daily trips per stop calculated by counting all trip_id occurrences per stop_id in stop_times.txt, then averaging across all stops within each borough.

### K-Means Clustering

Features: fare_burden_pct and avg_daily_trips, normalized using StandardScaler before clustering. n_clusters=3, random_state=42, n_init=10. Silhouette score of 0.499 indicates meaningful separation between equity tiers.

---

## Data Sources

| Dataset        | Source                                                                             | Description                                                                                                                                                  |
| -------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| STM GTFS       | [stm.info/en/info/developers](https://www.stm.info/en/info/developers)             | Static schedule data: stops, trips, routes, stop_times                                                                                                       |
| STM fares      | [stm.info/en/info/fares](https://www.stm.info/en/info/fares)                       | Monthly pass: $97.00 (2026)                                                                                                                                  |
| Borough income | Statistics Canada, 2021 Census Profile (manually compiled from CSD-level profiles) | Median total income of household (reference year 2020), compiled per borough and hardcoded in the analysis notebook — see `notebooks/analysis.ipynb` Block 4 |

### Data Limitations

- **Income data**: Statistics Canada does not publish 2021 census income figures at Montreal's administrative borough level directly. Figures used in this analysis were manually compiled from the closest corresponding Census Subdivision (CSD) profiles at `www12.statcan.gc.ca/census-recensement/2021`. These should be validated against official borough-level statistics for production use.
- **GTFS frequency**: The GTFS feed represents scheduled trips, not actual ridership. Actual passenger volumes may differ from scheduled frequency.
- **Borough coverage**: 8.9% of stops (791 out of 8,897) could not be assigned to a borough using GPS bounding boxes and were excluded from analysis.

---

## Tools & Libraries

| Tool         | Purpose                                  |
| ------------ | ---------------------------------------- |
| Python 3.13  | Data processing and analysis             |
| pandas       | Data cleaning, merging, aggregation      |
| scikit-learn | StandardScaler, KMeans, silhouette_score |
| matplotlib   | Exploratory charts                       |
| Power BI     | Interactive dashboard                    |
| openpyxl     | Excel export for Power BI import         |

---

## Project Structure

```
stm-fare-equity/
  notebooks/
    analysis.ipynb        <- Full analysis notebook (run top to bottom)
  data/
    gtfs_stm/
      stops.txt           <- STM stop locations
      routes.txt          <- Route definitions
      trips.txt           <- Trip schedules (excluded from git, 10.7MB)
      stop_times.txt      <- Vehicle visits per stop (excluded from git, 253MB)
      agency.txt
      calendar.txt
      calendar_dates.txt
      feed_info.txt
  output/
    fare_burden_chart.png <- Bar chart: fare burden by borough
    scatter_chart.png     <- Scatter: burden vs trip frequency
    final_data.xlsx       <- Exported dataset for Power BI
    Montreal STM Fare Equity Analysis.pdf     <- The analysis report imported from Power BI
  .gitignore
  README.md
```

> **Note**: `stop_times.txt` (253MB) and `trips.txt` (10.7MB) are excluded from this repository due to file size limits. Download the full GTFS feed from [stm.info/en/info/developers](https://www.stm.info/en/info/developers) and place the files in `data/gtfs_stm/` before running the notebook. Borough-level income data is hardcoded in Block 4 of the notebook — see the Data Limitations section above.

---

## How to Run

```bash
# 1. Clone the repository
git clone https://github.com/mrid105/montreal-stm-fare-equity.git
cd montreal-stm-fare-equity

# 2. Install dependencies
pip install pandas matplotlib seaborn scikit-learn jupyter openpyxl

# 3. Download GTFS data
# Go to stm.info/en/info/developers -> Download GTFS
# Place all .txt files in data/gtfs_stm/

# 4. Open the notebook
jupyter notebook notebooks/analysis.ipynb
# or open in VS Code with the Jupyter extension
```

---

## Author

Mridul Mridul — Software Engineering (Co-op), Concordia University  
[linkedin.com/in/mridull](https://linkedin.com/in/mridull) | [github.com/mrid105](https://github.com/mrid105)
