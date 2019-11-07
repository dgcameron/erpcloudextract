# **Extract ERP Data using OTBI Reports**

### **Pre-requisites**

- You have an ERP Cloud instance account that has the required priviledges to access bicc and create connections and replications (see link above for details)
- You have created a target database (DBCS or ADW/ATP).
- You have provisioned a Oracle Analytics Cloud (OAC) Service.
- You have a local (not federated) cloud userid.
- The cloud userid has no more than one api key.  This is because you will need to create and add two additional public API keys from the new connections you will create below.  These connections do not allow you to use existing keys, and the cloud user accounts only allow a maximum of three API keys.
- You have created an OCI bucket in your tenancy.

### **Log into the ERP Cloud and navigate to OTBI**

- Log into ERP Cloud.

![](images/052.png)

- Navigate to Tools.  You can either use the menu in the upper left or the scroll icon on the right.

![](images/053.png)

- Select Data Visualizations.

![](images/054.png)

- Select Open Classic Home in the upper right menu.  This will take you to the OTBI rpd that points to the View Objects.

![](images/055.png)

- Select Catalog.  Note in this demo instance the current user does not have priviledges to create new reports, but permissions can be granted to create new reports on VOs that you wish to export.

![](images/056.png)

- Since the current user does not have any priviledges to export data this document will switch to another environment to show how the export would be done.  This is using the [SampleApp](http://sampleappv607biapp-wikibiappsb3a-k124bbx7.srv.ravcloud.com:9502/analytics/saw.dll?bieehome&startPage=1) hosted demo environment to do this (userid/pw weblogic / Admin123).  You can select any report.  I am selecting the Dashboard - Employee Expenses.

![](images/057.png)

- Select the export button.

![](images/058.png)

- We will export in csv format.

![](images/059.png)

- Review the contents.  Note the export limit will be 65,000 rows.

