# Options for Extracting Data Out of ERP Cloud and Optionally Loading into Oracle Analytics Cloud

Derrick Cameron
Nov 19, 2019

[Here](Architecture_Options_for_Fusion_Data_Extraction.pptx) is a presentation that summarizes the options.  Note that this document is not fully integrated with the presentation and there may be some inconsistencies in the options noted below and the presentation.

This provides an overview of thKTe options available to extract data from ERP Cloud for loading into a database or analytic platform such as OAC.  The extraction of data from ERP cloud involves a few steps, and for each step there are some choices.  The initial extract can be done one of the following ways, although some of these options are not recommended but included for completeness. Links to more detail are in the options below, and you can also navigate directly to these by clicking on the icon in the upper left. 

### **Options in Recommended Order (Depends Partly on Use Case):**

- [**Oracle Data Integrator (ODI) with BI Cloud Connector (BICC)**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=odi) (recommended):
    - No data volume limits.
    - Oracle Data Integrator (ODI).  ODI can manage the BICC source and DBCS/ADW targets, and orchestrate the movement of data from BICC to Object Storage and from there to your target.  This is the recommended tool.
- [**BI Cloud Connector (BICC)**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=bicc):
    - No data volume limits.
    - BICC Loads data to Object Storage with scheduled jobs.  From there you have choices to load into targets (in addition to using ODI noted above):
        - Datasync:  As an alternative to using ODI you can also replicate (via BICC) data from BICC to Object Storage and from there to your target.  Data Sync is not supported
        - Scripting:  Yet another alternative is to move data from Object Storage to DBCS or ADW via scripted methods (OCI API tool with External Tables).
- [**BI Publisher**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=bip):
    - Should only be used if you require information that is not available in the View Objects in BICC (very little is not available).
- [**Oracle Analytics Cloud (OAC) Data Replication**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=oac):
    - Simplest overall process if the objective is to use OAC against a DBCS or ADW target.
    - Has some data volume limits.
- **Oracle Integration Cloud (OIC)**:
    - Bulk extract possible with limitations, but not recommended, and is not discussed in this document.
- [**ERP Cloud REST APIs**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=restapi): 
    - Has data volume limits.
- [**OTBI Reports**](https://dgcameron.github.io/erpcloudextract/?page=README.md&nav=open&lab=otbi):
    - Has data volume limits.
    - Export data to csv files on your desktop, after which you need to load into your database target.


### **Important Considerations**

- **OIC:**  OIC connects your applications, on-premise with Cloud (Integration Cloud Service) and helps in design, monitor, and manage connections between your applications. Automate & manage business Process (Process Cloud Service) Build Applications Visually (Visual Builder Cloud Service).  ICS is generally a transaction integration platform, and while ICS supports bulk transaction extracts, this is not recommended for large data sets.  OIC can chunk data, but the technology is better suited to real time transaction integration.  Therefore this option will not be detailed in this document.
- **Application customizations:**  Developers can customize ERP Cloud and may create new data fields.  These data fields must be exposed in custom [View Objects](https://docs.oracle.com/cd/E25054_01/fusionapps.1111/e15524/adv_bi_vos.htm).  These custom View Objects can be selected in BICC and OAC Data Replications, but are not available in the REST APIs or OTBI.
- **Costs:**  Different options use different services, and these may differ depending on your choices.  While not covered in this document this is something that must be estimated and factored into architecture decisions.
- **Support:**  Not all the options above are supported, and depending on the use case may not be recommended.  Unsupported options are noted in the option details.
- **Data Volume:**  Different options support different volume limits.  This is noted in the option details above.

### **Demo Recordings**

- [Replication with ODI](odi.mp4)
- [Replication with OAC](oac.mp4)
