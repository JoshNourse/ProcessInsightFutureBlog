<---
Title: Power Query Calendar Date Table Template
Author: Josh Nourse
Tags: [Power Query, Templates, Date Table]
Excerpt: "Reference guide to creating a reusable calendar table in power query. Includes the code that can be copy and pasted to your editor"
FeatureImage: https://images.processinsightfuture.com/PnP_PowerQuery_Calendar_AppliedSteps_Fullsize.png
CSS: 
SEO: 

--->



# Power Query Calendar / Date Table Template

###### High level design and overview of the Calendar / Date table and how to adjust or optimize for a specific report.





###### Can I summarize the entire reference in one image

\** Probably not but I can try

![Create a new blank query, use advanced editor to copy in code, enjoy](https://images.ProcessInsightFuture.com/PnP_PowerQuery_Calendar_Singular_FullSize.png)



<TOC>

### Reference Overview:

Purpose: 

To understand and maintain the date tables implemented in your model. A reference to the concepts used, not necessarily how to create from scratch. 

  If you are interested in the details of creating from scratch, you could use this reference in conjunction with with the several articles I relied on heavily to create this. 

 ...I should probably give due credit anyways:

- [Exceleratorbil.com.au Build a Reusable Calendar Table with Power Query](https://exceleratorbi.com.au/build-reusable-calendar-table-power-query/)

- [Radacad.com Create a Date Dimension in Power BI in 4 Steps](https://radacad.com/create-a-date-dimension-in-power-bi-in-4-steps-step-1-calendar-columns)

-  [eHansalytics.com Create A Dynamic Date Table in Power Query](https://www.ehansalytics.com/blog/2019/3/17/create-a-dynamic-date-table-in-power-query)

Additionally: a related note on performance

- Understanding Resources on Queried Parameters: [Radacad.com Performance Tip for Power BI; Enable Load Sucks Memory Up](https://radacad.com/performance-tip-for-power-bi-enable-load-sucks-memory-up)





### Customize, Personalize, & Optimize:



- **Change "Start Date USE ME":  **
  - This is a pointer to one of the pre-configured start date parameters
  - [DEFUALT] = "Start Date Fixed"
    - i.e. If data will only include prior and current year records to compare: Use the "Start Last Year"
  - Update the value for "Start Date Fixed": Just click on the step and type new date.
  - Or repoint to an alternate parameter
- **Change the "End Date USE ME"**
  - Same as changing the start date, but using the End Date Parameters
  - [DEFUALT] = "End Next Year"
- **Create dynamic parameters** for start and end dates that changes based upon your data fact table.  Then just update the "USE ME" with your new parameter names. See:  [eHansalytics.com Create A Dynamic Date Table in Power Query](https://www.ehansalytics.com/blog/2019/3/17/create-a-dynamic-date-table-in-power-query)
- **Update First day of Week:** 
  - [DEFUALT]= Set to Sunday
  - Explicit arguments have been used if applicable even though the default is Sunday
  - Just Find and Replace "Date.Sunday" with "Date.[DayName]" in the code below
- **Update to Local Names**
  - [DEFAULT] = "en-US"
  - Explicit arguments have been used if applicable
  - Just find and replace "en-US" with "[YourLocal]"
- Set Fiscal Year
  - TBD



### Cut to the Chase, give me the code:

```Power Query M
let
    Source = List.Dates(#"Start Date USE ME", DateRangeDays, #duration(1,0,0,0)),
    Today = DateTime.Date(DateTime.LocalNow()),
    #"Start Date Fixed" = #date(2015, 1, 1),
    #"End Date FIxed" = #date(2021, 12, 31),
    #"Start Current Year" = Date.StartOfYear(Today),
    #"Start Last Year" = Date.StartOfYear(Date.AddYears(Today, -1)),
    #"End Current Year" = Date.EndOfYear(Today),
    #"End Next Year" = Date.EndOfYear(Date.AddYears(Today, 1)),
    #"Start Date USE ME" = #"Start Date Fixed",
    #"End Date USE ME" = #"End Next Year",
    DateRangeDays = Duration.Days(#"End Date USE ME" - #"Start Date USE ME")+1,
    #"Converted to Table" = Table.FromList(#"Source", Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "Date"}}),
    #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"Date", type date}}),
    #"Inserted Date ISO" = Table.AddColumn(#"Changed Type", "Date ISO", each Date.ToText([Date], "yyyy-MM-dd"), type text),
    #"Inserted YYMM" = Table.AddColumn(#"Inserted Date ISO", "YYMM", each Date.ToText([Date], "yyMM"), Int64.Type),
    #"Inserted YYYY-MM" = Table.AddColumn(#"Inserted YYMM", "YYYY-MM", each Date.ToText([Date], "yyy-MM"), type text),
    #"Inserted MMM-YY" = Table.AddColumn(#"Inserted YYYY-MM", "MMM-YY", each Date.ToText([Date], "MMM-yy"), type text),
    #"Inserted Month Name" = Table.AddColumn(#"Inserted MMM-YY", "Month Name", each Date.MonthName([Date], "en-US"), type text),
    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Month Name", "Week of Month", each Date.WeekOfMonth([Date], Day.Sunday), Int64.Type),
    #"Inserted Day of Week" = Table.AddColumn(#"Inserted Week of Month", "Day of Week", each Date.DayOfWeek([Date], Day.Sunday)+1, Int64.Type),
    #"Inserted Day of Week Name" = Table.AddColumn(#"Inserted Day of Week", "Day of Week Name", each Date.DayOfWeekName([Date], "en-US"), type text),
    #"Inserted Day" = Table.AddColumn(#"Inserted Day of Week Name", "Day", each Date.Day([Date]), Int64.Type),
    #"Inserted Calendar Year" = Table.AddColumn(#"Inserted Day", "Year", each Date.Year([Date]), Int64.Type),
    #"Inserted Calendar Quarter" = Table.AddColumn(#"Inserted Calendar Year", "Quarter", each Date.QuarterOfYear([Date]), Int64.Type),
    #"Inserted Calendar Month" = Table.AddColumn(#"Inserted Calendar Quarter", "Month", each Date.Month([Date]), Int64.Type),
    #"Inserted Calendar Week of Year" = Table.AddColumn(#"Inserted Calendar Month", "Week of Year", each Date.WeekOfYear([Date], Day.Sunday), Int64.Type),
    #"Inserted Calendar Day of Year" = Table.AddColumn(#"Inserted Calendar Week of Year", "Day of Year", each Date.DayOfYear([Date]), Int64.Type)
in
    #"Inserted Calendar Day of Year"
```



###### Expression Reference



![Applied Steps Broken Out](https://images.processinsightfuture.com/PnP_PowerQuery_Calendar_AppliedSteps_Fullsize.png)



| Applied Step           | Data Type  | Expression                                                   | Docs                                                         |
| ---------------------- | ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
|                        |            | ##### Generate  List of Date                                 |                                                              |
| Source                 | Int64.Type | =  List.Dates(#"Start Date USE ME", DateRangeDays, #duration(1,0,0,0)) | [MS Docs:   List.Dates](https://docs.microsoft.com/en-us/powerquery-m/list-dates) |
| Today                  | Parameter  | =  DateTime.Date(DateTime.LocalNow())                        | [MS   Docs: DateTime.LocalNow](https://docs.microsoft.com/en-us/powerquery-m/datetime-localnow)  [MS Docs:   DateTime.Date](https://docs.microsoft.com/en-us/powerquery-m/datetime-date) |
| Start Date Fixed       | Parameter  | 1/1/2015                                                     |                                                              |
| End Date Fixed         | Parameter  | 12/31/2020                                                   |                                                              |
| Start Current Year     | Parameter  | =  Date.StartOfYear(Today)                                   | [MS   Docs: Date.StartOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-startofyear) |
| Start Last Year        | Parameter  | =  Date.StartOfYear(Date.AddYears(Today, -1))                | [MS   Docs: Date.StartOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-startofyear)  [MS Docs:   Date.AddYears](https://docs.microsoft.com/en-us/powerquery-m/date-addyears) |
| End Current Year       | Parameter  | =  Date.EndOfYear(Today)                                     | [MS Docs:   Date.EndOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-endofyear) |
| End Next Year          | Parameter  | =  Date.EndOfYear(Date.AddYears(Today, 1))                   | [MS Docs:   Date.EndOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-endofyear)  [MS Docs:   Date.AddYears](https://docs.microsoft.com/en-us/powerquery-m/date-addyears) |
| Start Date USE ME      | Pointer    | [DEFAULT] =  #"Start Date Fixed"                             |                                                              |
| End Date USE ME        | Pointer    | [DEFAULT] =  #"End Next Year"                                |                                                              |
| DateRangeDays          | Parameter  | =  Duration.Days(#"End Date USE ME" - #"Start Date USE  ME")+1 | [MS Doscs:   Duration.Days](https://docs.microsoft.com/en-us/powerquery-m/duration-days) |
|                        |            | ###### Turn the  List into a table                           |                                                              |
| Convert To Table       | Transform  | =  Table.FromList(#"Source", Splitter.SplitByNothing(), null, null,  ExtraValues.Error) | [MS Docs:   Table.FromList](https://docs.microsoft.com/en-us/powerquery-m/table-fromlist)  ** Generated by  Power Query Editor |
| Renamed Columns        | Transform  | =  Table.RenameColumns(#"Converted to Table",{{"Column1",  "Date"}}) | [MS   Docs: Table.RenameColumns](https://docs.microsoft.com/en-us/powerquery-m/table-renamecolumns)  ** Generated by  Power Query Editor |
| Changed Type           | Date       | =  Table.TransformColumnTypes(#"Renamed Columns",{{"Date",  type date}}) | [MS   Docs: TransformColumnTypes](https://docs.microsoft.com/en-us/powerquery-m/table-transformcolumntypes)  ** Generated by  Power Query Editor |
|                        |            |                                                              |                                                              |
|                        |            | ##### Explode the  date Columns                              |                                                              |
|                        |            | ###### Fiscal Period Agnostic                                |                                                              |
| Date ISO               | type text  | =  Table.AddColumn(#"Changed Type", "Date ISO", each  Date.ToText([Date], "yyyy-MM-dd"), type text) |                                                              |
| YYMM                   | Int64.Type | Date.ToText([Date],  "yyMM"                                  | [MS Docs:   Date.ToText](https://docs.microsoft.com/en-us/powerquery-m/date-totext) |
| YYYY-MM                | text       | Date.ToText([Date],  "yyy-MM")                               | [MS Docs:   Date.ToText](https://docs.microsoft.com/en-us/powerquery-m/date-totext) |
| MMM-YY                 | text       | Date.ToText([Date],  "MMM-yy")                               | [MS Docs:   Date.ToText](https://docs.microsoft.com/en-us/powerquery-m/date-totext) |
| Month Name             | type text  | Date.MonthName([Date],  "en-US")                             | [MS Docs:   Date.MonthName](https://docs.microsoft.com/en-us/powerquery-m/date-monthname) |
| Week of Month          | Int64.Type | Date.WeekOfMonth([Date],  Day.Sunday)                        | [MS   Docs: Date.WeekOfMonth](https://docs.microsoft.com/en-us/powerquery-m/date-weekofmonth) |
| Day of Week            | Int64.Type | Date.DayofWeek([Date],  Day.Sunday)+1  Sunday  is always 0+1, first day of week | [MS Docs:   Date.DayOfWeek](https://docs.microsoft.com/en-us/powerquery-m/date-dayofweek) |
| Day of Week Name       | type text  | Date.DayofWeekName([Date],  "en-US")                         | [MS   Docs: Date.DayOfWeekName](https://docs.microsoft.com/en-us/powerquery-m/date-dayofweekname) |
| Day                    | Int64.Type | Date.Day([Date])                                             | [MS Docs:   Date.Day](https://docs.microsoft.com/en-us/powerquery-m/date-day) |
|                        |            | ###### Calendar  Year                                        |                                                              |
| Calendar Year          | Int64.Type | Date.Year([Date])                                            | [MS Docs:   Date.Year](https://docs.microsoft.com/en-us/powerquery-m/date-year) |
| Calendar Quarter       | Int64.Type | Date.QuarterofYear([Date])                                   | [MS   Docs: Date.QuarterOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-quarterofyear) |
| Calendar Month         | Int64.Type | Date.Month([Date])                                           | [MS Docs:   Date.Month](https://docs.microsoft.com/en-us/powerquery-m/date-month) |
| Calendar Week of  Year | Int64.Type | Date.WeekOfYear([Date],  Day.Sunday)                         | [MS Docs:   Date.WeekOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-weekofyear) |
| Calendar Day of  Year  | Int64.Type | Date.DayOfYear([Date])                                       | [MS Docs:   Date.DayOfYear](https://docs.microsoft.com/en-us/powerquery-m/date-dayofyear) |
|                        |            | ###### Fiscal Year                                           |                                                              |







### Concept, Theory and Some Design Decisions

#### Create a list of dates

 

The first step in creating a date table is creating a list of dates within a range. Sounds simple enough right?

 **How big (what range) should the calendar table be? **

The range needs to include at least the entire range of your data but will normally contain some period of time before or after (such as though the end of the year). However, if the range includes a lot of unnecessary dates in the past or future, it will hurt memory and performance. The template is flexible you can easily change the date range to suite the report being generated. 

  

##### Generating a start and end date for the list (oh the possibilities!):

  

###### What's in this template?

 

- The template has a default  date range of 01/01/2015 - 12/31/[Next Year] ("Start Date Fixed" - "End Next Year"). This includes at least 5 years of historical information and forecast/budget for at least one year. This is probably larger than needed for most reports but should work out of the box without adjusting.  This can be narrowed down after the report has been created.
- Template is preconfigures to easily adjust with the following parameters, To update you only need to adjust the pointers in the two "USE ME" steps

| Parameter          | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
|                    | **Change these to  customize**                               |
| Start Date USE ME  | Just a pointer to  one of the values below to make it easier to update |
| End Date USE ME    | Just a pointer to  one of the values below to make it easier to update |
|                    | **Static Values**                                            |
| Start Date Fixed   | Specify a static  start date that does not change: Default: 01/01/2015 |
| End Date Fixed     | Specify a static  start date that does not change: Default: 12/31/2021 |
|                    | **Today() Values**                                           |
| Start Current Year | Automatically  start from 01/01/[current year]               |
| Start Last Year    | Automatically  start from 01/01/[prior year]                 |
| End Current Year   | Automatically end  on 12/31/[current year]                   |
| End Next Year      | Automatically end  on 12/31/[following year]                 |
|                    | **Dynamic Values**                                           |
| Start Dynamic      | [Would be an  External parameter] ** See note                |
| End Dynamic        | [Would be an  External parameter ]** See note                |

 

  

###### Digging Deeper

 

From the list of pre-configured options above, there are many ways to generate the range of start and end dates. Lets break them downby type.

Static:  Explicitly state the date.  They are useful when generating a small compact model with limited data for development and testing. Or if the minimum date available in the data source is know (like the last software migration). However, these will probably need periodically updated to keep the report working, so maybe not the best option for production.

Today:  Based upon a period around today; or rather a period before or after today. The date table can adapt to a point in time and maintain itself based upon the data it expects in relation to today. 

 i.e. if the data is filtered to compare prior and current year sales, it would need a date table from January of the year prior through the end of the current year. This can all be expressed as a calculation from today using the "Start Last Year" - "End Current Year" parameters.

 

Dynamic:  Use the data returned in the report to automatically set date range.

Query the fact table, find the first and last dates in specified columns and create a list of dates between them. Honestly a great way to get the most compact and efficient date table. For the application of a generalized template on model where the fact tables may change and perspective could transition from past to future it is difficult to implement at this stage. Once a report model is generated and the performance of the date table needs tuned, this method could be implemented more effectively. ** Note: the parameters to generate these dynamic start and end dates are defined outside of this script but once defined can be substituted into the "USE ME"parameters.

 Here is a great write up on how to implement this dynamic range via parameters that I was able to get working: https://www.ehansalytics.com/blog/2019/3/17/create-a-dynamic-date-table-in-power-query

 

**Mixed:  Which is what I actually decided to use for the template.** I know the data will not have dates prior to 2015 but I do what the table to grow dynamically into the future. I have chosen to use the "Static Start Date" and include all dates through the "End Next Year" to accommodate some short term forecasting. As a general purpose template calendar table this should hold up for most situations. 

 To update the range, just set the Pointers "Start Date USE ME" and "End Date USE ME" to deferent targets.

  

#### Explode the date table

 

After you have the list of dates squared away, exploding the date table columns is relatively easy. There are copious examples on the web of creating any date column imaginable both in code and using the editor GUI. I would start with the sites I have referenced above and become familiar with the [Microsoft Docs Date Functions](https://docs.microsoft.com/en-us/powerquery-m/date-functions). Probably the most important thing to get familiar with is the [Date.ToText()](https://docs.microsoft.com/en-us/powerquery-m/date-totext) function and becoming familiar with the different formatting strings.

 

 