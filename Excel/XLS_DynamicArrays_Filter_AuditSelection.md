# Using the Dynamic Array Filter Function to automatically select audit transactions

 

Hello my auditing friends, this is a quick post to show how to use Excel Dynamic Arrays to help take the pain out of selecting transactions to audit.

 

To demonstrate, I will use an export of a QuickBooks Online report for the Repairs and Maintenance account. 

Could there be some uncapitalized expenses in there? Never - right?

 

 

## Here is the report . . .



![The Exported Report](https://images.processinsightfuture.com/XLS_DynamicArray_Filter_AuditSelection_export_web.png)





## Here is the formula to show all transaction greater than $1,000

 

(In pseudo-code)
``` Excel
=FILTER( entire_range_to_return, range_to_filter[=,>,<,<> . .][value])
```


(in this example)
``` Excel
=FILTER(Export!$A:$J,Export!$I:$I>1000)
```

** You only type the formula into the first cell, Excel "spills" as needed to adjacent cells. Welcome to Dynamic Arrays!!



![Formula in Action](https://images.processinsightfuture.com/XLS_DynamicArray_Filter_AuditSelection_Formula_web.png)



### Dig in

# Dig in . . .

 

Ok, that is just the start. To dig in check out the articles at ExcelJet and start dreaming.

https://exceljet.net/dynamic-array-formulas-in-excel





