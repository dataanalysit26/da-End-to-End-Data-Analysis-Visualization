### Data Extraction Query

After completing the cleaning and validation steps on the raw dataset, the final dataset was generated using a **Google Sheets QUERY** operation.

This query filters valid transactions, removes invalid records, and restructures the dataset before exporting it as a CSV file for further analysis.

```excel
=QUERY(Raw_Data!A1:I541910, 
  "SELECT G, A, E, B, C, D, F, H 
   WHERE I = TRUE 
   AND F > 0 
   AND NOT B MATCHES '^(post|d|c2|dot|m|bank charges|samples|amazonfee)$' 
   AND NOT C MATCHES '.*(adjust bad debt|bank charges|samples|amazonfee).*' 
   LABEL G 'CustomerID', A 'Invoice No', B 'Stock Code', C 'Description', D 'Quantity', F 'Unit Price', H 'Country'", 1)
```

This operation performs the following actions:

* Selects validated rows marked as **TRUE** from the cleaning process
* Filters transactions where **Unit Price > 0**
* Removes invalid stock codes using **regex filtering**
* Removes unwanted transaction descriptions
* Renames output columns for the final analytical dataset
* Produces the **clean dataset exported as CSV**
