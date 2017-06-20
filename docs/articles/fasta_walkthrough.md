---
title: "Generating Reports and FASTA Files"
author: "Cale Basaraba"
output: 
  rmarkdown::html_vignette
vignette: >
  %\VignetteIndexEntry{Generating Reports and FASTA Files}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---


  
  
### Initializing EIDITH R package:

In order to do anything with the EIDITH R package, we need to first make sure to load the `eidith` library. For straightforward data manipulation we will also load the `dplyr` package:

```r
library(eidith)
library(dplyr)
```

  
  
### Using EIDITH R package to generate FASTA files:

The EIDITH R package has a built-in function for retrieving a summary of sequences ready for interpretation: `ed_tests_report()`. Running the default `ed_tests_report()` function will generate a table of all tests whose status is `"Result finalized, interpretation pending"`. We can retrieve tests with other statuses by using `ed_tests_report()` with the `status =` option. For example, if we are interested in retrieving a table of tests whose status was `"Under Sequence Review"` or `"Active testing ongoing"`, we can run:  

```r
ed_tests_report(status = c("Under Sequence Review", "Active testing ongoing"))
```

If some of these tests do not have sequence data entered in EIDITH, a warning will appear in the R console informing the user.


The `ed_tests_report()` function constructs a summary table that includes a sequence field, and this information can easily be exported to FASTA format using the function `ed_fasta()`. If we had assigned the result of our `ed_tests_report()` to a variable, such as:  


```r
report <- ed_tests_report()  
```

We could then create a FASTA file of this report by:  

```r
ed_fasta(report, "report.fasta")  
```

Alternatively, we could skip a step and call `ed_fasta(ed_tests_report(), "report.fasta")` directly.
  
  
### Manually Subset by Viral Family or Method:  

If we are interested in subsetting a report, we can easily do that by using `filter()`. For instance, if we wanted a report of only Enteroviruses, we could subset by running:


```r
report <- ed_tests_report()
entero_report <- filter(report, test_rq == "Enteroviruses")
```

The `entero_report` dataframe now only contains records for Enterovirus tests, and can be saved in FASTA format by:


```r
ed_fasta(entero_report, "entero.fasta")
```

We could subset by the value of any field in our report table. For instance, if we are interested in only sequences derived by the "Johne, VP1 gene" protocol, we can run:

```r
report <- ed_tests_report()
johne_report <- filter(report, meth == "Johne, VP1 gene")
```
  
  
### Automatically Grouped FASTA files by Viral Family, Method, or Both:  

The function `ed_fasta_group()` will automatically create FASTA files that have been grouped by either Viral Family or Method. The name of these files will correspond to the grouping variable. If we want to output grouped FASTA files based on Viral family, we can run:


```r
ed_fasta_group(report, grouping = "virus")
```

And R will create several FASTA files with names reflecting the type of Viral Family sequences they contain (e.g. *"Orthopoxviruses.fasta"*, *"Enteroviruses.fasta"*, *"Polyomaviruses.fasta"*).  

If we are interested in grouping by the method/protocol we can instead run:


```r
ed_fasta_group(report, grouping = "method")
```

And we would have a series of FASTA files grouped by method (e.g. *"Nitsch et al, RdRp gene.fasta"*, *"Chmielewicz et al, Term gene.fasta"*).  

If we are interested in grouping by both viral family and method, we can instead run:


```r
ed_fasta_group(report, grouping = "both")
```

And we would have a series of FASTA files grouped by both viral family and method whose names are separated by a "-" (e.g. *"Herpesvirus-Van DeVanter et al, Pol gene.fasta"*).

### Creating Excel files to return to EIDITH  
  
The function `ed_report_excel()` will take a report and output a .csv file that can easily be opened in Excel for entering data that can be returned to EIDITH. This function will work for the default report generated by `ed_tests_report()` or any subsetted part of that report. For example, the following code will create an Excel file for the default report and save it as *"report.csv"*:


```r
report <- ed_tests_report()
ed_report_excel(report, "report.csv")
```
  
We can do the same with our previous subsetted examples:  

```r
ed_report_excel(entero_report, "entero_report.csv")
ed_report_excel(johne_report, "johne_report.csv")
```