# Options for Extracting Data Out of ERP Cloud and Optionally Loading into Oracle Analytics Cloud

Derrick Cameron
Oct 30, 2019

## **DRAFT!**

This provides an overview of the options available to extract data from ERP Cloud for loading into a database or analytic platform such as OAC.  The extraction of data from ERP cloud involves a few steps, and for each step there are some choices.  However the initial extract can only be done either using Integration Cloud Service (ICS) or Oracle Transactional Business Intelligence (OTBI) View Objects.  All options are noted below for completeness, and to address possible questions on these options.  There is Oracle documentation and several blogs on various approaches.  This is meant to cover all the various options and provide clarity on the tradeoffs between them rather that provide detail on any one approach.

### **High level approach**

- ICS (bulk extract possible with limitations, but not recommended)
- Application Development Framework (ADF) View Objects (VO):
    - Available as part of ERP Cloud
    - Custom VOs created by developers to extend applications and expose custom fields
    - OTBI RPD (metadata) uses OOTB VOs 
    - OTBI Reports: Limited data volume, not recommended
    - or BI Cloud Connector (BICC) with:
        - OAC Data Flows: Simplest process to load and access data in OAC, limited data volume and not recommended for larger extracts
        - or BICC with Datasync: Supports larger data sets, but Datasync is not supported.
        - or BICC with Oracle Data Integrator (ODI) or some other ETL or scripted tool: This is the recommended approach for larger scale ETL
- ERP Cloud REST APIs

### **ICS (not recommended)**

ICS connects your applications, on-premise with Cloud (Integration Cloud Service) and helps in design, monitor, and manage connections between your applications. Automate & manage business Process (Process Cloud Service) Build Applications Visually (Visual Builder Cloud Service).  ICS is generally a transaction integration platform, and while ICS supports bulk transaction extracts, this is not recommended for large data sets.  ICS can chunk data, but the technology is better suited to real time transaction integration.  Therefore this option will not be detailed in this document.

### **ADF View Objects**

https://docs.oracle.com/cd/E25054_01/fusionapps.1111/e15524/adv_bi_vos.htm

View Objects are the base logical business layer on top of the physical database tables.  Since there is no direct access to ERP Cloud database tables access is through these views.  If customizations are made to the applications developers need to expose custom fields through the View Objects.  OTBI is a BI platform on top of the View Objects in ERP Cloud.  It consists of a metadata layer (binary formatted rpd file) where View Objects representing subject area content are used to access the underlying data, and a web query front end for direct access to transactional data.  View Objects are not documented so the end user must search using key words.  Regardless whether you use the OTBI web dashboards/reporting tool or other options (noted below), these all use View Objects in OTBI.

#### **OTBI Reports:**

- Extract data using an analysis created in Answers - 65k row limit.
- Extract based on the SQL from the 'Advanced' tab of an Analyses Report.  There is no 65k row limit and no dependency on an answers report, but does require you to create sql queries.
- Extract based on a Folder from a Subject Area from the /analytics portal.  No 65k row limit and provides allows mappings for multiple Subject Area folders to be created in one step, but is limited to the folder.
- Extract using a BI Publisher report.

#### **BICC:**

https://www.ateam-oracle.com/using-data-sync-to-load-data-from-fusionsaas-using-bicc-and-ucm
https://www.ateam-oracle.com/set-up-oracle-fusion-saas-business-intelligence-cloud-connector-bicc-to-use-oracle-cloud-infrastructure-oci-object-storage

- Extract to UCM (now called Oracle Webcenter Content Server), Cloud Storage Service (Classic), or OCI Object Storage.  Note Oracle Storage Service Classic is not recommended and will not be discussed further in these notes.
- Steps
    - Add offering.
    - Add a datastore for an offering (View Object).
    - Configure where to load data (UCM, Cloud Storage Service, or Object Storage).  BICC extract job creates a zip file with: 
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
    - OCI Object Storage:  Download available through the cloud console, or use the OCI API.
    - ODI: ETL platform that can pull data from UCM or Object Storage and load into an Oracle or non-Oracle database.  See below for more details.
    - Use some other tool to pull data from storage (or scripts).

#### **DataSync:**

https://www.ateam-oracle.com/using-bics-data-sync-to-extract-data-from-oracle-otbi-either-cloud-or-on-premise

- Configured with OTBI or BICC
- Can also extract from any JDBC Source/database, including EBS and other Oracle and non-Oracle databases (general and basic ETL tool).
- This option is not supported but can be used.  It can be used to load full or incremental data sets, perform basic transformations, and does not have data volume limitations.

#### **ODI:**

https://www.ateam-oracle.com/integrating-oracle-data-integrator-odi-on-premise-with-cloud-services
https://docs.oracle.com/en/middleware/fusion-middleware/data-integrator/12.2.1.3/odikm/oracle-object-storage.html#GUID-DFE3EBF0-0A0D-4BA0-94FE-202185E47804

Oracle Data Integrator is a comprehensive data integration platform that covers all data integration requirements: from high-volume, high-performance batch loads, to event-driven, trickle-feed integration processes, to SOA-enabled data services.

- ODI supports both RESTful and SOAP web services; thus, ODI users can extract data from any SaaS applications that provide RESTful or SOAP web services.
- Alternatively, ODI users may use a JDBC driver to establish a connection, between ODI and the SaaS application, and extract data from the SaaS application.  The JDBC driver is a wrapper that uses the underlying web service APIs to facilitate the connection and consumption of data between the client (ODI) and the web service (SaaS application).

#### **OAC Dataflow:**

https://www.ateam-oracle.com/saas-data-replication-in-oracle-analytics-cloud-oac-and-oaac
https://www.oracle.com/webfolder/technetwork/tutorials/tutorial/cloud/r13/wn/common/releases/19C/19C-common-wn.htm#F9089
https://docs.oracle.com/en/cloud/paas/analytics-cloud/acsom/create-services-oracle-analytics-cloud.html#GUID-6FC19D21-E572-4A36-937C-A1136AD989EE

- The user must also initially log into the Fusion SaaS BI Cloud Connector console and set up the Cloud Storage Service that will be used.
- Steps:
    - Create source
    - Create Replication Connection target
    - Create a Data Replication
        - Select full or incremental replication
        - Option to include deletions
        - Run now or schedule with full or incremental replication
        - Various row and data limits apply for data sources and reports, and for DV, Business Intelligence, and Essbase Services.  For data sources row limits are 125k for 1 OCPU up to 2.2M for 16+ OCPU.  See the OCC link above for details (plan your service section).
- New in OAC, replication can source data from either BICC or OTBI VOs directly, and can target either OAC internal storage or a DBCS target.

### **ERP Cloud REST APIs**

[REST API for Oracle Financials Cloud](https://docs.oracle.com/en/cloud/saas/financials/19d/farfa/rest-endpoints.html)

Note: Custom fields created by developers are not supported.

REST APIs can retrieve content from the applications using 'GET' requests.  The response is a JSON payload.  There are many ways to invoke these requests, and this discussion will not cover these.  Generally this would require saving the data extract (JSON response), uploading to Object Storage (for ADW) or the database VM (for DBCS), and then importing the data.  Depending on whether your target is DBCS or ADW/ATP the steps are slightly different:

- [ADW](https://blogs.oracle.com/datawarehousing/loading-your-object-store-data-into-autonomous-database)
    - Upload to Object Storage
    - Create a new table to load the data into.  This is a JSON document store (BLOB). 
    - Optionally create a second standard table for validated JSON data (less processing, faster).
    - Execute DBMS_CLOUD.COPY_DATA to load the JSON data into the JSON table.
    - Optionally load the standard table from the JSON table.
- [DBCS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/adjsn/loading-external-json-data.html#GUID-52EFC452-5E65-4148-8070-1FA588A6E697)
    - Upload to the DBCS VM (scp).
    - Create a new external table that will be used to query the JSON document on the file system.
    - Create a second standard table for validated JSON data (less processing, faster).
    - Query the external table and insert (load) rows into the standard table.

Sample Extract Call:

```
curl -u	\<userid\>:\<password\> \\
-H "Content-Type:application/json" \
-H "Accept:application/json" \
-X GET https://<host>/fscmRestApi/resources/11.13.18.05/expenseReports>expensereports

cat expensereports

{
  "items" : [ {
    "MeasureCatalogForArchival" : null,
    "MostRecentDaysToKeep" : null,
    "ArchiveTimeHierarchy" : null,
    "ArchiveTimeLevel" : null,
    "BasePlanId" : null,
    "PlanName" : "GOP",
    "CreatedBy" : "SCM_IMPL",
    "CreationDate" : "2017-09-26T15:42:13.001+00:00",
    "CutoffDate" : "2022-03-28",
    "DeleteArchive" : null,
    "DemandPlanCompletionDate" : null,
    "DemandPlanStartDate" : null,
    "Description" : "Global Order Promising",
    "EnableForArchiving" : null,
    "EnableMultithreading" : null,
    "HasRun" : null,
    "LastUpdateDate" : "2019-04-15T20:39:11.626+00:00",
    "LastUpdatedBy" : "SCM_IMPL",
    "LoadedStatus" : 1,
    "PlanCompletionDate" : null,
    "PlanHorizonStartDate" : "2014-03-20",
    "PlanId" : -2,
    "SupplyPlanningMode" : 1,
    "Owner" : "SEED_DATA_FROM_APPLICATION",
    "PlanStartDate" : null,
    "PlanType" : 100,
    "PlanAccess" : 1,
    "SavedOption" : null,
    "PlanStatus" : null,
    "links" : [ {
      "rel" : "self",
      "href" : "https://adc3-zcyy-fa-ext.oracledemos.com:443/fscmRestApi/resources/11.13.18.05/supplyChainPlans/-2",
      "name" : "supplyChainPlans",
      "kind" : "item"
    }, {
      "rel" : "canonical",
      "href" : "https://adc3-zcyy-fa-ext.oracledemos.com:443/fscmRestApi/resources/11.13.18.05/supplyChainPlans/-2",
      "name" : "supplyChainPlans",
      "kind" : "item"
    }, {
      "rel" : "child",
      "href" : "https://adc3-zcyy-fa-ext.oracledemos.com:443/fscmRestApi/resources/11.13.18.05/supplyChainPlans/-2/child/PlanningTables",
      "name" : "PlanningTables",
      "kind" : "collection"
    }, {
      "rel" : "child",
      "href" : "https://adc3-zcyy-fa-ext.oracledemos.com:443/fscmRestApi/resources/11.13.18.05/supplyChainPlans/-2/child/Archives",
      "name" : "Archives",
      "kind" : "collection"
    }, {
      "rel" : "child",
      "href" : "https://adc3-zcyy-fa-ext.oracledemos.com:443/fscmRestApi/resources/11.13.18.05/supplyChainPlans/-2/child/Duplicates",
      "name" : "Duplicates",
      "kind" : "collection"
    } ]
  }
  ```