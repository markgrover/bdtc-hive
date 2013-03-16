bdtc-hive
=========

Presentation on Apache Hive at Big Data TechCon

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
