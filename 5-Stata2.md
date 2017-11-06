# Stata: second round

Now that we have created a mashup file for the DMR annotations we can go back to stata to calculate differences in methylation.

We can begin creating averages of methylation as we did previously. First we create a new column with the sums of methylation.
Then we create a column with the total number of replicates with data at that point. We then divide the sum by the number 
of reps to get the average. Detailed instructions on how to do this while avoiding missing data are in Stata1.

We can then apply our DMR criteria to the data by creating columns of differences.
As well as specific differences in CG, CHG, and CHH methylation I also want a large difference is all contexts.
so I created another column

```
gen diff_C_all=0
replace diff_C_all = diff_CG + diff_CHG + diff_CHH
```

Then we can create a DMR column
```
gen DMR=0
replace DMR2 =1 if diff_CG > 0 & diff_CHG > 0.05 & diff_CHH >0 .1 & diff_C_all > 0.4
```
This will give a column of 0s and 1s showing whether the earlier DMRs meet our new criteria.

As a further criterion, I want each DMR to have higher methylation in all my tissue(s) of interest's reps than somatic.
That is to say, the tissue(s) of interest minimum should be greater than the somatic maximum.

To do this I created a new CMethyl column calculating methylation from all Cs and Ts, regardless of context.
```
gen somaN_C = (tissue from mashup)-C
gen somaN_C1 = somaN_C
replace somaN_C1=0 if somaN_C==.

gen somaN_T = (tissue from mashup)-T
gen somaN_T1 = somaN_C
replace somaN_T1=0 if somaN_T==.

gen somaN_Cmethyl=.
replace somaN_Cmethyl=somaN_C1/(somaN_C1 + somaNT1)
```
We will also need totals of Cs and Ts of each so generate a total column too
```
gen soma_all_C = soma1_C1 + ... somaN_C1
```
We will need these totals columns later.

Now we can find the maximum methylation across all the reps
```
gen soma_max = soma1_Cmethyl
replace soma_max = soma2_Cmethyl if soma2_Cmethyl > soma_max
replace soma_max = soma3_Cmethyl if soma3_Cmethyl > soma_max
```
Run this through all of your somatic tissues until you have a column of maximums. 
If you run that line with soma1_Cmethyl at the end then 0 changes should be made as you already have all the highest values.

Perform this with your tissues of interest too, but reversing it to find the minumum values.
Now we can find out if this last criteria is satisfied
```
gen min_diff=0
replace min_diff=1 if tissue_min > soma_max
```
If both criteria are satisfied we can take these DMRs forward. We can make a new column to see if this is true

```
gen DMR2 = DMR*min_diff
```
I then exported two files
One contained sequence (CHR), coordinate (start), somaC, somaT, tissueC, tissueT
The other contained sequence, coordinate, and DMR2

As I hadn't included a mashup with the stop position I had to use excel to combine the stop position from the window_by_annotation
annotation gff with the stata export. If the mashup had the stop position in it I could have just exported directly.

I then had a file with the following columns in a `.txt` file 
```
sequence        start   stop    soma_all_C      soma_all_T      tissue_all_C       tissue_all_T
CHR1    110600  110700  4       2969    623     7516
CHR1    243650  243800  588     1890    1996    4334
```
I could have included the DMR column in the next steps but I combined it after.
We're now ready to go to the final step of Fisher's exact test and GFF creation
