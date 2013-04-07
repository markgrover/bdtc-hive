Custom Scripts
===========
* Let's first try using a simple cat map-side script

<pre>
<code>
SELECT
   TRANSFORM(name) USING '/bin/cat' as name
FROM
   airports
LIMIT 10
</code>
</pre>

* Now, let's do something more complicated (still as a custom map script)

<pre>
<code>
SELECT
   TRANSFORM(name) USING "/bin/sed -e 's/\s//g'" as name
FROM
   airports
LIMIT 10
</code>
</pre>
