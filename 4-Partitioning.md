Partitioning in Hive
====================
* On hive shell: Create a new table, *flight_data_p*, partitioned by month

<pre>
<code>
CREATE EXTERNAL TABLE flight_data_p(
   year INT,
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
PARTITIONED BY (month int)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/flight_data_p';
</code>
</pre>

* On hive shell: Populate some partitions of this new partitioned table from the existing non-partitioned table:

<pre>
<code>
INSERT INTO TABLE flight_data_p PARTITION(month=1)
SELECT
   year,
   day,
   day_of_week,
   dep_time,
   crs_dep_time,
   arr_time,
   crs_arr_time,
   unique_carrier,
   flight_num,
   tail_num,
   actual_elapsed_time,
   crs_elapsed_time,
   air_time,
   arr_delay,
   dep_delay,
   origin,
   dest,
   distance,
   taxi_in,
   taxi_out,
   cancelled,
   cancellation_code,
   diverted,
   carrier_delay,
   weather_delay,
   nas_delay,
   security_delay,
   late_aircraft_delay
FROM
   flight_data
WHERE
   month=1;
</code>
</pre>

* On Hive shell: Do the same for another month:

<pre>
<code>
INSERT INTO TABLE flight_data_p PARTITION(month=2)
SELECT
   year,
   day,
   day_of_week,
   dep_time,
   crs_dep_time,
   arr_time,
   crs_arr_time,
   unique_carrier,
   flight_num,
   tail_num,
   actual_elapsed_time,
   crs_elapsed_time,
   air_time,
   arr_delay,
   dep_delay,
   origin,
   dest,
   distance,
   taxi_in,
   taxi_out,
   cancelled,
   cancellation_code,
   diverted,
   carrier_delay,
   weather_delay,
   nas_delay,
   security_delay,
   late_aircraft_delay
FROM
   flight_data
WHERE
   month=2;
</code>
</pre>

* On hive shell: verify the partitions got created:

<pre>
<code>
SHOW PARTITIONS flight_data_p;
</code>
</pre>

On bash: verify them on HDFS as well:

<pre>
<code>
hadoop fs -ls /user/hive/warehouse/flight_data_p/
</code>
</pre>

* On Hive shell: compare the response times for a query with a predicate on month for the unpartitioned and partitioned table.

First, run the query on the non-partitioned table:

<pre>
<code>
SELECT
   avg(arr_delay)
FROM
   flight_data
WHERE
   month=1
   AND origin='SFO';
</code>
</pre>

Now, run the same query on the partitioned table:

<pre>
<code>
SELECT
   avg(arr_delay)
FROM
   flight_data_p
WHERE
   month=1
   AND origin='SFO';
</code>
</pre>
