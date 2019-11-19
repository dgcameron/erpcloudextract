# **ERP Cloud REST APIs**

[REST API for Oracle Financials Cloud](https://docs.oracle.com/en/cloud/saas/financials/19d/farfa/rest-endpoints.html)

***Note:*** Custom fields created by developers are not supported.

REST APIs can retrieve content from the applications using 'GET' requests.  The response is a JSON payload.  There are many ways to invoke these requests, and this discussion will not cover these.  Generally this would require saving the data extract (JSON response), uploading to Object Storage (for ADW) or the database VM (for DBCS), and then importing the data.  Depending on whether your target is DBCS or ADW/ATP the steps are slightly different.

After making your REST call to fetch the JSON formated data, you can load into a database target as follows:

- [**ADW**](https://blogs.oracle.com/datawarehousing/loading-your-object-store-data-into-autonomous-database)
    - Upload to Object Storage
    - Create a new table to load the data into.  This is a JSON document store (BLOB). 
    - Optionally create a second standard table for validated JSON data (less processing, faster).
    - Execute DBMS_CLOUD.COPY_DATA to load the JSON data into the JSON table.
    - Optionally load the standard table from the JSON table.
- [**DBCS**](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/adjsn/loading-external-json-data.html#GUID-52EFC452-5E65-4148-8070-1FA588A6E697)
    - Upload to the DBCS VM (scp).
    - Create a new external table that will be used to query the JSON document on the file system.
    - Create a second standard table for validated JSON data (less processing, faster).
    - Query the external table and insert (load) rows into the standard table.

Sample Extract Call:

```

curl -u	&lt;userid&gt;:&lt;password&gt; \
-H "Content-Type:application/json" \
-H "Accept:application/json" \
-X GET https://&lt;host&gt;/fscmRestApi/resources/11.13.18.05/expenseReports&gt;expensereports

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