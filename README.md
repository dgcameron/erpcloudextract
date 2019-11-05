# Options for Extracting Data Out of ERP Cloud and Optionally Loading into Oracle Analytics Cloud

Derrick Cameron
Oct 30, 2019

## **DRAFT!**

This provides an overview of the options available to extract data from ERP Cloud for loading into a database or analytic platform such as OAC.  The extraction of data from ERP cloud involves a few steps, and for each step there are some choices.  However the initial extract can only be done either using Integration Cloud Service (ICS) or ERP View Objects.  All options are noted below for completeness, and to address possible questions on these options.  There is Oracle documentation and several blogs on various approaches.  This is meant to cover all the various options and provide clarity on the tradeoffs between them rather that provide detail on any one approach.

Summary Comparison.  Click on the upper right for more details.

|Extract From ERP|Steps|Result|Limitations|Pricing-paygo|Pricing-Monthly Flex|
|---|---|---|---|---|---|
|Oracle Integration Cloud (OAC)|Define source and target Connections|Target table loaded|Must not exceed the maximum quantity of 5,000 Messages per Hour that You set when You create an instance for the Oracle Cloud Service|5k messages/hour|5k messages/hour|
|   |Define integration|   |   |Standard: $1.2097|Standard: $0.806467|
|   |   |   |   |Enterprise: $2.4194|Enterprise: $1.612933|
|   |   |   |   |BYOL 20k messages/hour|BYOL 20k messages/hour|
|   |   |   |   |Standard: $0.4839|Standard: $0.3226|
|   |   |   |   |Enterprise: $0.4839|Enterprise: $0.3226|
|   |   |   |   |  |   |
|   |   |   |   |  |   |
|   |   |   |   |  |   |