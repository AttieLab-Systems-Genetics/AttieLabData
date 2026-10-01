# Attie Lab Data

This repository points to systems genetics data from the Attie Lab at the University of Wisconsin-Madison.

## Data Storage Units

Data reside in 3 major areas,
two partitions on
[ResearchDrive (`RD`)](https://researchdrive.wisc.edu/)
and one in an AWS
[Research Object Storage (`S3`)](https://it.wisc.edu/services/research-object-storage-s3/).

- [`adattie`  & `mkeller3` on `RD`](RD_Data.md)
- [`adattie-bucket-01` on `S3`](S3_Data.md)

## GitHub Repos

These repos are of two types:
documentation of data and analysis files,
and analysis tools.
Additional analysis tools and repos can be found at
<https://github.com/AttieLab-Systems-Genetics>.

- [`AttieLabData`](https://github.com/AttieLab-Systems-Genetics/AttieLabData): this repository
- [`sysgenDO1200`](https://github.com/AttieLab-Systems-Genetics/sysgenDO1200): Documents `RD` folder `mkeller3/General/main_directory`
- [`mkeller3Projects2`](https://github.com/AttieLab-Systems-Genetics/mkeller3Projects2): Mark Keller Workflows in `mkeller3/General/Projects2`
- [`sysgenAnalysis`](https://github.com/AttieLab-Systems-Genetics/sysgenAnalysis): Systems Genetic Analysis Tools
  - beginning work to upgrade `mkeller3Projects2`

## Data Formats

Data formats have evolved over time, in part based on
convention and/or convenience, and in part based on the need for fast and efficient access.

Data are usually collected in spreadsheets, often XLSX, and later cleaned up (by hand often) and stored as CSV. While this is convenient for researchers to inspect, these are slow.

Many files are also stored as
[RDS](https://r-statistics.co/readr-write_rds-in-R.html)
for faster access in R.

There are two other major storage formats.
The genotype files are complicated objects, having
a 3-dimensional array for each chromosome.
These can be stored as a large RDS file, but are
more efficiently handled with
[`qtl2fst`](https://kbroman.org/qtl2/assets/vignettes/qtl2fst.html).
This stores each chromosome as a long-format FST database, with a controller file (RDS format).
The `qtl2fst` has R S3 methods that extend the default method for `calc_genoprob` objects.

The other major storage format is `sqlite`, used
for SNP variants and related material.
There are currently several million SNPs and other variants that are important for mapping, reconstructing haplotypes, and getting at gene function.

## Parquet Files

It has been suggested to move all data to
[parquet](https://parquet.apache.org/) format
to streamline data access.
This would have the advantage of fast access and
fewer individual files to manage.
Note that it would be possible to create a
`qtl2parquet` package and method for `calc_genoprob`
objects.
