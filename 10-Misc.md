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
<code>
&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
    &lt;value&gt;jdbc:mysql://127.0.0.1/metastore?createDatabaseIfNotExist=true&lt;/value&gt;
    &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;
</code>
</pre>
