# ODI on JCS Install and Config Notes

Derrick Cameron
Mar 8, 2019

This are some notes to help you through the installation and configuration of ODI on JCS.  This requires a database.  It was tested on 12.1, but should work on 12.2 (Classic and OCI).  There are some differences when using DB Classic versus OCI, but this is just in the beginning.  

### **Create a DBCS Service (Classic or OCI)**

Not documented, but straightforward.  Be sure to select backup.

If the Database is on OCI or ADW/ATP you need to create the following policies.

OCI: 

```
Allow service PSM to inspect database-family in compartment <compartment_name>
Allow service PSM to inspect database-family in compartment <compartment_name>
Allow service PSM to inspect vcns in compartment <compartment_name>
Allow service PSM to use subnets in compartment <compartment_name>
Allow service PSM to use vnics in compartment <compartment_name>
Allow service PSM to manage security-lists in compartment <compartment_name>
Allow service PSM to manage all-resources in compartment <compartment_name>

Click on button “Create” at the bottom of the screen
```

ADW: 

`Allow service PSM to inspect autonomous-database in compartment <compartment_name>`

### **Create JCS**

If using DB on OCI, first apply [this patch](https://support.oracle.com/epmos/faces/DocContentDisplay?_afrLoop=500232808913353&id=2434657.1&_afrWindowMode=0&_adf.ctrl-state=1p33fhsy9_9) to JCS.

**Be sure to select the availability domain if the DB is on OCI**

![](images/001.png)

You should see something similar to this:

```
Jan 17, 2019 10:32:59 PM UTC	Activity Submitted
Jan 17, 2019 10:33:03 PM UTC	Activity Started
Jan 17, 2019 10:33:05 PM UTC	Started operation to create service [JCSODICS] in identity domain [idcs-a8db2211d48045aa9bbc9f93fe2475bf].
Jan 17, 2019 10:33:06 PM UTC	Creating resources [jcsodics-wls-1] for service [JCSODICS].
Jan 17, 2019 10:39:14 PM UTC	Completed creating service [JCSODICS] resources in domain [idcs-a8db2211d48045aa9bbc9f93fe2475bf].
Jan 17, 2019 10:41:27 PM UTC	Started operation to check provisioning status on the VMs for [WLS]
Jan 17, 2019 10:49:57 PM UTC	Provisioning Succeeded on host(s): jcsodics-wls-1
Jan 17, 2019 10:49:57 PM UTC	Provisioning of component [WLS] succeeded.
Jan 17, 2019 10:50:40 PM UTC	Activity Ended
```

### **Setup JCS Desktop**

These instructions follow [this documentation](https://www.oracle.com/webfolder/technetwork/tutorials/obe/cloud/javaservice/JCS/FMW_UpperStack_on_JCS/odi_on_jcs_obe/provisioning_oracle_data_integrator_cloud_service.html#section1).

- ssh into the image with your private key and enter the following:

`sudo killall gnome-screensaver`

- Add access rule - open 5901 (WLS_ADMIN)

![](images/002.png)

- Open firewall (actually not sure this is required).

```
sudo sed -i 's/IPTABLES_SAVE_ON_RESTART="no"/IPTABLES_SAVE_ON_RESTART="yes"/g' /etc/sysconfig/iptables-config
sudo iptables -I INPUT -p tcp -m tcp --dport 5901 -j ACCEPT
sudo service iptables restart
```

- Set the vnc password and start the server:

```
sudo su - oracle

vncpasswd <enter password>
vncserver -nolisten local -geometry 1680x1050
```
### **Install ODI**

- Enter the following in a terminal window.

```
cd /u01/zips/upperstack
unzip ODI.zip
java -jar fmw_12.2.1.2.6_odi_generic.jar
```

![](images/003.png)
![](images/004.png)
![](images/005.png)
![](images/006.png)
![](images/007.png)
![](images/008.png)
![](images/009.png)
![](images/010.png)
![](images/011.png)
![](images/012.png)

- Enter the following:

```
sudo su - oracle
more /u01/data/domains/JCSODICS_domain/config/jdbc/mds-owsm-jdbc.xml
```

- note the SP number in the text file - you will need this later (eg: SP1547852814)

### **Run RCU**

- Enter the following:

```
cd /u01/app/oracle/middleware/oracle_common/bin
./rcu
```
![](images/013.png)
![](images/014.png)
![](images/015.png)
![](images/016.png)
![](images/017.png)
![](images/018.png)
![](images/019.png)
![](images/020.png)
![](images/021.png)
![](images/022.png)
![](images/023.png)

### **Log into WLS and stop the admin and managed servers**

- Log into the console and do the following:

![](images/024.png)
![](images/025.png)
![](images/026.png)

### **Create and Configure ODI Domain**

- Enter the following.

```
cd /u01/app/oracle/middleware/oracle_common/common/bin
./config.sh
```

![](images/027.png)

- Select the following items - need to scroll down for one of them (selecting one will cause others to be selected with green checks):

```
    Oracle Data Integrator - Agent
    Oracle Data Integrator - Agent Libraries
    Oracle Data Integrator SDK Shared Library Template
    Oracle Data Integrator - Console
    Oracle Data Integrator – Standalone Collocated Agent
    Oracle Enterprise Manager Plugin for ODI
```

![](images/028.png)
![](images/029.png)
![](images/030.png)
![](images/031.png)
![](images/032.png)
![](images/033.png)
![](images/034.png)
![](images/035.png)
![](images/036.png)
![](images/037.png)
![](images/038.png)
![](images/039.png)
![](images/040.png)
![](images/041.png)
![](images/042.png)
![](images/043.png)
![](images/044.png)
![](images/045.png)
![](images/046.png)
![](images/047.png)
![](images/048.png)

- Be careful in the next few steps - pay attention to where you are on the Cluster tree on the right.

![](images/049.png)
![](images/050.png)
![](images/051.png)
![](images/052.png)
![](images/053.png)
![](images/054.png)

### **Start Studio and create agent**

- Enter the following:

```
cd /u01/app/oracle/middleware/odi/studio
./odi.sh
```

![](images/055.png)
![](images/056.png)
![](images/057.png)
![](images/058.png)
![](images/059.png)
![](images/060.png)
![](images/061.png)
![](images/062.png)
![](images/063.png)
![](images/064.png)

### **Start WLS**

- Enter the following:

```
cd /u01/app/oracle/middleware/oracle_common/common/bin
./wlst.sh
```

- Connect (mnConnect):

```
wls:/offline> nmConnect('weblogic','Wel_Come#123','129.156.112.61','5556','JCSODICS_domain','/u01/data/domains/JCSODICS_domain')

Connecting to Node Manager ...
<Jan 18, 2019 6:03:51 PM UTC> <Info> <Security> <BEA-090905> <Disabling the CryptoJ JCE Provider self-integrity check for better startup performance. To enable this check, specify -Dweblogic.security.allowCryptoJDefaultJCEVerification=true.> 
<Jan 18, 2019 6:03:51 PM UTC> <Info> <Security> <BEA-090906> <Changing the default Random Number Generator in RSA CryptoJ from ECDRBG128 to HMACDRBG. To disable this change, specify -Dweblogic.security.allowCryptoJDefaultPRNG=true.> 
<Jan 18, 2019 6:03:51 PM UTC> <Info> <Security> <BEA-090909> <Using the configured custom SSL Hostname Verifier implementation: weblogic.security.utils.SSLWLSHostnameVerifier$NullHostnameVerifier.> 
Successfully Connected to Node Manager.
```

- Start (nmStart):

```
wls:/nm/JCSODICS_domain> nmStart('JCSODICS_adminserver')

Starting server JCSODICS_adminserver ...
Successfully started server JCSODICS_adminserver ...
wls:/nm/JCSODICS_domain> 
```

- Log into WL Server

![](images/065.png)
![](images/066.png)
![](images/067.png)
![](images/068.png)
![](images/069.png)
![](images/070.png)
![](images/071.png)
![](images/072.png)
![](images/073.png)
![](images/074.png)
![](images/075.png)

### **Test Agent**

- Return to ODI Studio, select the Topology tab, and then expand agents.  Select the agent and then select the test tab.  Note that an agent is not required to run mappings (eg. for POC), but is recommended for performance reasons.

![](images/076.png)

![](images/077.png)