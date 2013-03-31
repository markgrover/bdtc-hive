bdtc-hive
=========

Presentation on Apache Hive at Big Data TechCon

To do before the presentation
=============================
1. Set up and install Hadoop and Hive. Easiest way is to actually download a demo VM with Hadoop, Hive and HBase installed. Cloudera Demo VMs are available [here](https://ccp.cloudera.com/display/SUPPORT/Cloudera's+Hadoop+Demo+VM+for+CDH4).
2. On **your demo VM**, download the dataset by git cloning this repository:
<pre>
<code>
cd ~
git clone git://github.com/markgrover/bdtc-hive.git
(This may take a minute because of the large dataset).
tar -xzvf bdtc-hive/2008.tar.gz
</code>
</pre>
The source of this dataset is http://stat-computing.org/dataexpo/2009/the-data.html. The dataset contains on-time flight performance data from 2008, originally released by [Research and Innovative Technology Administration (RITA)](http://www.transtats.bts.gov/Fields.asp?Table_ID=236).
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

Load the dataset
================
* On bash: Create a HDFS directory
<pre>
<code>
hadoop fs -mkdir /user/hive/warehouse/flight_data
</code>
</pre>
* On bash: Load the data to HDFS
<pre>
<code>
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
INSERT INTO flight_data_p PARTITION(month=1)
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

Miscellaneous notes
===================
To disable safe mode:
<pre>
<code>
hadoop dfsadmin -safemode leave
</code>
</pre>
