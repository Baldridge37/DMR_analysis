# Creating GFFs from Stata export

This will go through turning our Stata export txt file into a GFF file that can be used downstream

First we have to turn our data into a GFF with the required 9 columns for the regions where we have found DMRs

Currently heading our file will look like this
```
sequence        coord   dmr
CHR1    1
CHR1    51
CHR1    101
CHR1    151
CHR1    201
CHR1    251
CHR1    301
CHR1    351
CHR1    401
```

To extract the rows of interest and create a GFF I use awk.

## Awk

We want to extract the rows of interest (dmr==1) and create a gff for them. I performed

```
cat DMR1.txt | col -b | awk '{if ($3==1) print $1 "\t.\tDMR1\t"$2"\t"$2+49"\t1\t.\t.\t."}' > DMR1.gff
```
This takes the rows where `$3` equals 1 and prints the columns in the txt file, a label, and end positions into a GFF.
This now looks like
```
Chr1    .       DMR1        6501    6550    1       .       .       .
```
The columns are Chromosome, ., label, start, end, DMR==1, ., ., .,
This can now be viewed on Signal Map

## Bedtools

Now we want to merge the windows if they are within 100bp. To do this we use Bedtools

```
source bedtools-2.24.0
bedtools merge -d 100 -i DMR1.gff > DMR_merge.txt
```
This output will need to be created into another GFF

## Awk again

We will use a similar awk script to create a new gff, this time with the end positions from bedtools rather than +49

```
cat DMR_merge.txt | col -b | awk print $1 "\t.\tDMR_merge\t"$2"\t"$3"\t1\t.\t.\t."}' > DMR_merge.gff
```

Now we'll use another awk script to remove windows that are less than 100bp

```
cat DMR_merge.gff | awk '{if(($5-$4)>99) print $0}' > DMR_merge100.gff
```

For downstream analysis and ID in column nine is needed. For now I will just number them 1 to n.
To do this I used another awk script

```
cat DMR_merge100.gff | awk '{print $1"\t"$2"\t"$3"\t"$4"\t"$5"\t"$6"\t"$7"\t"$8"\t""ID=" NR }' > DMR_merge100ID.gff
mv DMRmerge100ID.gff DMRmerge100.gff
```
NR will insert the row number after `ID=` so now our DMR GFF file looks like this

```
Chr1    .       DMR_merge100        110600  110700  1       .       .       ID=1
Chr1    .       DMR_merge100        243650  243800  1       .       .       ID=2
Chr1    .       DMR_merge100        255800  255900  1       .       .       ID=3
```
