# **Replicate Data using Oracle Data Integrator With BICC**

Note:  Thanks to Martha Aguilar for her help with setup!  These instructions were developed over several days and used different compartments.  Generally you would put everything in one compartment, but this is not required.  There is some inconsistency in the instructions about which compartments are used, but it shouldn't matter where you put services as long as you know where you put them :).

Note the current V2 version of the Marketplace Image is missing the reverse engineering knowledge Module (RKM) for BICC.  You can [right click on this link to download it](./KM_RKM Oracle BI Cloud Connector.xml).

### **Prerequisites**

- You have an ERP Cloud instance account that has the required priviledges to access bicc and create connections and replications (see link above for details).  Note the application version must be 19C or greater.
- You have a local (not federated) cloud userid.  In this case I am using `api3_user`.
- You have created a connection to object storage in BICC (see the previous option for setup).
- You have created an OCI bucket in your tenancy.
- You have capacity for a 2.4 shape, see OCI menu Governance > Limits, Quotas and Usage.  Check to see you have an available 2.4 shape.
- You have a public/private pem key added to the local user, with the token in hand.

### **Deploy the ODI Marketplace Image**

- Log into the Cloud Console and go to the marketplace images.

![](images/063.png)

- Filter and select the ODI image.

![](images/064.png)

- Select your Compartment, accept terms.

![](images/065.png)

![](images/066.png)

- Either create a new VCN or use an existing one, and select an option for the ODI repositories (database schemas).  The simplest is embedded.

![](images/067.png)

- Enter a SSH public key, select an availability zone, and enter a VNC password.

![](images/068.png)

- It will take a few minutes to create.

![](images/069.png)

- Go to Compute - Instances and select the new image.

![](images/070.png)

- Note the public IP to access with VNC (port 5901).

![](images/071.png)

### **VNC to the Marketplace Image and Launch ODI from the Desktop**

- Connect using VNC with your VNC client using your public IP and VNC password you entered above.  Then double click in the ODI Studio icon on the desktop.  Note it will initally just look like a text document.

![](images/072.png)

- Connect to your repository using the password you entered when you deployed the image.

![](images/073.png)

![](images/074.png)

### **Topology - Create Connections to Object Storage, BICC, and ADW**

- Upload your private pem key file to your odi marketplace image.  In this case I SCP'd it to /home/opc/private.pem location.

- Create a new auth token for your local user (api3_user).  Copy the token.

![](images/077.png)

![](images/078.png)

- Under the Topology tab navigate down to Object Storage and right click and create New Data Server.

![](images/075.png)

- Enter the following details.  Note the pem key location from the previous step, and the Cloud API local username, and the token you copied from above.

![](images/076.png)

- Save first and then test the connection.

![](images/079.png)

![](images/080.png)

- Right click on the data server and create a new physical schema.

![](images/081.png)

- Enter the details (this assumes your bucket name is `dgc_odi_bucket`).

![](images/082.png)

- You'll need to specify a context.

![](images/083.png)

- Click on Context to the left and then add a new context with a logical schema name.

![](images/084.png)

- Next create a new data server in the `Oracle BI Cloud Connector Technology - right click to create.  Enter the following details.  You will need your ERP Cloud URL, your ERP Cloud userid and password, and then select the Object Storage Configuration.  Note the External Storage Name is your BICC External Connection Name.

![](images/085.png)

- Test your connection.

![](images/086.png)

![](images/087.png)

- Right click on your new data server and create a new physical schema.

![](images/093.png)

- Then select the schema from ERP Cloud that has the View Objects (VOs) you want to replicate.  Save your work.

![](images/094.png)

- Upload your ADW wallet to the ODI Marketplace Image (not shown here).

- Navigate to Oracle Technology and create a new Data Server (right click).  Enter a name, the admin userid and password on ADW, and you should be able to select your Credential File (the wallet).  In my case I entered details in the jdbc connection manually by opening the wallet zip file and copying the entry from the tnsnames.ora file.  If you chose this method you also need to enter two keys, one with the location of wallet (in my case /tmp/wallet).

![](images/088.png)

![](images/089.png)

- Save your work and test connection.

![](images/090.png)

![](images/091.png)

### **Designer - Create Models, a Project and a Mapping (source to target):**

- Navigate to Designer and Create a new model.

![](images/092.png)

- Enter details.

![](images/095.png)

- Select Reverse Engineer and consider filtering (there are a large number of objects).  In my case I am interested in COGSDistributionsPVO.  Be sure to set your `LIST_OF_OFFERINGS`.

![](images/096.png)

- You should see the objects you selected available in your model.

![](images/097.png)

- Select Create new model and topology objects.

![](images/098.png)

![](images/099.png)

- Save your new model.

![](images/100.png)

- Create new diagram for bicc_adm model (target).  Right click on Diagrams.

![](images/101.png)

- Drag the objects (one by one) from the bicc_fscm model (source) to your target.  This will simplify setup.

![](images/102.png)

- Then save.

![](images/103.png)

- Create a new project 

![](images/104.png)

- Patch ODI with the BICC RKM.  Call it bicc.  Upload and copy the `KM_RKM Oracle BI Cloud Connector.xml` file to `\home\oracle\odi\sdk\xml-reference` directory.

- Expand the `Knowledge Modules` section and right click on the `Reverse-Engineering (RKM) module` to import the RKM.

![](images/105.png)

![](images/106.png)

- Search for the BI Cloud Connector RKM and select it.

![](images/107.png)

![](images/108.png)

- Right click on the object in the target model that you with to replicate and select `Generate Mappings IN`.

![](images/109.png)

- Set Generation Folder.

![](images/110.png)

- Select the Physical Tab.

![](images/111.png)

- Select the target object and set the `CREATE_TARGET_TABLE` to True.

![](images/112.png)

- Select the staging object and set the `SUBMIT_BICC_JOB` and `CREATE_CREDENTIAL` variables to True.

![](images/113.png)

- Save your work.

![](images/114.png)

### **Run the Mapping**

- Run the mapping.  This will take several minutes, and will first run a BICC job that creates objects in Object Storage, then will create your external table in ADW and your physical table in ADW, and the the credential required to copy the data from Object Storage.

![](images/115.png)

![](images/116.png)