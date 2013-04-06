To do before the presentation
=============================
1. Set up and install Hadoop and Hive. Easiest way is to actually download a demo VM with Hadoop, Hive and HBase installed. Cloudera Demo VMs are available [here](https://ccp.cloudera.com/display/SUPPORT/Cloudera's+Hadoop+Demo+VM+for+CDH4).
2. On **your demo VM**, download the datasets by git cloning this repository:

<pre>
<code>
cd ~
# Install git in case you don't already have it
sudo yum install git
git clone git://github.com/markgrover/bdtc-hive.git
# This may take a minute because of the large datasets
</code>
</pre>

There are 2 datasets in the repo.

a) The first dataset contains on-time flight performance data from 2008, originally released by [Research and Innovative Technology Administration (RITA)](http://www.transtats.bts.gov/Fields.asp?Table_ID=236). The source of this dataset is http://stat-computing.org/dataexpo/2009/the-data.html. The dataset 

b) The second dataset contains listing of various airport codes in continental US, Puerto Rico and US Virgin Islands. The source of this dataset is http://www.world-airport-codes.com/ The data was scraped from this website and then cleansed to be in its present CSV form.

3. Ensure that your virtual machine can connect to the internet.
FYI, if you are running VirtualBox on Ubuntu 12.10, you may be hitting a known bug related to internet connectivity of Demo VM. See [here](http://askubuntu.com/questions/211603/problems-with-nat-adapater-since-upgrade-to-ubuntu-12-10) for more details.
