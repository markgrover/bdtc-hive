Sampling and Bucketing
======================
* On Hive shell, let's pick a sample of the existing flight_data table based on unique_carrier column:

<pre>
<code>
SELECT
   *
FROM
   flight_data TABLESAMPLE(BUCKET 1 OUT OF 4 ON unique_carrier)s LIMIT 100;
</code>
</pre>

* On Hive shell: create a bucketed table:

<pre>
<code>
CREATE EXTERNAL TABLE flight_data_b(
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
CLUSTERED BY (unique_carrier) INTO 4 BUCKETS
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/flight_data_b';
</code>
</pre>

* Now, let's populate this table. Before we do that, we need to populate a property that would ensure the number of reducers is the same as number of buckets in the destination table. On hive shell, set the following property:

<pre>
<code>
SET hive.enforce.bucketing = true;
</code>
</pre>

* On hive shell: run the command to populate the bucketed table (it's no different than running the command on a regular table.

<pre>
<code>
INSERT OVERWRITE TABLE flight_data_b
SELECT
   *
FROM
   flight_data;
</code>
</pre>

* Disable the enforce bucketing property back to its default value:

<pre>
<code>
SET hive.enforce.bucketing = false;
</code>
</pre>

* On bash: verify the number of files in the HDFS directory corresponding to flight_data_b table.

<pre>
<code>
hadoop fs -ls /user/hive/warehouse/flight_data_b/
</code>
</pre>

* On hive shell: run a sampling query on the bucketed table

<pre>
<code>
SELECT
   *
FROM
   flight_data_b TABLESAMPLE(BUCKET 1 OUT OF 4  ON unique_carrier)s LIMIT 100;
</code>
</pre>

* Compare the time betwen sampling on non-bucketed table with that on bucketed table
