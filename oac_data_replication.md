# **Replicate Data using BICC and OAC Replication**

[Preparing Data in Oracle Analytics Cloud](https://docs.oracle.com/en/cloud/paas/analytics-cloud/acabi/replicate-data.html#GUID-5FDDF00E-4774-44E7-AE56-572D003C2062)
[Blog on BICC - OCI Object Storage](https://www.ateam-oracle.com/set-up-oracle-fusion-saas-business-intelligence-cloud-connector-bicc-to-use-oracle-cloud-infrastructure-oci-object-storage)

### **Pre-requisites**

- You have an ERP Cloud instance account that has the required priviledges to access bicc and create connections and replications (see link above for details)
- You have created a target database (DBCS or ADW/ATP).
- You have provisioned a Oracle Analytics Cloud (OAC) Service.
- You have a local (not federated) cloud userid.
- The cloud userid has no more than one api key.  This is because you will need to create and add two additional public API keys from the new connections you will create below.  These connections do not allow you to use existing keys, and the cloud user accounts only allow a maximum of three API keys.
- You have created an OCI bucket in your tenancy.

### **Log into the Oracle Business Intelligence Cloud Connector Console	and Create a Connection to Object Storage**

- Log into bicc using the following URL format: `https://<server>/biacm`

![](images/002.png)

- Select configure external source

![](images/003.png)

- Select OCI Object Storage Connection

![](images/004.png)

- Select Add

![](images/005.png)

- Enter connection details.  Note you will need to generate a new OCI API Signing Key.  Also note you will not be able to test this connection until you add the public key to your cloud user account.

![](images/006.png)

- Export your public key and save to your desktop.

![](images/007.png)

- Open the public key you just downloaded and copy it.

![](images/008.png)

- Add new public key in your userid.

![](images/009.png)

- Paste your public key.

![](images/010.png)

- Go back to the BICC Console and test your connection.

![](images/011.png)

![](images/012.png)

- At this point everything else is done in OAC.  BICC does allow you to create, schedule, and run data exports, and if you do not wish to use OAC then you would do this, and then reference the downloaded objects in object storage.  Note the BICC jobs create (optionally) the metadata, data, and a manafest file (together in a zip file).  However OAC does not use this BICC scheduler and does not use BICC jobs to extract data.  Instead it creates its own jobs without the metadata (only a single data file).  To see the difference we will create and run a BICC job which **will NOT** be used by OAC.   

- Select Manage Extract Schedules.

![](images/013.png)

- Add schedule.

![](images/014.png)

- Select Application Data Extract

![](images/015.png)

- Give it a name and hit next.

![](images/016.png)

- Select an offering.

![](images/017.png)

- Select a business object and hit next.

![](images/018.png)

- Select External Storage - select the object store you created above.

![](images/019.png)

- Save.

![](images/020.png)

- After you save a job should kick off and execute.  The status is initially `WAIT`, and then it will execute.

![](images/021.png)

- Return to object storage and review files.

![](images/022.png)

- Download the three objects and review.

![](images/023.png)

![](images/024.png)

![](images/025.png)

![](images/026.png)

### **Configure OAC Replication**

- Log into OAC and select create replication connection.

![](images/027.png)

- Select Fusion Application Storage.

![](images/028.png)

- Enter details up to the API key, then select generate, and then copy.  Although you see the fingerprint of the key, when you hit the copy button it actually copies the public key in the background (confusing!) which is what you need.

![](images/029.png)

- In a **separate browser tab** open the cloud userid and create a new API Signing Key.

![](images/030.png)

- Add public key and paste the key you copied above.

![](images/031.png)

![](images/032.png)

- Return to the OAC connection and enter the remaining details.  You will be entering your ERP Cloud userid/pw and the BICC connection name.

![](images/033.png)

- Create Replication Connection for you target database.  In this case we will target dbcs.

![](images/035.png)

- Save

![](images/036.png)

- Create a new Data Replication.

![](images/034.png)

- Select your erp source.

![](images/037.png)

- Next select your dbcs target.

![](images/038.png)

- Select data store paths to better identify the object names you see in BICC.  Also note that if your developers have created custom fields and have created custom view objects you can select those here.

![](images/039.png)

- Select an object to replicate.  Note that you need be sure to **select the check box on the left**.  Also note that not all fields are selected by default.  If you wish all fields to be select then check the source check box.

![](images/040.png)

- Select `Run Replication Flow`.  Give the replication a name and select save and run.

![](images/041.png)

-  The job will take a few minutes to run.  When it is complete open the connection in SQL Developer (SQL Dev setup not shown here) to review the data.  THe job creates several tables in addition to the replication data.

![](images/042.png)

![](images/043.png)

![](images/044.png)

![](images/045.png)

![](images/046.png)

![](images/047.png)

- Return to object storage to review the data that was stored from the job.

![](images/048.png)

![](images/049.png)

- Compare this with the table that was replicated into DBCS.  Note the additional columns that were added by OAC to track process information.

![](images/050.png)

![](images/051.png)
