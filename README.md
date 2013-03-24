bdtc-hive
=========

Presentation on Apache Hive at Big Data TechCon

To do before the presentation
=============================
1. Set up and install Hadoop and Hive. Easiest way is to actually download a demo VM with Hadoop, Hive and HBase installed. Cloudera Demo VMs are available [here](https://ccp.cloudera.com/display/SUPPORT/Cloudera's+Hadoop+Demo+VM+for+CDH4).
2. On your demo VM, download the dataset (source: http://stat-computing.org/dataexpo/2009/the-data.html)
<pre>
<code>
mkdir -p ~/hive
cd ~/hive
wget http://stat-computing.org/dataexpo/2009/2008.csv.bz2
bzip2 -d 2008.csv.bz2
</code>
</pre>
The dataset contains on-time flight performance data from 2008, originally released by [Research and Innovative Technology Administration (RITA)](http://www.transtats.bts.gov/Fields.asp?Table_ID=236).
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
* On-time flight performance data from 2008
<pre>
<code>
hadoop fs -mkdir /user/hive/warehouse/flight_data
</code>
</pre>
* Verify it got loaded
<pre>
<code>
hadoop fs -ls /
</code>
</pre>
* Create hive table
<pre>
<code>
CREATE EXTERNAL TABLE flight_data(
year int,
month int,
day int,
day_of_week int,
dep_time int,
crs_dep_time int,
arr_time int,
crs_arr_time int,
unique_carrier string,
flight_num int,
tail_num string,
actual_elapsed_time int,
crs_elapsed_time int,
air_time int,
arr_delay int,
dep_delay int,
origin string,
dest string,
distance int,
taxi_in int,
taxi_out int,
cancelled boolean,
cancellation_code string,
diverted boolean,
carrier_delay string,
weather_delay string,
nas_delay string,
security_delay string,
late_aircraft_delay string
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LOCATION '/user/hive/warehouse/flight_data';
</code>
<pre>

Miscellaneous notes
===================
To disable safe mode:
<pre>
<code>
hadoop dfsadmin -safemode leave
</code>
</pre>
