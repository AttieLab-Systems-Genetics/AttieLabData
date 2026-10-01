# S3 Data for Attie Lab

The overall S3 bucket structure for the Attie lab `2026` directory on collection `wisc-s3-campus-dev` is organized into three primary environments: `prod` for production data and `dev` for development assets. In addition, `physiological_qtl_app_s3` is a separate directory that contains data for the physiological QTL analysis and visualization application.

The current directory path in [Globus File Manager](https://app.globus.org/file-manager?origin_id=f79d3165-e61a-4a6f-93d6-c27f0bd85a27&origin_path=%2Fadattie-bucket-01%2F2026%2Fdev%2F&two_pane=false) is `/adattie-bucket-01/2026/` on collection `wisc-s3-campus-dev`.

- [prod](#prod)
- [dev](#dev)
- [physiological_qtl_app_s3](#physiological_qtl_app_s3)

## prod

The directory `/adattie-bucket-01/2026/prod/`
has one sub-directory `miniViewer_3.0/` containing **274 total files** stored in serialized fast (`.fst`) format, organized by data type, chromosome, and experimental model.

### High-Level Overview

- **Total Volume:** 274 items modified in early April 2026, primarily consisting of high-dimensional genomic, transcriptomic, and metabolomic association datasets for mouse liver models.
- **Paired Data Architecture:** Every primary dataset (`.fst`) is accompanied by an index/row-lookup table (`*_rows.fst`) containing transcript or feature metadata.

### Naming Convention

The filenames in `prod/miniViewer_3.0/` follow a templated structure across two main data formats (isoforms and metabolites).

**General Naming Schema**

- **Isoforms:**
`chromosome[chr]_[phenotype_class]_[subjects]_mice_[model]_data_with_transcript_symbols[_rows].fst`
- **Metabolites:**
`chromosome[chr]_[phenotype_class]_labeled_[subjects]_mice_[model]_data_processed[_rows].fst`

**Variable Components & Levels**

- **`[chr]` (Genomic Partition):**
  - Levels: `1` through `19`, `X` (e.g., `chromosome10`)
- **`[phenotype_class]` (Phenotype Class):**
  - `liver_`isoforms` (transcript isoform expression)
  - `liver_metabolites` (metabolite abundance)
- **`[subjects]` (Subject Stratification):**
  - `all`: Combined cohort
  - `female`: Female-only subset
  - `male`: Male-only subset
  - `HC`: High carbohydrate diet subset
  - `HF`: High fat diet subset
- **`[model]` (Statistical Model Type):**
  - `additive`: Additive genetic model (scanned across `all`, `female`, `male`, `HC`, and `HF`)
  - `diet_interactive`: Diet-by-genotype interaction model (evaluated on `all` mice)
  - `sex_interactive`: Sex-by-genotype interaction model (evaluated on `all` mice)
- **Suffix Type:**
  - `.fst`: Primary association / scan matrix (gigabyte-scale for isoforms, megabyte-scale for metabolites)
  - `_rows.fst`: Row metadata index table containing transcript symbols or feature annotations (kilobyte to low megabyte-scale)

## dev

The directory `/adattie-bucket-01/2026/dev/` has two subdirectories,
**miniViewer_3.0/** and **DO_mapping_files/**,
with application data assets and reference mapping resources,
respectively.
The `miniViewer_3.0/` has the development build of the `miniViewer` v3.0 data repository,
containing partitioned analytical matrices (e.g., fast `.fst` serialized transcriptomic, metabolomic,
and interactive association models) to feed interactive viewer applications.

### DO_mapping_files/output/

The `DO_mapping_files/` has one subdirectory `output/` that contains the genomic and phenotypic mapping assets tailored for Diversity Outbred (DO)
mouse cohort analyses (such as founder genotype reconstructions, recombination maps, or marker reference files).
It has per-gene candidate variant association outputs from Diversity Outbred (DO) mouse liver QTL mapping.

- **Total Scope:** 53,684 individual `.csv` tables representing genome-wide QTL scan outputs across mouse genes.
- **Content Focus:** Variant effect mapping focusing on high- and moderate-impact single nucleotide polymorphisms (SNPs) associated with liver gene expression.
- **File Profiles:** Typical file sizes range from small minimal hits (~736 B) to denser multi-variant regions (~15–22 KB).

Files adhere to a standardized naming pattern encoding the target feature, genomic location, and peak position:
`top_snps_hi_mod_impact_liver_<GENE_ID>_<CHROMOSOME>_<POSITION_MB>.csv`

- **Prefix & Tissue Context:** `top_snps_hi_mod_impact_liver_` denotes the filter criteria (high/moderate impact variants) and target tissue (liver).
- **Ensembl Gene Identifier:** Standard mouse Ensembl gene IDs (e.g., `ENSMUSG00000000001`, `ENSMUSG00000000037`).
- **Genomic Coordinates:**
- **Chromosome:** Autosomes (`3`, `6`, `7`, `9`, `11`, `14`, `16`, `18`, etc.) and sex chromosomes (`X`).
- **Peak Coordinate:** Peak LOD or physical location in megabases (e.g., `_3_108.1978.csv`, `_X_159.299436.csv`).

## physiological_qtl_app_s3

The directory `/adattie-bucket-01/2026/physiological_qtl_app_s3/` contains the data backend for the physiological QTL analysis and visualization application.
This is the base data directory for the physiological QTL application hosted on S3 storage.
It is modularized into **7 specialized data directories** separating input profiles, association statistics, mediation layers, and static genomic references.

### `phenotypes/` & `expression/` (Biological Inputs)

- **`phenotypes/`:** Clinical and physiological trait measurements across the experimental cohort.
- **`expression/`:** Normalized transcriptomic or protein expression matrices used as quantitative molecular traits.

### `gwas/`, `snp_scans/`, & `lod_profiles/` (Genetic Mapping Outputs)

- **`gwas/`:** Genome-wide association study summary tables and variant-level statistics.
- **`snp_scans/`:** Dense single-nucleotide polymorphism mapping results across specific candidate regions.
- **`lod_profiles/`:** Chromosome-wide and genome-wide Logarithm of the Odds (LOD) score tracks for trait-to-locus linkage.

### `mediations/` (Causal Modeling)

- Intermediate causal mediation scan results linking genetic loci, expression intermediates, and downstream physiological phenotypes.

### `reference/` (Annotation & Metadata)

- Genomic annotations, marker positions, physical coordinates, and sample design tables supporting the app's interactive visualizers.
