# Attie Lab Data Map

Compiled 2026-10-07 from `AttieLab-Systems-Genetics/AttieLabData` (main) and
its linked repos. Refresh per the SKILL.md protocol if stale.

**This is a mirror of `~/Heureka Bench/.heureka/skills/attie-data/data_map.md`
from Brian Yandell's Heureka Bench; see
[Heureka](https://github.com/AttieLab-Systems-Genetics/Heureka).**

## Storage units

| Unit | Path / mount | Access |
| --- | --- | --- |
| ResearchDrive `adattie` | `$RD/adattie/` — `W:` (Windows) or `/Volumes/adattie` (Mac) | UW ResearchDrive credentials; near full, legacy |
| ResearchDrive `mkeller3` | `$RD/mkeller3/` — `/Volumes/mkeller3` (Mac) | UW ResearchDrive credentials; current projects |
| S3 `adattie-bucket-01` | `/adattie-bucket-01/2026/` on collection `wisc-s3-campus-dev` | Globus: app.globus.org file manager, endpoint id `f79d3165-e61a-4a6f-93d6-c27f0bd85a27`; also AWS Research Object Storage (it.wisc.edu) |

## ResearchDrive: `adattie` (legacy)

- Mostly microarray expression raw data + historical lab-member records.
- `adattie/General/founder_diet_study` — raw + harmonized data for the
  Founder diet app (<https://connect.doit.wisc.edu/FounderDietStudy>, limited
  access).
- `adattie/General/founder_calcium_website` — raw + harmonized data for the
  Founder calcium app (<https://connect.doit.wisc.edu/FounderCalciumStudy>).
- Related tooling: foundrShiny (<https://byandell-sysgen.github.io/foundrShiny/>)
  and byandell-sysgen GitHub org (`FounderDietStudy`, `foundr` package).

### Founder diet study data layout (from `founder_diet_study` repo)

192 founder mice: 8 CC/DO strains (129, AJ, B6, CAST, NOD, NZO, PWK, WSB) ×
2 diets (HC_LF, HF_LC) × sex.

- `RawData/` (collated by Mark Keller; much from a Google Drive folder):
  - `Annotation/` — mouse metadata / sample annotation (xlsx)
  - `Enrich/` — dynamic plasma tracer enrichment (13C + deuterium) timecourse
  - `LivMet/` — liver metabolomics (raw + normalized, isobar-resolved peaks)
  - `LivRna/` — liver RNA counts, VST/VSD, sequencing QC
- `HarmonizedData/` (Brian Yandell) — per-layer RDS triples (`*Data.rds`,
  `*Signal.rds`, `*Stats.rds`) plus `traitData.sqlite` (~192 MB):
  - `Physio/` ~34k rows (~140 traits); `LivRna/` ~3.2M rows (~16.5k genes);
    `LivIso/` ~19.8M rows (~103k isoforms); `LivMet/` ~96k; `Lipid/` ~77k;
    `PlaMet*` ~58k (0/120 min); `Enrich*` ~19k; `Module/` WGCNA eigengenes.
  - ~23.3M rows total. Parquet migration specs + target schema in the repo's
    `DEVELOPER.md`.

## ResearchDrive: `mkeller3` (current)

- `mkeller3/General/main_directory` — **DO1200 project**.
  Documented in repo `sysgenDO1200` (public repo, private RD). Key subfolders:
  `raw_data/`, `rankz_data/`, `scans/`, `permutations/`,
  `annotated_peak_summaries/`, `mapping_data/`, `scripts/`,
  `files_for_cross_object/`, `snp_scans/`, `mediations/` — each has a README
  in the repo (symlinked into RD). `project.md` = project outline.

  **QTL scan result naming** (`annotated_peak_summaries/README.md`):
  - `DO1200_[phenotype_class]_[subjects]_mice_[model]_peaks.csv` (annotated peaks)
  - `DO1200_[phenotype_class]_[subjects]_mice_[model]_lod_profiles.rds` (in `lod_profile_rds_files/`)
  - `DO1200_[phenotype_class]_qtlxdiet_peaks_in[subjects]_mice_[model].csv` (in `qtlxcovar_peaks/`)
  - `[phenotype_class]`: clinical_traits | liver_genes | liver_isoforms | liver_lipids | liver_metabolites_labeled | liver_splice_juncs | plasma_metabolites
  - `[subjects]`: all | male | female | HC | HF
  - `[model]`: additive | diet_interactive | sex_interactive | sexbydiet_interactive
  - HF/HC and other subject splits exist only for `additive`; the interactive models are all-mice only. `scans/` holds the raw genome-wide LOD output behind these summaries.
  - `mediations/[phenotype_class]/[trait_subset_and_model]/[mediator_set]/{in,out}put/` — bmediatR per-peak results (`mediation_[trait]_[chr]_[pos].csv`), concatenated in `all_mediation_summary_files/`.
  - `files_for_cross_object/` holds the qtl2 inputs (`pheno_liver_13C_metabolites_*.csv`, `pheno_liver_2H_metabolites_*.csv`, genotypes/maps/covariates) — the raw liver-metabolite measurements before scanning.
- `mkeller3/General/Projects2` — **Mark Keller R workflows** for DO mice
  (lipids, metabolites, RNA). Documented in repo `mkeller3Projects2`
  (public; its README claims private). Pipelines: batch correction/ComBat +
  rank-Z transforms; QTL mapping & hotspot analysis; WGCNA; Bayesian
  mediation; visualization; utilities. Mirrors `Projects2/R scripts` on RD.

## S3: `adattie-bucket-01/2026/`

### `prod/miniViewer_3.0/` — 274 files, all `.fst` (updated Apr 2026)

Mouse liver model association scan matrices, per chromosome (1–19, X).
Every primary file has a paired `*_rows.fst` index (feature metadata).

Naming:

- Isoforms: `chromosome[chr]_liver_isoforms_[subjects]_mice_[model]_data_with_transcript_symbols.fst`
- Metabolites: `chromosome[chr]_liver_metabolites_labeled_[subjects]_mice_[model]_data_processed.fst`
- `[subjects]`: all | female | male | HC | HF
- `[model]`: additive | diet_interactive (all mice) | sex_interactive (all mice)
- Scale: isoform files GB-scale; metabolite MB-scale; rows files KB–low MB.

### `dev/miniViewer_3.0/` — development build of the above

### `dev/DO_mapping_files/output/` — 53,684 CSVs

Per-gene top SNP outputs from DO liver QTL mapping (high/moderate-impact
variants). Naming: `top_snps_hi_mod_impact_liver_<ENSG>_<CHR>_<POS_MB>.csv`
(~0.7–22 KB each).

### `physiological_qtl_app_s3/` — backend for the physiological QTL app

7 subdirectories: `phenotypes/` (trait measurements), `expression/`
(normalized matrices), `gwas/`, `snp_scans/`, `lod_profiles/` (LOD tracks),
`mediations/` (causal mediation scans), `reference/` (annotations, marker
positions, sample design).

## Formats guide

- **XLSX → CSV → RDS**: data collected in spreadsheets, hand-cleaned to CSV,
  then RDS for fast R access.
- **qtl2fst**: genotypes (3D array per chromosome) stored as per-chromosome
  FST + RDS controller file; R S3 methods extend `calc_genoprob`.
- **sqlite**: SNP/variant tables (millions of SNPs) for mapping, haplotype
  reconstruction, gene function.
- **Parquet migration in progress**: founder_diet_study has full schemas in
  `DEVELOPER.md`; lab considering `qtl2parquet` for genotypes. fst = R-fast
  local; parquet = cross-language/compressed (lab's own comparison in the
  AttieLabData README).

## GitHub repos

| Repo | Documents | Branch | Access |
| --- | --- | --- | --- |
| `AttieLab-Systems-Genetics/AttieLabData` | This data map's source of truth (README, RD_Data.md, S3_Data.md) | main | public |
| `AttieLab-Systems-Genetics/sysgenDO1200` | RD `mkeller3/General/main_directory` (DO1200) | main | public |
| `AttieLab-Systems-Genetics/mkeller3Projects2` | RD `mkeller3/General/Projects2` (Keller workflows; README claims private, but repo is public) | master | public |
| `AttieLab-Systems-Genetics/sysgenAnalysis` | R analysis package (qtl/hotspot/conserve: `analyze_*.R` + `explore_*.qmd`) | main | public |
| `AttieLab-Systems-Genetics/founder_diet_study` | Founder diet study data + Parquet migration docs | master | public |
| `byandell-sysgen/FounderDietStudy`, `byandell-sysgen/foundr` | Founder diet app + support package | main | public |

Full org: <https://github.com/AttieLab-Systems-Genetics>

## App endpoints

- Founder diet study: <https://connect.doit.wisc.edu/FounderDietStudy>
- Founder calcium study: <https://connect.doit.wisc.edu/FounderCalciumStudy>
- foundrShiny: <https://byandell-sysgen.github.io/foundrShiny/>
