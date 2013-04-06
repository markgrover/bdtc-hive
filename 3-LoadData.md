3. Load the flight dataset
=======================

* On bash: Create a HDFS directory

<pre>
<code>
hadoop fs -mkdir /user/hive/warehouse/flight_data
</code>
</pre>

* On bash: Untar the flight dataset and load it to HDFS

<pre>
<code>
tar -xzvf bdtc-hive/2008.tar.gz
hadoop fs -put 2008.csv /user/hive/warehouse/flight_data/2008.csv
</code>
</pre>

* On bash: Verify it got loaded

<pre>
<code>
hadoop fs -ls /user/hive/warehouse/flight_data/
</code>
</pre>

* On hive shell: Create hive table, *flight_data*:

<pre>
<code>
CREATE EXTERNAL TABLE flight_data(
   year INT,
   month INT,
   day INT,
   day_of_week INT,
   dep_time INT,
   crs_dep_time INT,
   arr_time INT,
   crs_arr_time INT,
   unique_carrier STRING,
   flight_num INT,
   tail_num STRING,
   actual_elapsed_time INT,
   crs_elapsed_time INT,
   air_time INT,
   arr_delay INT,
   dep_delay INT,
   origin STRING,
   dest STRING,
   distance INT,
   taxi_in INT,
   taxi_out INT,
   cancelled INT,
   cancellation_code STRING,
   diverted INT,
   carrier_delay STRING,
   weather_delay STRING,
   nas_delay STRING,
   security_delay STRING,
   late_aircraft_delay STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/flight_data';
</code>
</pre>

* On hive shell: Ensure that the create and load table were successful by running the following command:

<pre>
<code>
SHOW TABLES;
SELECT
   *
FROM
   flight_data
LIMIT 10; 
</code>
</pre>
