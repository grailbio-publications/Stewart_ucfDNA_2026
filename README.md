# Stewart_ucfDNA_2026

Analysis code and de-identified data for the manuscript:

> **Biopsy-free detection of new and recurrent urothelial cancers using methylation patterns in urine cell-free DNA**

This repository has the R Markdown notebook that produces the figures, tables, and
summary statistics in the manuscript, and the analysis-ready dataset it reads.

## Repository structure

```
Stewart_ucfDNA_2026/
├── Rmd/
│   └── urine_cfdna_manuscript_analysis.Rmd     # Main analysis notebook (figures + tables)
├── data/
│   └── urine_cfdna_manuscript_data_20261014.xlsx  # De-identified, analysis-ready data
├── output/
│   └── urine_cfdna_manuscript_analysis.html    # Pre-rendered report
├── LICENSE
└── README.md
```

## Data

`data/urine_cfdna_manuscript_data_20261014.xlsx` has four sheets:

| Sheet                 | Description                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------|
| `Clinical Data`       | One row per participant (n = 367): demographics, cohort, cancer status/type, grade, T stage, neoadjuvant therapy, pCR status, surveillance outcomes (cystoscopy, cytology, pathology, recurrence-free survival). |
| `Sample Data`         | One row per participant × biofluid sample (n = 501; urine and plasma): QC status, cfDNA concentration/yield, coverage, fragment length, tumor methylated fraction (TMeF), classifier score, and cancer call. |
| `Clinical Dictionary` | Variable descriptions and allowed values/ranges for `Clinical Data`.                                 |
| `Sample Dictionary`   | Variable descriptions and allowed values/ranges for `Sample Data`.                                   |

The two data sheets are joined on `participant_id`.

### Cohorts

| Cohort                        | Biobank | Use                                                     |
|-------------------------------|---------|---------------------------------------------------------|
| UCSD Training                 | UCSD    | Classifier training / baseline characterization         |
| MGB Holdout (Baseline)        | MGB     | Independent validation of cancer detection              |
| UCSD Holdout (Pre-Surgery)    | UCSD    | Prediction of pathologic complete response (pCR)        |
| MGB Holdout (Pre-Surgery)     | MGB     | Prediction of pathologic complete response (pCR)        |
| UCSD Holdout (Surveillance)   | UCSD    | Detection of recurrence during NMIBC surveillance       |

## Analyses included

The notebook `Rmd/urine_cfdna_manuscript_analysis.Rmd` has these sections:

1. **Cohort overview**: patient characteristics by cohort and cancer type; QC summary.
2. **Assay output**: cfDNA concentration, yield, and fragment length by biofluid and cancer status.
3. **TMeF of baseline samples**: TMeF by cancer type and tumor grade.
4. **Baseline sensitivity/specificity**: performance by cohort, cancer type (BC/UTUC), and grade, with Wilson 95% CIs.
5. **UCSD surveillance**: PPV/NPV for recurrence, tile plot against cystoscopy/cytology/biopsy, and Kaplan–Meier recurrence-free survival by urine ctDNA status.
6. **Pre-surgery pCR analysis**: urine, plasma, and urine-or-plasma performance for residual disease at radical cystectomy/nephroureterectomy.
7. **ROC**: ROC curves and AUCs (with 95% CI) for baseline detection, surveillance recurrence, and pre-surgery residual cancer.
8. **Reproducibility**: render parameters, `sessionInfo()`, and build state.

## Environment requirements

### R

- **R ≥ 4.3** (the report in `output/` was rendered with R 4.3.2 on `x86_64-pc-linux-gnu`).
- **Pandoc** is needed to render R Markdown to HTML. It ships with RStudio. Without
  RStudio, install it from <https://pandoc.org/installing.html>.

### R packages

The notebook calls every package with `pkg::fun()`, so nothing is attached with
`library()`. Each package below only needs to be installed. The "Tested version"
column lists the versions used for the report in `output/`.

| Package       | Tested version | Purpose                                        |
|---------------|----------------|------------------------------------------------|
| rmarkdown     | 2.25           | Rendering                                      |
| knitr         | 1.45           | Rendering                                      |
| openxlsx      | 4.2.5.2        | Reading the Excel workbook                     |
| dplyr         | 1.2.0          | Data manipulation (needs ≥ 1.1.0 for `.by`)    |
| forcats       | 1.0.0          | Factor handling                                |
| purrr         | 1.0.2          | Functional iteration                           |
| scales        | 1.4.0          | Axis/percent formatting                        |
| ggplot2       | 4.0.x          | Plotting (needs ≥ 3.5.0 for `legend.position.inside`) |
| patchwork     | 1.3.x          | Figure composition (needs ≥ 1.2.0 for `free()`) |
| ggbeeswarm    | 0.7.2          | Quasirandom point layouts                      |
| ggnewscale    | 0.4.10         | Multiple fill scales in tile plots             |
| ggh4x         | 0.2.8          | Strip styling                                  |
| gtsummary     | 1.7.2          | Summary tables and statistical tests           |
| gt            | 0.10.1         | Table rendering                                |
| DescTools     | 0.99.54        | Binomial (Wilson) confidence intervals         |
| CIfinder      | 2.0.0          | PPV/NPV confidence intervals                   |
| pROC          | 1.18.5         | ROC curves and AUC confidence intervals        |
| survival      | 3.5-8          | Survival objects                               |
| survminer     | 0.4.9          | Kaplan–Meier plots                             |
| pander        | 0.6.5          | Printing `sessionInfo()`                       |

> **Note on `gtsummary`:** The notebook was written and tested with **gtsummary 1.7.x**.
> gtsummary 2.0 changed several internals (e.g. `add_p()` paired tests), so we recommend
> pinning 1.7.2 for exact reproduction.

### Installation

```r
install.packages(c(
  "rmarkdown", "knitr", "openxlsx", "dplyr", "forcats", "purrr", "scales",
  "ggplot2", "patchwork", "ggbeeswarm", "ggnewscale", "ggh4x", "gt",
  "DescTools", "CIfinder", "pROC", "survival", "survminer", "pander",
  "remotes"
))

# Pin gtsummary to the tested version
remotes::install_version("gtsummary", version = "1.7.2")
```

## Reproducing the analysis

Clone the repository and render the notebook from the repository root:

```bash
git clone https://github.com/grailbio-publications/Stewart_ucfDNA_2026.git
cd Stewart_ucfDNA_2026
Rscript -e 'rmarkdown::render("Rmd/urine_cfdna_manuscript_analysis.Rmd", output_dir = "output")'
```

This writes `output/urine_cfdna_manuscript_analysis.html`, with figures embedded in the
HTML.

### Parameters

You can override the YAML parameters at render time:

| Parameter       | Default                                              | Description                         |
|-----------------|------------------------------------------------------|-------------------------------------|
| `data_file`     | `../data/urine_cfdna_manuscript_data_20261014.xlsx`  | Path to the input workbook, relative to `Rmd/` |
| `figure_format` | `png`                                                | knitr graphics device (e.g. `png`, `pdf`, `svg`) |

Example:

```r
rmarkdown::render(
  "Rmd/urine_cfdna_manuscript_analysis.Rmd",
  output_dir = "output",
  params = list(figure_format = "pdf")
)
```

## License

This project is released under the [MIT License](LICENSE).
