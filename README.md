
# scdrake

[![NEWS:
updates](https://img.shields.io/badge/NEWS-updates-informational)](NEWS.md)
[![Documentation and
vignettes](https://img.shields.io/badge/Documentation%20&%20vignettes-bioinfocz.github.io/scdrake-informational)](https://bioinfocz.github.io/scdrake)
[![Overview and
outputs](https://img.shields.io/badge/Overview%20&%20outputs-vignette\(%22pipeline_overview%22\)-informational)](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html)
![License](https://img.shields.io/github/license/bioinfocz/scdrake)
[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![R-CMD-check-bioc](https://github.com/bioinfocz/scdrake/actions/workflows/check-bioc.yaml/badge.svg?branch=main)](https://github.com/bioinfocz/scdrake/actions/workflows/check-bioc.yaml)

`{scdrake}` is a scalable and reproducible pipeline for secondary
analysis of droplet-based single-cell RNA-seq data. `{scdrake}` is an R
package built on top of the `{drake}` package, a
[Make](https://www.gnu.org/software/make)-like pipeline toolkit for [R
language](https://www.r-project.org).

The main features of the `{scdrake}` pipeline are:

  - Import of scRNA-seq data: [10x Genomics Cell
    Ranger](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/what-is-cell-ranger)
    output, delimited table, or `SingleCellExperiment` object.
  - Quality control and filtering of cells and genes, removal of empty
    droplets.
  - Higly variable genes detection, cell cycle scoring, normalization,
    clustering, and dimensionality reduction.
  - Cell type annotation.
  - Integration of multiple datasets.
  - Computation of cluster markers and differentially expressed genes
    between clusters (denoted as “contrasts”).
  - Rich graphical and HTML outputs based on customizable RMarkdown
    documents.
      - You can find links to example outputs
        [here](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html).
  - Thanks to `{drake}`, the pipeline is highly efficient, scalable and
    reproducible, and also extendable.
      - Want to change some parameter? No problem\! Only parts of the
        pipeline which changed will rerun, while up-to-date ones will be
        skipped.
      - Want to reuse the intermediate results for your own analyses? No
        problem\! The pipeline has smartly defined checkpoints which can
        be loaded from a `{drake}` cache.
      - Want to extend the pipeline? No problem\! The pipeline
        definition is just an R object which can be arbitrarily
        extended.

`{scdrake}` is aimed at both non-technical and bioinformatic public:
both will benefit from reports and visualizations, and the latter also
from the possibility to utilize all benefits of `{drake}` for custom
analyses.

Huge thanks go to the authors of the [Orchestrating Single-Cell Analysis
with Bioconductor](https://bioconductor.org/books/release/OSCA) book on
whose methods and recommendations is `{scdrake}` largely based.

-----

# Installation instructions

`{scdrake}` is currently not released on
[CRAN](https://cran.r-project.org), however, you can install its latest
stable version from GitHub using the following code:

``` r
if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
  BiocManager::install(version = "3.15")
}

BiocManager::install("bioinfocz/scdrake@v1.3.1")
```

For development version use

``` r
if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
  BiocManager::install(version = "3.15")
}

BiocManager::install("bioinfocz/scdrake")
```

In case you **won’t** be using `{scdrake}` from within the RStudio, you
will need to install [pandoc](https://pandoc.org/), which is used for
rendering of HTML reports.

  - For Ubuntu: `sudo apt install pandoc`
  - For MacOS: `brew install pandoc`
  - For other OSs see [official
    instructions](https://pandoc.org/installing.html).

## Installation failed?

The most common reason for a failed installation are missing system
libraries. When packages are compiled from source, some of them require
shared libraries not provided by R. Below you can find commands to
install those libraries for a specific OS.

If it does not help and you are really helpless, do not hesitate to open
a new issue.

### Linux

[See here](required_libs_linux.md)

### MacOS

You can install the required libraries with [Homebrew](https://brew.sh/)
package manager:

``` bash
brew install libxml2 imagemagick@6 harfbuzz fribidi libgit2 geos
```

-----

## Using `renv` (optional)

`{renv}` package allows to install other packages into local,
project-specific library, and thus, those won’t interfere with
system-wide packages.

<details>

<summary>▶ Click for details</summary>

Because `{scdrake}` uses a lot of packages, we also encourage users to
use the `{renv}` package, which will install all dependencies to a
local, private, project library. In case you have already initialized a
new `{scdrake}` project (as described in `vignette("scdrake")`) and your
working directory is in the project’s root, you can initialize a new
`{renv}` private library with

``` r
renv::restore()
```

It will use the `renv.lock` file (which is bundled with `{scdrake}`
package) holding all dependencies and their versions.

To save time and space, you can also symlink the `renv/library`
directory to multiple `{scdrake}` projects.

Alternatively, just install `{scdrake}` from within the activated renv
and all dependencies will be installed to the private library. But keep
in mind in that case dependencies will be installed from package’s
`DESCRIPTION` file in which package versions are not specified, and thus
the latest (and possibly untested) package versions will be installed.

</details>

-----

## Vignettes and other readings

See <https://bioinfocz.github.io/scdrake> for a documentation website
where links to vignettes below become real :-)

  - Guides:
      - 01 Quick start (single-sample pipeline): `vignette("scdrake")`
      - 02 Integration pipeline guide: `vignette("scdrake_integration")`
      - Extending the pipeline: `vignette("scdrake_extend")`
      - `{drake}` basics: `vignette("drake_basics")`
          - Or the official `{drake}` book:
            <https://books.ropensci.org/drake/>
  - General information:
      - Pipeline overview: `vignette("pipeline_overview")`
      - FAQ & Howtos: `vignette("scdrake_faq")`
      - Config files: `vignette("scdrake_config")`
      - Cluster markers: `vignette("cluster_markers")`
      - Environment variables: `vignette("scdrake_envvars")`
  - General configs:
      - Pipeline config: `vignette("config_pipeline")`
      - Main config: `vignette("config_main")`
  - Targets and config parameters for each stage:
      - Single-sample pipeline:
          - Reading in data, filtering, quality control (`01_input_qc`):
            `vignette("stage_input_qc")`
          - Normalization, HVG selection, dimensionality reduction,
            clustering, cell type annotation (`02_norm_clustering`):
            `vignette("stage_norm_clustering")`
      - Integration pipeline:
          - Reading in data and integration (`01_integration`):
            `vignette("stage_integration")`
          - Post-integration clustering (`02_int_clustering`):
            `vignette("stage_int_clustering")`
      - Common:
          - Cluster markers stage: `vignette("stage_cluster_markers")`
          - Contrasts stage: `vignette("stage_contrasts")`

We encourage all users to read
[basics](https://books.ropensci.org/drake) of the `{drake}` package.
While it is not necessary to know all `{drake}` internals to
successfully run the `{scdrake}` pipeline, its knowledge is a plus. You
can read the minimum basics in `vignette("drake_basics")`.

Also, the prior knowledge of Bioconductor and its classes (especially
the
[SingleCellExperiment](https://bioconductor.org/packages/release/bioc/html/SingleCellExperiment.html))
is considerable.

-----

## Citation

Below is the citation output from using `citation("scdrake")` in R.
Please run this yourself to check for any updates on how to cite
**scdrake**.

``` r
print(citation("scdrake"), bibtex = TRUE)
```

    To cite package ‘scdrake’ in publications use:
    
      Jiri Novotny and Jan Kubovciak (2021). scdrake: A Pipeline For 10x Chromium Single-Cell RNA-seq Data Analysis.
      https://github.com/bioinfocz/scdrake, https://bioinfocz.github.io/scdrake.
    
    A BibTeX entry for LaTeX users is
    
      @Manual{,
        title = {scdrake: A Pipeline For 10x Chromium Single-Cell RNA-seq Data Analysis},
        author = {Jiri Novotny and Jan Kubovciak},
        year = {2021},
        note = {https://github.com/bioinfocz/scdrake, https://bioinfocz.github.io/scdrake},
      }

Please note that the `{scdrake}` was only made possible thanks to many
other R and bioinformatics software authors, which are cited either in
the vignettes and/or the paper(s) describing this package.

## Help and support

In case of any problems or suggestions, please, open a new
[issue](https://github.com/bioinfocz/scdrake/issues). We will be happy
to answer your questions, integrate new ideas, or resolve any problems
:blush:

You can also use [GitHub
Discussions](https://github.com/bioinfocz/scdrake/discussions), mainly
for topics **not** related to development (bugs, feature requests etc.),
but if you need e.g. a general help.

## Contribution

If you want to contribute to `{scdrake}`, read the [contribution
guide](.github/CONTRIBUTING.md), please. All pull requests are welcome\!
:slightly\_smiling\_face:

## Code of Conduct

Please note that the `{scdrake}` project is released with a [Contributor
Code of
Conduct](https://bioinfocz.github.io/scdrake/CODE_OF_CONDUCT.html). By
contributing to this project, you agree to abide by its terms.

## Acknowledgements

### Funding

This work was supported by [ELIXIR CZ](https://www.elixir-czech.cz)
research infrastructure project (MEYS Grant No: LM2018131) including
access to computing and storage facilities.

### Software and methods used by `{scdrake}`

Many things are used by `{scdrake}`, but these are really worth
mentioning:

  - The [Bioconductor](https://www.bioconductor.org) ecosystem.
  - The [*Orchestrating Single-Cell Analysis with
    Bioconductor*](https://bioconductor.org/books/release/OSCA) book.
  - The
    [scran](https://bioconductor.org/packages/release/bioc/html/scran.html),
    [scater](https://bioconductor.org/packages/release/bioc/html/scater.html),
    and other great packages from [Aaron
    Lun](https://orcid.org/0000-0002-3564-4813) et al.
  - The [drake](https://github.com/ropensci/drake) package.
  - The [rmarkdown](https://github.com/rstudio/rmarkdown) package, and
    other ones from the [tidyverse](https://www.tidyverse.org)
    ecosystem.

### Development tools

  - Continuous code testing is possible thanks to [GitHub
    actions](https://github.com/features/actions) through `{usethis}`,
    `{remotes}`, and `{rcmdcheck}`. Customized to use [Bioconductor’s
    docker containers](https://www.bioconductor.org/help/docker) and
    ~~`{BiocCheck}`~~ (in the future, probably).
  - Code coverage assessment is possible thanks to
    [codecov](https://codecov.io/gh) and `{covr}`.
  - The [documentation website](https://bioinfocz.github.io/scdrake) is
    generated by `{pkgdown}`.
  - The code is styled automatically thanks to `{styler}`.
  - The documentation is formatted thanks to `{devtools}` and
    `{roxygen2}`.

This package was developed using `{biocthis}`.
