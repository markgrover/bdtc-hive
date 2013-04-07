Joins
=====
* On bash: load the second dataset related to airport codes:

<pre>
<code>
hadoop fs -mkdir /user/hive/warehouse/airports
hadoop fs -put airports.csv /user/hive/warehouse/airports/airports.csv
</code>
</pre>

* On hive shell: create the airports table

<pre>
<code>
CREATE EXTERNAL TABLE airports(
   name STRING,
   country STRING,
   area_code INT,
   code STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/airports';
</code>
</pre>

* On hive shell, list some rows from the airports table:

<pre>
<code>
SELECT
   *
FROM
   airports
LIMIT 10
</code>
</pre>

* On hive shell: run a join query to find the average delay in January 2008 for each airport and to print out the airport's name:

<pre>
<code>
SELECT
   name,
   AVG(arr_delay)
FROM
   flight_data_p f
   INNER JOIN airports a
   ON (f.origin=a.code)
WHERE
   month=1
GROUP BY
   name;
</code>
</pre>

* Now, let's run a query to run Map-side join using query hint:

<pre>
<code>
SELECT
   /*+ MAPJOIN(airports) */
   name,
   AVG(arr_delay)
FROM
   flight_data_p f
   INNER JOIN airports a
   ON (f.origin=a.code)
WHERE
   month=1
GROUP BY
   name;
</code>
</pre>


* On hive shell: set an optimization property and run the same query to autoconvert to map join:

<pre>
<code>
SET hive.auto.convert.join=true;
SELECT
   name,
   AVG(arr_delay)
FROM
   flight_data_p f
   INNER JOIN airports a
   ON (f.origin=a.code)
WHERE
   month=1
GROUP BY
   name;
</code>
</pre>
