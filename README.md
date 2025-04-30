# Data_Engineer_Practice_2_Data_Warehousing
```
CREATE OR REPLACE EXTERNAL TABLE dtc-de-course-454300.trips_data_all.yellow_trip_data_2024_external
OPTIONS(
  format = 'PARQUET',
  uris = ['gs://dtc-de-course-454300-bucket/raw/yellow_tripdata/2024/yellow_tripdata_2024-*.parquet']
);



CREATE OR REPLACE TABLE dtc-de-course-454300.trips_data_all.yellow_trip_data_2024 AS
SELECT * FROM dtc-de-course-454300.trips_data_all.yellow_trip_data_2024_external;

--Question 1: What is count of records for the 2024 Yellow Taxi Data?
SELECT COUNT(*) FROM dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;
-- 20332093 records total

-- Question 2: Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables. What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

SELECT 
  COUNT(DISTINCT(PULocationID)) 
FROM 
  dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;
-- This query will process 155.12 MB when run.


SELECT 
  COUNT(DISTINCT(PULocationID)) 
FROM 
  dtc-de-course-454300.trips_data_all.yellow_trip_data_2024_external;
-- This query will process 0 B when run. 


SELECT PULocationID FROM dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;
-- This query will process 155.12 MB when run.

SELECT PULocationID,DOLocationID FROM dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;
-- This query will process 310.24 MB when run.

-- ANSWER: BigQuery is a columnar database, and it only scans the specific columns requested in the query. Querying two columns (PULocationID, DOLocationID) requires reading more data than querying one column (PULocationID), leading to a higher estimated number of bytes processed.


-- Question 4: How many records have a fare_amount of 0?
SELECT 
  count(*) 
FROM
  dtc-de-course-454300.trips_data_all.yellow_trip_data_2024
WHERE 
  fare_amount = 0;
-- ANSWER: 8333

-- Question 5: What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)

--ANSWER: Partition by tpep_dropoff_datetime and Cluster on VendorID
CREATE OR REPLACE TABLE dtc-de-course-454300.trips_data_all.yellow_trip_data_2024_partitioned_clustered 
PARTITION BY date(tpep_dropoff_datetime)
CLUSTER BY VendorID AS 
SELECT * FROM dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;


-- Question 6: Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 2024-03-01 and 2024-03-15 (inclusive).

--Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 5 and note the estimated bytes processed. What are these values?

SELECT
  distinct(VendorID)
FROM
  dtc-de-course-454300.trips_data_all.yellow_trip_data_2024
WHERE 
  date(tpep_dropoff_datetime) between '2024-03-01' and '2024-03-15';

-- This query will process 310.24 MB when run.


SELECT
  distinct(VendorID)
FROM
  dtc-de-course-454300.trips_data_all.yellow_trip_data_2024_partitioned_clustered
WHERE 
  date(tpep_dropoff_datetime) between '2024-03-01' and '2024-03-15';

--This query will process 26.84 MB when run.


--ANSWER: 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table

--Question 7: Where is the data stored in the External Table you created?
--ANSWER: GCP Bucket

--Question 8: It is best practice in Big Query to always cluster your data:
--ANSWER: False, only when the column is high cardinality and where, groupby, and order by are used freuently on the column(s).
```


select count(*) from dtc-de-course-454300.trips_data_all.yellow_trip_data_2024;

