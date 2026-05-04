# Step 0: Exploratory Species Analysis in Hive

This section documents the initial exploratory analysis performed in Hive
to identify suitable species for spatiotemporal analysis based on data
availability and temporal coverage.

---

## 0.1 Set the Active Hive Database

All analysis was performed using pre‑existing Hive tables in the shared
`nshah37` database.

```sql
USE nshah37;

---

## 0.2 Identify Species with the Highest Number of Observations

This query ranks species by total observation count to determine which species have sufficient data density for spatial aggregation.

```sql
SELECT
    scientific_name,
    COUNT(*) AS observation_count
FROM observations_raw
WHERE scientific_name RLIKE '[A-Za-z]'
GROUP BY scientific_name
ORDER BY observation_count DESC
LIMIT 10;


