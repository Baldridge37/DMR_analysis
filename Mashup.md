Take the the w50.gff files of the tissues you would like to compare into one folder
Use the perl mashup script in the group folder to create a TSV document of tissue C, and T values, and scores.

```
source perl-5.16.2 
perl -S ~/group-data/bin/gff_mashup.pl -c c,t,score -o MASHUP.txt *.w50.gff
```

Pitfalls
There are a few things to be aware of at this stage to avoid errors downstream.
1. Make sure that all `$1` in the GFF files are the same. This isn't essential but can definitely save time later on.
head the files to make sure they're all the same and use sed to edit them if necessary

```
cat file.gff | sed 's/CHR/Chr/g' > newfile.gff
```

2. MAKE SURE THE FILES HAVEN'T BEEN CONVERTED FROM TAIR8! 
This isn't so much a problem if they have been converted at the w1.gff stage but is a huge problem if they have been converted at the w50.
w50 files should never be converted from TAIR8 to 10, or vice versa, as it makes no sense to add or remove bases to 50bp windows.
The whole data gets shuffled along rather than gaining information on the differences between 8 and 10.
This might "look" ok when examinging by eye on signal map but renders the whole DMR analysis impossible.

It would be best to re-reun the alignment with TAIR10 but if you have the w1s they can be converted to TAIR10 (Though the new bases are still missing data)
