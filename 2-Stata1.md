# Stata 

Once your mashup script has completed your MASHUP.txt file can be loaded into Stata. 
just go File > Import > Text data (delimited, .csv, ...). My files are tab delimited.

This takes a while so go an put the kettle on.

Once it has loaded one can create new columns and variables using `gen`. First off define the tissue types you want to compare.
I want to compare somatic to other tissues so I will create somatic averages of methylation in CG, CHG, and CHH contexts.

## Averages
Define which tissues are your reps for each group. I am using four somatic tissues and analysing CG, CHG, and CHH methylation
```
gen somaN_C*= …
```
Create a modified variable for each. Missing data (.) are replaced by 0
```
gen somaN_C*_md1= somaN_C*
replace somaN_C*_md1 =0 if somaN_C*==.
```
Generate a column with the sum of reps with data for that window. Keep the data as missing only if all reps have data missing
```
gen soma_C*_sum_new = soma1_C*_md1 + … somaN_C*_md1
replace soma_C*_sum_new=. If soma1_C*==. & soma2_C*==. … somaN_C*
```
Generate a new modified column to count the number of reps that have data for that window
```
gen somaN_C*_md2 = somaN_C*
replace somaN_C*_md2 =1 if somaN_C*!=.
replace somaN_C*_md2 =0 if somaN_C*==.
```
Generate a totals column to create the average
```
gen soma_C*_column_no_new = soma1_C*_md2 + … somaN_C*_md2
gen soma_C*_avg_new = soma_C*_sum_new/soma_C*_column_no_new if soma_C*_column_no_new!=0 
```
Repeat for all contexts and your tissue(s) of interest to compare to somatic.

## DMRs
Now we have the averages calculated we can look for Differentially Methylated Regions.
First off create columns of differences in methylation. 
```
gen diff_C* = tissue_C*_avg - soma_C*_avg
```
Then replace missing data with 0
```
replace diff_C*=0 if diff_C*==.
```
Create a sum of differences column. (There should only be 0s but you can replace dots just in case)
```
gen diff_sum = diff_CG + diff_CHG + diff_CHH
```
Create a DMR column
```
gen DMR=.
replace DMR=1 if diff_CG>a & diff_CHG>b & diff_CHH>c & diff_sum>d
```
This will give windows where the differences in methylation you have defined are satisfied.
The numbers a-d can be whatever you want and will vary between tissues.

Now we can export the DMRs for further analysis by File>Export>Tab delimited
Select sequence (CHRn), Coordinates, and your DMR column. Export as txt
