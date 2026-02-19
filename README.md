# WATTRAC

Data engineering project to track AQI patterns in Indian metro cities and relate them to traffic congestion, weather, and other external factors.

## Bronze foundation (implemented)

The Bronze layer now has three Databricks notebooks:

1. `WATTRAC_BRONZE/01_ingest_traffic_congestion.ipynb`
   - Ingests traffic congestion metrics (speed/travel-time signals) from a traffic API.
   - Intended to run every 30 minutes.
2. `WATTRAC_BRONZE/02_ingest_aqi_realtime_30min.ipynb`
   - Ingests near-real-time AQI snapshots for configured metro cities.
   - Intended to run every 30 minutes.
3. `WATTRAC_BRONZE/03_backfill_aqi_historical.ipynb`
   - Performs one-time/ad-hoc historical AQI backfill for a date range.

Each notebook:
- Uses Databricks widgets for parameterization.
- Adds ingestion metadata (`ingestion_ts`, `ingestion_date`).
- Writes append-only Delta tables into a Bronze schema.

## Suggested Databricks orchestration

Create a Databricks Workflow with:
- **Task A**: `01_ingest_traffic_congestion` (schedule: every 30 min)
- **Task B**: `02_ingest_aqi_realtime_30min` (schedule: every 30 min)
- **Task C**: `03_backfill_aqi_historical` (manual trigger only, not on schedule)

Recommended next step: build Silver transformations to align timestamps/city keys and compute feature tables for downstream AQI impact analysis (traffic peaks, holidays, weather influence).
