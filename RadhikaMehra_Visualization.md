# Hive Visualization Preparation Pipeline

This document describes the complete Hive-based workflow used to explore,
prepare, and aggregate wildlife observation data for visualization.

---

## Step 0: Exploratory Species Analysis in Hive

This step identifies suitable species for analysis based on observation
volume and temporal coverage using pre-existing Hive tables.

---

### 0.1 Set the Active Hive Database

All analysis was performed using tables from the shared `nshah37` database.

```sql
USE nshah37;
```
---

### 0.2 Identify Species with the Highest Number of Observations

This query ranks species by total observation count to ensure sufficient data density for spatial aggregation.

```sql
SELECT
    scientific_name,
    COUNT(*) AS observation_count
FROM observations_raw
WHERE scientific_name RLIKE '[A-Za-z]'
GROUP BY scientific_name
ORDER BY observation_count DESC
LIMIT 10;
```
---

### 0.3 Identify Species with the Greatest Temporal Coverage

```sql
SELECT
    scientific_name,
    COUNT(DISTINCT YEAR(observed_on)) AS num_years
FROM observations_raw
WHERE scientific_name RLIKE '[A-Za-z]'
GROUP BY scientific_name
ORDER BY num_years DESC
LIMIT 20;
```
---

### 0.4 Species Selection

Species appearing in both the high observation-count and high temporal-coverage rankings were considered suitable for analysis. Mule deer (Odocoileus hemionus) satisfies both criteria and was selected for downstream processing.

---

## Step 1 : Visualization Code

### Step 1.1: Create a Clean Base View for Analysis - odocoileus_base

```sql
CREATE OR REPLACE VIEW odocoileus_base AS
SELECT
id,
latitude,
longitude,
YEAR(FROM_UNIXTIME(UNIX_TIMESTAMP(observed_on, 'yyyy-MM-dd'))) AS year,
MONTH(FROM_UNIXTIME(UNIX_TIMESTAMP(observed_on, 'yyyy-MM-dd'))) AS month,
temperature_2m
FROM fab_four_master_table
WHERE scientific_name = 'Odocoileus hemionus'
  AND latitude IS NOT NULL
  AND longitude IS NOT NULL
  AND temperature_2m IS NOT NULL
  AND YEAR(FROM_UNIXTIME(UNIX_TIMESTAMP(observed_on, 'yyyy-MM-dd'))) BETWEEN 2020 AND 2022;
```
---

### Ste


