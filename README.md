# Options for Extracting Data Out of ERP Cloud and Optionally Loading into Oracle Analytics Cloud

Derrick Cameron
Oct 30, 2019

The extraction of data from ERP cloud involves a few steps, and for each step there are some choices.  However the initial extract can only be done either using Integration Cloud Service (ICS) or Oracle Transactional Business Intelligence (OTBI) View Objects.  All options are noted below for completeness, and to address possible questions on these options.

### **High level approach**

- Use ICS (bulk extract possible but not recommended)
- Use OTBI with
    - OTBI Reports: Limited data volume, not recommended
    - or BI Cloud Connector (BICC) with:
        - OAC Data Flows: Simplest process to load and access data in OAC, limited data volume and not recommended for larger extracts
        - or BICC with Datasync: Supports larger data sets, but Datasync is not supported.
        - or BICC with Oracle Data Integrator (ODI) or some other ETL or scripted tool: This is the recommended approach for larger scale ETL

### **ICS (not recommended)**

ICS is generally a transaction integration platform, and while ICS supports bulk transaction extracts, this is not recommended for large data sets.  ICS can chunk data, but the technology is better suited to real time transaction integration.  Therefore this option will not be detailed in this document.

### **OTBI Options**

OTBI is a BI platform on top of ERP Cloud.  It consists of a metadata layer (binary formatted rpd file) where View Objects representing subject area content are used to access the underlying data, and a web query front end for direct access to transactional data.  View Objects are not documented so the end user must search using key words.  Regardless whether you use the OTBI web dashboards/reporting tool or other options (noted below), these all use View Objects in OTBI.

#### **OTBI Reports:**

- Extract data using an analysis created in Answers - 65k row limit.
- Extract based on the SQL from the 'Advanced' tab of an Analyses Report.  There is no 65k row limit and no dependency on an answers report, but does require you to create sql queries.
- Extract based on a Folder from a Subject Area from the /analytics portal.  No 65k row limit and provides allows mappings for multiple Subject Area folders to be created in one step, but is limited to the folder.
- Extract using a BI Publisher report.

#### **BICC:**

https://www.ateam-oracle.com/using-data-sync-to-load-data-from-fusionsaas-using-bicc-and-ucm
https://www.ateam-oracle.com/set-up-oracle-fusion-saas-business-intelligence-cloud-connector-bicc-to-use-oracle-cloud-infrastructure-oci-object-storage

- Extract to UCM (now called Oracle Webcenter Content Server), Cloud Storage Service (Classic), or Object Storage
- Steps
    - Add offering (what is an offering)?
    - Add a datastore for an offering (what is a datastore)?  datastore = VO?  Can you browse VOs?
    - Configure where to load data (UCM, Cloud Storage Service, or Object Storage).  Creates zip file with 
        - Metadata comma-separated value (.mdcsv) files: metadata files with details about columns and data type definitions for Flex VOs.
        - Comma-separated value (.csv) files: VO data and are uploaded as compressed files.
        - Primary Key comma-separated value (.pecsv) files: data files with primary key column values used to identify deleted records in the warehouse.
- Next Steps:
    - Download files through the UI (File Import\Export UI or Traditional UCM UI).  See https://blogs.oracle.com/fusionhcmcoe/oracle-hcm-cloud-introduction-to-ucm-webcenter-content-server.
    - You may also use web services to automate the file transfer to\from UCM. It supports both RIDC and Generic Soap Port. We recommend using Generic SOAP Port service for UCM automation.
        - Option1: Command line- You can use the WebCenter Content Document Transfer Utility via command line\shell\batch scripts to automate the file transfer.
        - Option2: Java Programs- You can use the Webcenter Content Document Transfer utility and invoke it via java or use the API wrapper.
    - Datasync to pull data from UCM.
    - ODI to pull data from UCM or Object Storage.
    - Use some other tool to pull data from storage (or scripts).

#### **DataSync:**

https://www.ateam-oracle.com/using-bics-data-sync-to-extract-data-from-oracle-otbi-either-cloud-or-on-premise

- Configured with OTBI or BICC
- Can also extract from any JDBC Source/database, including EBS and other Oracle and non-Oracle databases (general and basic ETL tool).

#### **ODI:**

 https://www.ateam-oracle.com/integrating-oracle-data-integrator-odi-on-premise-with-cloud-services
 https://docs.oracle.com/en/middleware/fusion-middleware/data-integrator/12.2.1.3/odikm/oracle-object-storage.html#GUID-DFE3EBF0-0A0D-4BA0-94FE-202185E47804
 
- ODI supports both RESTful and SOAP web services; thus, ODI users can extract data from any SaaS applications that provide RESTful or SOAP web services.
- Alternatively, ODI users may use a JDBC driver to establish a connection, between ODI and the SaaS application, and extract data from the SaaS application.  The JDBC driver is a wrapper that uses the underlying web service APIs to facilitate the connection and consumption of data between the client (ODI) and the web service (SaaS application).

#### **OAC Dataflow:**

https://www.ateam-oracle.com/saas-data-replication-in-oracle-analytics-cloud-oac-and-oaac

- The user must also initially log into the Fusion SaaS BI Cloud Connector console and set up the Cloud Storage Service that will be used.
- Steps:
    - Create source
    - Create Replication Connection target
    - Create a Data Replication
        - Select full or incremental replication
        - Option to include deletions
        - Run now or schedule

### **Summary of Options:**

Option Title: Datasync

Option Description: This uses Datasync to

Components

Configuration Steps

Extraction Steps

Load Options (full/incremental, etc.)

ETL (transformation) support

Scripting Support

Scheduling Support

Limitations

Pros

Cons

Performance Considerations

References 