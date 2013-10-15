Custom UDFs
===========
* Let's first use a built-in UDF. On hive shell:

<pre>
<code>
SELECT
  LOWER(origin)
FROM
   flight_data
LIMIT 10;
</code>
</pre>

* Now, let's write our own UDF. However, we need maven to build code, so let's install maven. On bash shell, type:

<pre>
<code>
cd ~
wget http://apache.mirrors.lucidnetworks.net/maven/maven-3/3.1.1/binaries/apache-maven-3.1.1-bin.tar.gz
tar -xzvf apache-maven-3.1.1-bin.tar.gz
export PATH=$PATH:$(pwd)/apache-maven-3.1.1/bin
</code>
</pre>

* Write some code. For now, let's just download it. On bash, git clone this repo

<pre>
<code>
cd ~
git clone git://github.com/markgrover/hive-translate.git
cd hive-translate
mvn clean package
</code>
</pre>

* Now, let's use the UDF we just compiled. On hive shell, type the following to register the UDF with Hive:

<pre>
<code>
ADD JAR target/translate-udf-0.0.1-SNAPSHOT.jar;
CREATE TEMPORARY FUNCTION my_translate AS 'org.mgrover.hive.translate.GenericUDFTranslate';
</code>
</pre>

* On Hive shell: type the following to use the newly created UDF:

<pre>
<code>
SELECT
   my_translate('abc','a','A')
FROM
   flight_data
LIMIT 10;
</code>
</pre>

* On web browser: let's browse through the UDF code:
https://github.com/markgrover/hive-translate/blob/master/src/main/java/org/mgrover/hive/translate/GenericUDFTranslate.java
