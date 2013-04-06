Dynamic partitioning
====================
* On hive shell: set some properties to enable dynamic partitioning

<pre>
<code>
SET hive.exec.dynamic.partition.mode=nonstrict;
</code>
</pre>

* On Hive shell: populate all partitions of the partitioned table from the unpartitioned table

<pre>
<code>
INSERT OVERWRITE TABLE flight_data_p PARTITION(month)
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
   late_aircraft_delay,
   month
FROM
   flight_data;
</pre>
</code>
