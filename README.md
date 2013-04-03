bdtc-hive
=========

Presentation on Apache Hive at Big Data TechCon

To do before the presentation
=============================
1. Set up and install Hadoop and Hive. Easiest way is to actually download a demo VM with Hadoop, Hive and HBase installed. Cloudera Demo VMs are available [here](https://ccp.cloudera.com/display/SUPPORT/Cloudera's+Hadoop+Demo+VM+for+CDH4).
2. On **your demo VM**, download the datasets by git cloning this repository:
<pre>
<code>
cd ~
git clone git://github.com/markgrover/bdtc-hive.git
(This may take a minute because of the large datasets).
</code>
</pre>
There are 2 datasets in the repo.

    a) The first dataset contains on-time flight performance data from 2008, originally released by [Research and Innovative Technology Administration (RITA)](http://www.transtats.bts.gov/Fields.asp?Table_ID=236). The source of this dataset is http://stat-computing.org/dataexpo/2009/the-data.html. The dataset 

    b) The second dataset contains listing of various airport codes in continental US, Puerto Rico and US Virgin Islands. The source of this dataset is http://www.world-airport-codes.com/ The data was scraped from this website and then cleansed to be in its present CSV form.

3. Ensure that your virtual machine can connect to the internet.
FYI, if you are running VirtualBox on Ubuntu 12.10, you may be hitting a known bug related to internet connectivity of Demo VM. See [here](http://askubuntu.com/questions/211603/problems-with-nat-adapater-since-upgrade-to-ubuntu-12-10) for more details.

Run Hadoop examples
===============
* Verify contents of HDFS
<pre>
<code>
hadoop fs -ls /
</code>
</pre>
* Pi job
<pre>
<code>
hadoop jar /usr/lib/hadoop-0.20-mapreduce/hadoop*examples.jar pi 10 100
</code>
</pre>
* Wordcount job
<pre>
<code>
hadoop fs -mkdir input
hadoop fs -put /etc/hadoop/conf/*.xml input
hadoop jar /usr/lib/hadoop-0.20-mapreduce/hadoop*examples.jar wordcount input output
</code>
</pre>
By the way, if you re-run this job, it will fail. Why is that?

Load the flight dataset
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
SELECT * FROM flight_data LIMIT 10; 
</code>
</pre>

Partitioning in Hive
===================
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
* On Hive shell: compare the response times for a query with a predicate on month for the unpartitioned and partitioned table.

First, run the query on the non-partitioned table:
<pre>
<code>
SELECT
   avg(arr_delay)
FROM
   flight_data
WHERE
   month=1;
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
   month=1;

</code>
</pre>

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

* On hive shell: set an optimization property and run the same query:
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
set hive.enforce.bucketing = true;
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
SET hive.enforce.bucketing=false;
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

Miscellaneous notes
===================
To disable safe mode:
<pre>
<code>
hadoop dfsadmin -safemode leave
</code>
</pre>

To configure your hive-site.xml to use MySQL:

<pre>
    <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://127.0.0.1/metastore?createDatabaseIfNotExist=true</value>
    <description>JDBC connect string for a JDBC metastore</description>
    </property>

    <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
    </property>

    <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
    </property>

    <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
    </property>
</pre>
