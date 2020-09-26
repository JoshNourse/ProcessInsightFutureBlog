---
Title: Automating data exports from Dealer Information System / Keystone data mine
Author: Josh Nourse
Tags: [DIS, Dealer Information Systems, Keystone, Keystone Data Mine]
CSS: 
SEO: 
---



# Automating data exports from Dealer Information System / Keystone data mine

###### Use the Keystone data mine query options to enable automatic running and saving of queries.





### Procedure Overview:

Data Mines in Keystone can be configured to automatically export to Excel. For a report that is used frequently this setting will in effect open a Data Mine KDM file in an Excel Workbook. Just double click the KDM file and an view the data in a new workbook.

 Additionally, the query options can be configured to automatically save the workbook and close the data mine when complete. This is what allows the KDM file to be scheduled with the windows task scheduler to create an automated refresh of reporting data.

 Reports can be developed from these consistently refreshed data sets as a component of an automated reporting pipeline.  

 There are additional parameters that you can experiment with as well.



### Procedure Summary:

1. Open Keystone Data Mine file (.KDM file)
2. Query Menu - Options
   1. Setup Autorun options
   2. Setup Autosave options





### Notes and Gotcha

- Keystone must be open and logged in on the system.

- Automated exports are in legacy xls format.

- By not checking "append timestamp to filename," file will be written over with each run.
  - This is the desired behavior for updating reusable data sources
- Per software vendor: stay away from backup windows for automatic scheduling

¿¿¿¿ Unknowns ???

- Excel must be installed?







### Related and Resources



###### Related Procedures

| Procedure Link                                          | Description                                                  |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [Scheudling data mine with windows task scheduler](URL) | After the KDM has been configured correctly, schedule it to automatically run with the windows task scheduler. |
|                                                         |                                                              |



###### Permissions and Requirements

| Source   | Permission                 | Granted by        |
| -------- | -------------------------- | ----------------- |
| Keystone | Access to data mine module | Application Admin |
|          |                            |                   |





### Procedure Detail

1. Open Keystone Data Mine file (.KDM file)

2. Query Menu - Options

3. Setup Autorun options

4. 1. Check "Run      Query when query is opened"

   2. Check "Export to excel      when query is opened"

      ![Query Options Autorun](https://images.processinsightfuture.com/PnP_DealerInformationSystems_KeystoneDataMine_QueryOptions_AutoRun1.png)

5. Setup Autosave options

6. 1. Check      "Autosave Export File and Close Keystone Data Mine"

   2. Browse to the desired      directory and supply a file name

   3. 1. ![Query Options Autosave Export File and Close Keystone Data Mine](https://images.processinsightfuture.com/PnP_DealerInformationSystems_KeystoneDataMine_QueryOptions_AutoSave1.png)

 

 

For Reference: all Keystone Data Mine Query Options

 

![All Query options](https://images.processinsightfuture.com/PnP_DealerInformationSystems_KeystoneDataMine_QueryOptions_Raw.png)

