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


