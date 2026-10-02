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
- [`founder_diet_study`](https://github.com/AttieLab-Systems-Genetics/founder_diet_study): Support Data Repo for Founder diet study

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

The choice between the fst package and Apache Parquet comes down to whether you are working purely within the R ecosystem or managing an open, multi-language big data pipeline.

### Quick Comparison of FST and Parquet

This was provided by Google Gemini.

| Feature | fst package | Apache Parquet |
| --- | --- | --- |
| Primary Language | R exclusively | Universal (Python, R, Java, C++, etc.) |
| Read/Write Speed | Extremely fast inside R (utilizes massive multi-threading) | Fast, but often slower than fst for native R operations |
| File Compression | Moderate (optimized for raw I/O throughput) | Excellent (highly compressed via dictionary-encoding) |
| Ecosystem Fit | Local R scripts, shiny apps, single-machine processing | Big data infrastructure (Spark, AWS S3, Cloud Data Lakes) |
| Random Access | Highly advanced (efficient row and column slicing) | Column-oriented slicing (poor random row access) |

------------------------------

### Key Differences## 1. Performance and Use Case

- fst: This package is optimized for speed. It compiles directly into R and uses LZ4 or ZSTD compression to deliver lightning-fast read/write operations by utilizing multiple CPU cores. Benchmarks show that fst can be significantly faster than Parquet for processing massive R data frames locally. It also features true random access, allowing you to read specific row or column slices without loading the entire file into memory. [1, 2, 3, 4]
- Apache Parquet: Built primarily for analytical querying and storage efficiency in distributed systems. While exceptionally fast compared to text formats like CSV, it generally yields slower read/write cycles than fst on a single local machine using R. [2, 5]

### 2. Interoperability

- fst: The format is essentially a silo. It is explicitly written for R data frames. Reading .fst files in Python or other tools requires spinning up an R interface, making it impractical for cross-language workflows. [1, 6]
- Apache Parquet: The industry standard for big data. Parquet integrates natively with Python (pandas/PyArrow), Spark, AWS Athena, DuckDB, and almost every modern data stack. [5, 7, 8]

### 3. File Size and Compression

- fst: Prioritizes raw CPU decompression speed over minimizing storage footprint. As a result, its files can be significantly larger than Parquet. [3, 7]
- Apache Parquet: Employs advanced dictionary encoding, bit-packing, and run-length encoding. It compresses the data heavily, saving substantial disk and network storage costs. [5, 7]

### Summary Recommendation

- Choose fst if you are building an isolated R pipeline, local Shiny dashboard, or doing predictive modeling entirely in R, where you need to save and pull large data frames to/from your local SSD at maximum speed. [4, 9]
- Choose Apache Parquet if your data needs to be accessed by Python, passed to data engineers, or stored long-term in cloud buckets like AWS S3 or Google Cloud Storage. [5, 7]

[1] [https://stackoverflow.com](https://stackoverflow.com/questions/71710435/does-the-r-arrow-package-have-anything-like-the-random-access-capability-of-the)
[2] [https://issues.apache.org](https://issues.apache.org/jira/browse/ARROW-6230)
[3] [https://www.r-bloggers.com](https://www.r-bloggers.com/2017/02/fst-fast-serialization-of-r-data-frames/)
[4] [https://github.com](https://github.com/fstpackage/fst)
[5] [https://www.youtube.com](https://www.youtube.com/watch?v=EJ_kqkQ7vh8&t=478)
[6] [https://prof-thiagooliveira.netlify.app](https://prof-thiagooliveira.netlify.app/post/data-read-write-performance/)
[7] [https://ursalabs.org](https://ursalabs.org/blog/2019-10-columnar-perf/)
[8] [https://www.reddit.com](https://www.reddit.com/r/Python/comments/tver6p/i_made_a_video_comparing_different_data_storage/)
[9] [https://prof-thiagooliveira.netlify.app](https://prof-thiagooliveira.netlify.app/post/data-read-write-performance/)
