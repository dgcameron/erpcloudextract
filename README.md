# Options for Extracting Data Out of ERP Cloud and Optionally Loading into Oracle Analytics Cloud

Derrick Cameron
Oct 30, 2019

## **DRAFT!**

This provides an overview of the options available to extract data from ERP Cloud for loading into a database or analytic platform such as OAC.  The extraction of data from ERP cloud involves a few steps, and for each step there are some choices.  The initial extract can be done one of the following four ways, although some of these options are not recommended, but included for completeness. 

### **Options in Recommended Order (Depends Partly on Use Case):**

- [BI Cloud Connector (BICC)](bicc_data_replication.md):
  - No data volume limits
  - Loads data to object storage.  From there you have choices to load into targets:
    - Oracle Data Integrator (ODI).  ODI can manage the BICC source and dbcs/adw targets, and orchestrate the movement of data from BICC to Object Storage and from there to your target.  This is the recommended tool.
    - As an alternative to using ODI you can also replicate (via BICC) data from BICC to Object Storage and from there to your target.  Data Sync is not supported
    - Yet another alternative is to move data from Object Storage to DBCS or ADW via scripted methods (OCI API tool with External Tables).
- [Oracle Analytics Cloud (OAC) Data Replication](oac_data_replication.md):
    - Simplest overall process if the objective is to use OAC against a DBCS or ADW target.
    - Has some data volume limits.
- Oracle Integration Cloud (OIC):
  - Bulk extract possible with limitations, but not recommended.
- [ERP Cloud REST APIs](restapip_data_replication.md): 
  - Has data volume limits.
- [OTBI Reports](otbi_data_replication.md):
  - Has data volume limits.
  - Export data to csv files on your desktop, after which you need to load into your database target.

### **OIC (not recommended)**

OIC connects your applications, on-premise with Cloud (Integration Cloud Service) and helps in design, monitor, and manage connections between your applications. Automate & manage business Process (Process Cloud Service) Build Applications Visually (Visual Builder Cloud Service).  ICS is generally a transaction integration platform, and while ICS supports bulk transaction extracts, this is not recommended for large data sets.  OIC can chunk data, but the technology is better suited to real time transaction integration.  Therefore this option will not be detailed in this document.

### **ADF View Objects**

[Introduction to View Objects for Oracle Business Intelligence Applications](https://docs.oracle.com/cd/E25054_01/fusionapps.1111/e15524/adv_bi_vos.htm)

View Objects are the base logical business layer on top of the physical database tables.  Since there is no direct access to ERP Cloud database tables access is through these views.  If customizations are made to the applications developers need to expose custom fields through the View Objects.  OTBI is a BI platform on top of the View Objects in ERP Cloud.  It consists of a metadata layer (binary formatted rpd file) where View Objects representing subject area content are used to access the underlying data, and a web query front end for direct access to transactional data.  View Objects are not documented so the end user must search using key words.  Regardless whether you use the OTBI web dashboards/reporting tool or other options (noted below), these all use View Objects.

#### **BICC:**

[Using Data Sync to Extract and Load Data from Fusion SaaS using the BI Cloud Connector (BICC) and UCM](https://www.ateam-oracle.com/using-data-sync-to-load-data-from-fusionsaas-using-bicc-and-ucm)
[Set up Oracle Fusion SaaS Business Intelligence Cloud Connector (BICC) to use Oracle Cloud Infrastructure (OCI) Object Storage](https://www.ateam-oracle.com/set-up-oracle-fusion-saas-business-intelligence-cloud-connector-bicc-to-use-oracle-cloud-infrastructure-oci-object-storage)

- Extract to:
    - UCM (now called Oracle Webcenter Content Server).
    - or Cloud Storage Service (Classic - not recommended and will not be discussed further in these notes).
    - or OCI Object Storage ***(recommended)***.
- Steps
    - Add offering
    - Add a datastore for an offering (View Object).
    - Configure target storage (UCM, Cloud Storage Service, or Object Storage).  BICC extract job creates a zip file with: 
        - Metadata comma-separated value (.mdcsv) files: metadata files with details about columns and data type definitions for Flex VOs.
        - Comma-separated value (.csv) files: VO data and are uploaded as compressed files.
        - Primary Key comma-separated value (.pecsv) files: data files with primary key column values used to identify deleted records in the warehouse.
- Next Steps:
    - UCM:  
        - Download files through the UI (File Import\Export UI or Traditional UCM UI).  See https://blogs.oracle.com/fusionhcmcoe/oracle-hcm-cloud-introduction-to-ucm-webcenter-content-server.
        - Datasync: Option to pull data from UCM and load to any Oracle or non-Oracle database.  See below for more details.
        - You may also use web services to automate the file transfer to\from UCM. It supports both RIDC and Generic Soap Port. We recommend using Generic SOAP Port service for UCM automation.
            - Option1: Command line- You can use the WebCenter Content Document Transfer Utility via command line\shell\batch scripts to automate the file transfer.
            - Option2: Java Programs- You can use the Webcenter Content Document Transfer utility and invoke it via java or use the API wrapper.
    - OCI Object Storage ***(recommended)***:  
        - Download available through the cloud console, or use the OCI API.
        - ADW target: ***(recommended)***
            - Create an external table that points to the csv file(s) in object storage (directly queryable).
            - Optionally create standard table in ADW and load/copy data from the external object store to the ADW table.
        - DBCS target:
            - Copy the export file from Object Storage to the DBCS O/S by either downloading from object storage and uploading to DBCS, or installing the OCI CLI on DBCS and directly copying the data file.
            - Create an external table in DBCS.
            - Optionally create standard table in DBCS and load/copy data from the external object store to the ADW table.
    - ODI: ***(recommended)*** 
        - ETL platform that can pull data from UCM or Object Storage and load into an Oracle or non-Oracle database.  See below for more details.
    - Use some other tool to pull data from storage (or scripts).
