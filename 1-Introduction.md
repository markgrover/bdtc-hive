To do before the presentation
=============================
1. Set up and install Hadoop and Hive. Easiest way is to actually download a demo VM with Hadoop, Hive and HBase installed. Cloudera Demo VMs are available.
You will need an 64-bit OS on your computer. 
 * Install a VM player: [VirtualBox](https://www.virtualbox.org/wiki/Downloads?elq=2cd90eb19a8d4d2fbd4698478134e333&elqCampaignId=367) (recommended), [VMware Player for Windows or Linux](https://my.vmware.com/web/vmware/free?elq=0e1c89a03b0e48e2a5ce499b194daff4&elqCampaignId=&elq=2cd90eb19a8d4d2fbd4698478134e333&elqCampaignId=367#desktop_end_user_computing/vmware_player/6_0), or [VMware Fusion for Mac](http://www.vmware.com/products/fusion/?elq=2cd90eb19a8d4d2fbd4698478134e333&elqCampaignId=367). (Again, a 64-bit host OS is required.)
 * Install the VM image for the lab: Download [here](https://ccp.cloudera.com/display/SUPPORT/Cloudera's+Hadoop+Demo+VM+for+CDH4). (Note: be sure to install the correct image for whatever player you have, and be sure to unpack the file before using.)
 * If using a PC, confirm that your laptop is configured to support virtualization. (Enter BIOS, find the "Virtualization" settings [usually under "Security"] and enable all the virtualization options.)
 For common troubleshooting tips during installation, read Ryan Blue's troubleshooting tips [here](https://github.com/rdblue/cdk-examples/tree/0.7.0/demo?elq=2cd90eb19a8d4d2fbd4698478134e333&elqCampaignId=367#troubleshooting).

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
