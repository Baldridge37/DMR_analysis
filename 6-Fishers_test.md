# Fisher's test

Now we have the DMRs that have passed our stringent criteria we can see if they are also statistically significant.
(Spoiler alert: they almost all are)

From the Stata export I have a file that looks like this
```
sequence        start   stop    soma_all_C      soma_all_T      tissue_all_C       tissue_all_T
CHR1    110600  110700  4       2969    623     7516
CHR1    243650  243800  588     1890    1996    4334
```

To perform Fisher's exact test I used the fisher_exact_test.pl perl script
```
source perl-5.16.2
perl -s ~/group-data/bin/fisher_exact_test.pl -a 4 -b5 -c6 -d7 -h -i input.txt -o output.txt
```

`-a` and `-b` are tissue1 c and t respectively and `-c` and `-d` are tissue2. -h means header, so the first line is skipped.

the output.txt will have an extra column with p values. 

I then combined this output.txt with the DMR2 column to give
```
sequence        start   stop    soma_all_C      soma_all_T      tissue_all_C       tissue_all_T       p-val   DMR2
CHR1    110600  110700  4       2969    623     7516    2.99E-80        0
CHR1    243650  243800  588     1890    1996    4334    4.75E-14        0
````
Now we can see which DMRs have satisfied both these criteria and can go into our final GFF file.
For this I used two awk scripts 

```
 cat DMR2_postFisher.txt | awk '{if ($8<0.001) print $0}' | awk '{if ($9==1) print $0}' > DMR_final.txt
 ```
 
 These final DMRs were then made into a GFF file with another awk script.
 
 ```
 cat DMR_final.txt | awk '{print $1 "\t.\tDMR\t"$2"\t"$3"\t1\t.\t.\tID="NR";p-value="$8}' > DMR_final.gff
 ```
 
 I used `$9` to put in an ID for each DMR as we did previously, as well as the p-value from the Fisher test.
 I had a total of 1525 DMRs in this final output.
 
 And there you have it, DMRs that you're free to do what you like with.
