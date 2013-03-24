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
Hadoop commands
===============
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
