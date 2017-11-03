# Window by annotation

Now we have the locations of our DMRs we need to be able to find out the methylation within them, rather than at the w50 level.
To do this we need to use the w1.gff files of our tissues to extract the c, t, and score values across each DMR.
Now, instead of methylation in 50bp windows we will have methylation across variable sized DMRs.

For this I used the following Slurm script.

```
#!/bin/bash
#SBATCH -p nbi-medium
#SBATCH --mail-type=END,FAIL # notifications
#SBATCH --mail-user=ss
#SBATCH --mem=64000
#SBATCH --array=0-
#SBATCH --cpus-per-task=1

source bssequel-0.0.1

ARRAY=()

window_by_annotation_2.pl -g DMR_merge100.gff -k -t 'ID' -o ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CG_wba.gff ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CG.w1.gff
window_by_annotation_2.pl -g DMR_merge100.gff -k -t 'ID' -o ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CHG_wba.gff ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CHG.w1.gff
window_by_annotation_2.pl -g DMR_merge100.gff -k -t 'ID' -o ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CHH_wba.gff ${ARRAY[$SLURM_ARRAY_TASK_ID]}.CHH.w1.gff
```

Note: window_by_annotation_2.pl requires perl-5.16 so check the environment variables before you submit to avoid this error

```
Devel::OverloadInfo version 0.004 required--this is only version 0.002 at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Class/MOP/Mixin/HasOverloads.pm line 9.
BEGIN failed--compilation aborted at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Class/MOP/Mixin/HasOverloads.pm line 9.
Compilation failed in require at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Class/MOP.pm line 17.
BEGIN failed--compilation aborted at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Class/MOP.pm line 17.
Compilation failed in require at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Moose/Exporter.pm line 8.
BEGIN failed--compilation aborted at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Moose/Exporter.pm line 8.
Compilation failed in require at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Moose.pm line 15.
BEGIN failed--compilation aborted at /nbi/software/testing/perl/5.22.1/x86_64/lib/site_perl/5.22.1/x86_64-linux/Moose.pm line 15.
Compilation failed in require at /nbi/software/testing/bssequel/0.0.1/x86_64/bin/lib/GFF/Tree.pm line 6.
BEGIN failed--compilation aborted at /nbi/software/testing/bssequel/0.0.1/x86_64/bin/lib/GFF/Tree.pm line 6.
```

