# PEGASUS_flies
Application of the PEGASUS gene-level association test to the Drosophila Genome Reference Panel

Developed using Pegasus Version 1.3 (Nakka et al. 2016 - Genetics)

December 12, 2019

Code is modified from VEGAS source code (Liu et al. 2010 - AJHG)

Questions: Contact samuel_smith1@brown.edu

Requirements:

Perl 5

R (version 3.0.2 or higher)

Plink 1.07 (1.9 beta 3, 7 Jun is also okay)

Please note the following:

R and PLINK must be installed and in your PATH for this program to run.
This program requires the R packages corpcor and CompQuadForm. If they are missing, it will return an error.
This directory (PEGASUS) contains the following:

`pegasus_flies.pl` - source code for PEGASUS_flies program.

DGRP - directory containing genotype data from the 205 lines of the Drosophila Genome Research Project.

`glist-dm5.57` - list of genes and their bounds developed from annotation file provided by the DGRP (http://dgrp2.gnets.ncsu.edu/data.html)

Usage: Commands to run this program should have the following format:

`./pegasus_flies.pl [input pvalues file] [parameters]`

The input p-values file is a tab-separated table of SNP names and corresponding p-values. For an example file of p-values, please see `example.txt`. The one required parameter is to specify which reference genotype data or custom LD file should be used for LD calculations. If using the provided DGRP data, type: `-pop DGRP`. If using your own genotype data to calculate LD, type `-custom` followed by the path to and prefix of your PLINK bed files. Alternatively, if you would like to use your own LD file containing pre-calculated values for correlation between SNPs in every gene in the dataset, type `-ld-file` followed by the absolute path to your LD file in PLINK format. Note that correlation values should be r and not r^2. (See below for further explanation and sample commands.)

This program uses the following flags for required LD parameter:

`-custom [path + prefix of PLINK bed files for custom genotypes to use for calculating LD, must be in bed format]` specify path and name of plink bed/bim/fam files with custom genotypes to calculate LD matrices (ex."path/to/bed/file/mydata")

OR

`-pop DGRP` use given DGRP data as reference genotypes to calculate LD 

OR

`-ld-file [absolute path + name of pre-calculated LD file]` specify absolute path and name of pre-calculated LD file in default PLINK format (see below) containing correlation values (r) between SNPs in each gene to be evaluated.

```Accepted format for custom LD file: CHR_A BP_A SNP_A CHR_B BP_B SNP_B R

 1       10000015       X_10000015_INS      1       10000031       X_10000031_SNP     0.872127 

 1       10000015       X_10000015_INS      1       10000035       X_10000035_SNP     0.804592 
``` 
Please note that processing the custom LD file can slow down the PEGASUS program considerably for large genome-wide SNP datasets. Parallelizing gene score computation by running each chromosome separately is highly recommended with this option.

Optional: `-out [path + prefix of outfile]` specify name for the .out output file with gene scores

`-chr [# between 1 and 6]` compute gene scores for given chromosome only. Chromosomes are labeled 1 through 6 using the following conversion: X:1, 2L:2, 2R:3, 3L:4, 3R:5, 4:6. If you wish to include genes on Y and mitochondrial, encode them as chromosomes 7 and 8, respectively.

`-upper [# of bp downstream of gene to be included ex. 30000]` This flag can only be used with custom genotypes or pre-calculated LD files. The default is 1kb.

`-lower [# of bp upstream of gene to be included ex. 30000]` This flag can only be used with custom genotypes or pre-calculated LD files. The default is 1kb.

The following is a sample command using the given example files:

`./pegasus_flies.pl example.txt -pop DGRP -out test`

The resulting output file test.out should match the given example.out file.

To parallelize the process for large datasets, it is useful to run each chromosome separately using the -chr flag as in the following example:

`./pegasus_flies.pl example.txt -pop DGRP -chr 1 -out example_chr1`

Troubleshooting:

When using custom genotypes, please make sure the length of the .bim file is the same as the length of the p-values file (they should contain the same markers).

`ERROR: Could not place marker for left/right window edge` - this is a plink error message that occurs when there are not enough markers in a given bp range and can be ignored.

Make sure the directory DGRP is in the same directory as `pegasus_flies.pl` — this directory contains files needed for the program to function.
