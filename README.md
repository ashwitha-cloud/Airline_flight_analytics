# Airline Flight Operations Analytics

A Databricks Medallion pipeline (Bronze, Silver, Gold) built with Lakeflow Spark Declarative Pipelines in SQL, plus a 3-page AI/BI dashboard. It turns a raw airline flights CSV (1,365 rows) into trusted summary tables and proves that no row was lost.

**Tools:** Databricks, Unity Catalog, Lakeflow Spark Declarative Pipelines (SQL), Databricks AI/BI

## Dashboard

### Overview
![Overview](screenshots/dashboard_overview.png)

### Airlines, Routes and Delays
![Airlines, Routes and Delays](screenshots/dashboard_airlines_routes_delays.png)

### Data Quality
![Data Quality](screenshots/dashboard_data_quality.png)

## Pipeline

![Pipeline graph](screenshots/pipeline_graph.png)

Bronze keeps the raw data, Silver cleans it and quarantines bad rows, and Gold builds the summary tables.

## Data quality and reconciliation

![Data quality result](screenshots/data_quality_result.png)

1,365 Bronze rows = 1,321 clean + 19 rejected + 25 duplicates removed.

## Gold table example

![Airline summary](screenshots/gold_airline_summary.png)

## Cleaning rules

| Problem | Rule | Why |
|---|---|---|
| 25 duplicate flights | SELECT DISTINCT | flight_id must be unique |
| Extra spaces, mixed case | INITCAP(TRIM(col)) | Group names consistently |
| Blank airline and delay reason | COALESCE to 'Unknown' | Keep the flight, show the gap |
| Blank delay minutes | TIMESTAMPDIFF of the two departure times | Value can be calculated |
| Blank ticket revenue | passengers x avg_fare | Formula defines revenue |
| Passengers above seats, negative fare | Moved to the rejected table with a reason | Impossible values |
| Negative delay | Kept, counted as On Time | The flight left early |

## How to run

1. Create catalog `flights`, schema `ops` and volume `raw_files`.
2. Upload `airline_flights_raw.csv` to `/Volumes/flights/ops/raw_files/`.
3. Create an ETL pipeline (default catalog `flights`, schema `ops`) and add `airline_flights_pipeline.sql`.
4. Click Run pipeline, then refresh the dashboard.

## Files

- `airline_flights_pipeline.sql`: full pipeline code
- `airline_flights_raw.csv`: synthetic source data
- `screenshots/`: pipeline graph, results and dashboard pages
