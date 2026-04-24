# HW3 (due 26.04)

**ETL Processes, Superset, and Data Visualization**

**Objective:** Start the Lecture 4 environment, run the ETL workflow, connect Superset to the warehouse, and create visualizations based on the course datasets.

---

## Task 1: Lecture 4 Environment and Superset Startup Review

Start the Lecture 4 environment from the repository root inside the devcontainer:

```bash
make init
make up-superset
make devcontainer-join-course-network
make ps
```

In your own words, explain what each command does and why it is needed.

**Your explanation should cover:**
- What each command does.
- Which services are expected to exist for Lecture 4 (`postgres`, `superset`, `superset-redis`, and the one-time `superset-init`).
- Why `make devcontainer-join-course-network` is needed before running ETL from the devcontainer.
- How `make` and Docker Compose work together in this repository.

**Also explain how you would start working again in each of these cases:**
- The Superset stack already exists but is stopped.
- The containers have been removed.
- You changed `.env` values or reopened the repository from a different host folder.

---

## Task 2: Data Analysis and Visualization in Superset

Make sure the current Lecture 4 datasets are available in your Superset instance.

Run the ETL and validation flow from the repository root inside the devcontainer:

```bash
make etl-bootstrap
.venv/bin/python -m etl.airviro.cli run --from 2026-01-01 --to 2026-03-26 --source-key air_quality_station_8 --source-key pollen_station_25 --verbose
make warehouse-status
```

> **Note:** For this homework, keep the date range inside 2026. The year-to-lecture-04 example above was validated during course preparation. Do not use wide historical backfills here, because older API responses may contain timestamp inconsistencies.

Then connect Superset to the warehouse and use the current serving views:
- `l4_mart.v_air_quality_hourly_station_8`
- `l4_mart.v_pollen_daily_station_25`
- `l4_mart.v_airviro_measurements_long` (optional helper dataset)

**Warehouse connection values:**
- **host:** `postgres`
- **port:** `5432`
- **database:** `warehouse`
- **username:** `warehouse`
- **password:** `warehouse`

**Create a chart in Superset that shows dust particle concentration (PM10) by:**
- **rows:** weekday
- **columns:** hour of day (0-23)
- **metric:** average pm10

**Recommended dataset:**
- `l4_mart.v_air_quality_hourly_station_8`

**Recommended fields:**
- **weekday:** `day_short`
- **hour:** `hour_number`
- **metric:** `AVG(pm10)`

> **Note:** `day_short` is intentionally space-padded in the datetime dimension so weekday labels sort chronologically in many Superset charts.

---

## Task 3: Dashboard Creation in Superset

Create a new dashboard in Superset that includes:
- The chart you created in Task 2.
- Two more charts of your choice.
- At least one chart based on `l4_mart.v_pollen_daily_station_25`.
- At least one chart based on air-quality indicators from `l4_mart.v_air_quality_hourly_station_8` such as `ws10`, `wd10`, `pm2_5`, `pm10`, `o3`, `no2`, or `so2`.
- A Markdown element containing a title, a brief explanation of the dashboard, and your name.

> **Note:** Do not rely on `temp` for this homework. During the lecture period, the source API returned unreliable temperature values for station 8. The homework still stays on station 8, because the current Lecture 4 simple ETL and serving views are intentionally built around that station. If you or your agent buddy feels confident, you can choose another air quality station (view ohuseire.ee).

Try to keep the visuals different from each other, for example a pivot table, line chart, and box plot or bar chart.

---

## Submission

**Submit:**
1. One Markdown document for Task 1.
2. One screenshot of the Task 2 chart.
3. One screenshot or exported image of the final dashboard from Task 3.