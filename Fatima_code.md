
## Wildlife Satio-Temporal Analysis 
#### Question: 
#### "Are species observed at different times a day?"


#### Step 1: Connect to the correct database so all queries run on the correct dataset
#### This is where all team tables (raw + master) are stored here 
```sql
USE nshah37;
```
---
#### Step 2: View the available tables
#### Here we can Verify that the cleaned master dataset exists 
```sql
SHOW TABLES; 

```
---
#### STEP 3: Preview the master dataset
#### To insure that the required columns exist before analysis
```sql
SELECT * 
FROM fab_four_master_table 
LIMIT 10;
```
---
#### Step 4: Identify top 5 most observed species
#### This dataset is very large the approach taken we to reduce for a clearer insights and visualization
```sql
SELECT 
    common_name,
    COUNT(*) AS total_count
FROM fab_four_master_table
GROUP BY common_name
ORDER BY total_count DESC
LIMIT 5;
```
---
#### Step 5: Time a day when most observations recorded 
#### To identify the hours of the day when the highest number of wildlife observation occurs
```sql
SELECT
    obs_hour AS hour_of_day,
    COUNT(*) observations
FROM fab_hour_master_table
GROUP BY obs_hour
ORDER BY observations DESC
LIMIT 5;
```
---

#### Step 6: Analyze time-of-day patterns for top species
#### To convert numeric hour into meaningful categories that we are able to understand (Morning, Afternoon, etc.)
#### and to also helps interpret animal behavior patterns we did the following code. 
```sql
SELECT
    common_name,
    CASE
        WHEN obs_hour BETWEEN 5 AND 11 THEN 'Morning'
        WHEN obs_hour BETWEEN 12 AND 16 THEN 'Afternoon'
        WHEN obs_hour BETWEEN 17 AND 20 THEN 'Evening'
        ELSE 'Night'
    END AS time_of_day,
    COUNT(*) AS observations
FROM fab_four_master_table
WHERE common_name IN (
    SELECT common_name
    FROM fab_four_master_table
    GROUP BY common_name
    ORDER BY COUNT(*) DESC
    LIMIT 5
)
GROUP BY
    common_name,
    CASE
        WHEN obs_hour BETWEEN 5 AND 11 THEN 'Morning'
        WHEN obs_hour BETWEEN 12 AND 16 THEN 'Afternoon'
        WHEN obs_hour BETWEEN 17 AND 20 THEN 'Evening'
        ELSE 'Night'
    END
ORDER BY observations DESC;
```
---

#### STEP 8: Export the results to HDFS
#### To create a visualization tool of the data (Excel) and it Handles large data better than local export
```sql 
INSERT OVERWRITE DIRECTORY '/user/ffigue14/output'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT
    common_name,
    CASE
        WHEN obs_hour BETWEEN 5 AND 11 THEN 'Morning'
        WHEN obs_hour BETWEEN 12 AND 16 THEN 'Afternoon'
        WHEN obs_hour BETWEEN 17 AND 20 THEN 'Evening'
        ELSE 'Night'
    END AS time_of_day,
    COUNT(*) AS observations
FROM fab_four_master_table
WHERE common_name IN (
    SELECT common_name
    FROM fab_four_master_table
    GROUP BY common_name
    ORDER BY COUNT(*) DESC
    LIMIT 5
)
GROUP BY
    common_name,
    CASE
        WHEN obs_hour BETWEEN 5 AND 11 THEN 'Morning'
        WHEN obs_hour BETWEEN 12 AND 16 THEN 'Afternoon'
        WHEN obs_hour BETWEEN 17 AND 20 THEN 'Evening'
        ELSE 'Night'
    END;
```
---
#### Lastly, we transfer the the results into Excel where we were able to make a visulization for a better representation of the data to see species observed at different times a day.
