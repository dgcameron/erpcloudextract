# **Extract ERP Data using BI Publisher Reports**

### **Create BI Publisher Report**

- Log into BI Publisher.

![](images/117.png)

- Create new model.  Note - this step is not shown due to lack of priviledges for creating models.

![](images/118.png)

- Once you have a model that is select the data of interest, you can create a new BIP report.  Select create new report.

![](images/119.png)

- Select the data model you just created. Then take defaults.

![](images/120.png)

![](images/121.png)

![](images/122.png)

![](images/123.png)

- Drag fields from your model to the canvas.

![](images/124.png)

- Name and save your report.

![](images/125.png)

![](images/126.png)

![](images/127.png)

### **Run BI Publisher Report**

- Create new report job

![](images/128.png)

- Search for your new report.

![](images/129.png)

- Select output format.

![](images/130.png)

- Add content server (UCM) as a destination.

![](images/131.png)

- Select a destination folder.

![](images/132.png)

- Submit job.  Optionally you can schedule for later.

![](images/133.png)

![](images/134.png)

![](images/135.png)

- Log into UCM.  Note the URLs for ERP Cloud are as follows:
    - Main Apps home page: `<server>`
    - OTBI:  `<server>/analytics`
    - BIP: `<server>/xmlpserver`
    - UCM: `<server>/cs`

![](images/136.png)

![](images/137.png)

- Click on your exported file.  You can download it and review.

![](images/138.png)

![](images/139.png)

- There are some other options available.

![](images/140.png)

