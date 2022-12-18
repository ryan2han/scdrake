
# scdrake

[![NEWS:
updates](https://img.shields.io/badge/NEWS-updates-informational)](NEWS.md)
[![Documentation and
vignettes](https://img.shields.io/badge/Documentation%20&%20vignettes-bioinfocz.github.io/scdrake-informational)](https://bioinfocz.github.io/scdrake)
[![Overview and
outputs](https://img.shields.io/badge/Overview%20&%20outputs-vignette(%22pipeline_overview%22)-informational)](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html)
[![Pipeline
diagram](https://img.shields.io/badge/Pipeline%20diagram-Show-informational)](https://github.com/bioinfocz/scdrake/blob/main/diagrams/README.md)
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

-   Import of scRNA-seq data: [10x Genomics Cell
    Ranger](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/what-is-cell-ranger)
    output, delimited table, or `SingleCellExperiment` object.
-   Quality control and filtering of cells and genes, removal of empty
    droplets.
-   Higly variable genes detection, cell cycle scoring, normalization,
    clustering, and dimensionality reduction.
-   Cell type annotation.
-   Integration of multiple datasets.
-   Computation of cluster markers and differentially expressed genes
    between clusters (denoted as “contrasts”).
-   Rich graphical and HTML outputs based on customizable RMarkdown
    documents.
    -   You can find links to example outputs
        [here](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html).
-   Thanks to `{drake}`, the pipeline is highly efficient, scalable and
    reproducible, and also extendable.
    -   Want to change some parameter? No problem! Only parts of the
        pipeline which changed will rerun, while up-to-date ones will be
        skipped.
    -   Want to reuse the intermediate results for your own analyses? No
        problem! The pipeline has smartly defined checkpoints which can
        be loaded from a `{drake}` cache.
    -   Want to extend the pipeline? No problem! The pipeline definition
        is just an R object which can be arbitrarily extended.

`{scdrake}` is aimed at both non-technical and bioinformatic public:
both will benefit from reports and visualizations, and the latter also
from the possibility to utilize all benefits of `{drake}` for custom
analyses.

The pipeline structure along with
[diagrams](https://github.com/bioinfocz/scdrake/blob/main/diagrams/README.md)
and links to outputs is described in `vignette("pipeline_overview")`
([link](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html)).

Huge thanks go to the authors of the [Orchestrating Single-Cell Analysis
with Bioconductor](https://bioconductor.org/books/release/OSCA) book on
whose methods and recommendations is `{scdrake}` largely based.

------------------------------------------------------------------------

# Installation instructions

Even though `{scdrake}` is a R package, we don’t recommend to install it
as a normal package due to the violation of reproducibility as the
latest versions of dependencies will be always installed, which can also
break its functionality.

## Using a Docker image (**recommended**)

A Docker image based on the [official Bioconductor
image](https://bioconductor.org/help/docker/) (version 3.15) is
available. This is the most handy and reproducible way how to use
`{scdrake}` as all the dependencies are already installed and their
version is fixed. In addition, the parent Bioconductor image comes
bundled with RStudio Server.

You can pull the Docker image with the latest stable `{scdrake}` version
using

``` bash
docker pull bioinfocz/scdrake:v1.4.0-bioc3.15
```

or list available versions in [our Docker Hub
repository](https://hub.docker.com/r/bioinfocz/scdrake/tags).

For the latest development version use

``` bash
docker pull bioinfocz/scdrake:latest-bioc3.15
```

## Installing `{scdrake}` manually

<details>
<summary>
Click for details
</summary>

### Install the required system packages

-   For Linux, follow the commands for your distribution
    [here](required_libs_linux.md).
-   For MacOS:
    `$ brew install libxml2 imagemagick@6 harfbuzz fribidi libgit2 geos pandoc`

### Install R \>= 4.2

See <https://cloud.r-project.org/>

From now on, all commands are for R.

### Install `{renv}`

[`{renv}`](https://rstudio.github.io/renv/) is an R package for
management of local R libraries. It is intended to be used on a
per-project basis, i.e. each project should use its own library of R
packages.

``` r
install.packages("renv")
```

### Initialize a new `{renv}` library

Switch to directory where you will analyze data and initialize a new
`{renv}` library:

``` r
renv::consent(TRUE)
renv::init()
```

Now exit and run again R. You should see a message that renv library has
been activated.

### Install BiocManager

``` r
renv::install("BiocManager")
```

### Install Bioconductor 3.15

``` r
BiocManager::install(version = "3.15")
```

### Restore `{scdrake}` dependencies from lockfile

`{renv}` also allows to export the current installed versions of R
packages (and other things) into a lockfile. Such lockfile is available
for `{scdrake}` and you can use it to install all dependencies by

``` r
## -- This is a lockfile for the latest stable version of scdrake.
download.file("https://raw.githubusercontent.com/bioinfocz/scdrake/vv1.4.0/renv.lock")
## -- You can increase the number of CPU cores to speed up the installation.
options(Ncpus = 2)
renv::restore(lockfile = "renv.lock", repos = BiocManager::repositories())
```

For the lockfile for the latest development version use

``` r
download.file("https://raw.githubusercontent.com/bioinfocz/scdrake/main/renv.lock")
```

### Install the `{scdrake}` package

Now we can finally install the `{scdrake}` package, but using a
non-standard approach - without its dependencies (which are already
installed).

``` r
remotes::install_github(
  "bioinfocz/scdrake@v1.4.0",
  dependencies = FALSE, upgrade = FALSE,
  keep_source = TRUE, build_vignettes = TRUE,
  repos = BiocManager::repositories()
)
```

For the latest development version use `"bioinfocz/scdrake"`.

### Install the command line interface (CLI)

Optionally, you can install `{scdrake}`’s CLI scripts with

``` r
scdrake::install_cli()
```

CLI should be now accessible as a `scdrake` command. By default, the CLI
will be installed into `~/.local/bin`, which is usually present in the
`PATH` environment variable. In case it isn’t, just add to your
`~/.bashrc`: `PATH="${HOME}/.local/bin:${PATH}"`

**Every time you will be using the CLI make sure your current working
directory is inside an `{renv}` project.** You can read the reasons
below.

<details>
<summary>
Show details
</summary>

You might notice that the per-project library and this CLI are now
“disconnected” and if you install `{scdrake}` and its CLI within
multiple projects (`{renv}` libraries), then the CLI scripts in
`~/.local/bin` will be overwritten each time. To overcome this
situation, there is a built-in guard: the version of the CLI must match
the version of the bundled CLI scripts inside the installed `{scdrake}`
package. Anyway, we think changes in the CLI won’t be very frequent, so
this shouldn’t be a problem most of the time.

</details>

> TIP: To save time and space, you can symlink the `renv/library`
> directory to multiple `{scdrake}` projects.

</details>

------------------------------------------------------------------------

## Vignettes and other readings

See <https://bioinfocz.github.io/scdrake> for a documentation website
where links to vignettes below become real :-)

-   Guides:
    -   Using the Docker image: `vignette("scdrake_docker")`
    -   01 Quick start (single-sample pipeline): `vignette("scdrake")`
    -   02 Integration pipeline guide: `vignette("scdrake_integration")`
    -   Extending the pipeline: `vignette("scdrake_extend")`
    -   `{drake}` basics: `vignette("drake_basics")`
        -   Or the official `{drake}` book:
            <https://books.ropensci.org/drake/>
-   General information:
    -   Pipeline overview: `vignette("pipeline_overview")`
    -   FAQ & Howtos: `vignette("scdrake_faq")`
    -   Command line interface (CLI): `vignette("scdrake_cli")`
    -   Config files: `vignette("scdrake_config")`
    -   Cluster markers: `vignette("cluster_markers")`
    -   Environment variables: `vignette("scdrake_envvars")`
-   General configs:
    -   Pipeline config: `vignette("config_pipeline")`
    -   Main config: `vignette("config_main")`
-   Targets and config parameters for each stage:
    -   Single-sample pipeline:
        -   Reading in data, filtering, quality control (stage
            `01_input_qc`): `vignette("stage_input_qc")`
        -   Normalization, HVG selection, dimensionality reduction,
            clustering, cell type annotation (stage
            `02_norm_clustering`): `vignette("stage_norm_clustering")`
    -   Integration pipeline:
        -   Reading in data and integration (stage `01_integration`):
            `vignette("stage_integration")`
        -   Post-integration clustering (stage `02_int_clustering`):
            `vignette("stage_int_clustering")`
    -   Common:
        -   Cluster markers stage: `vignette("stage_cluster_markers")`
        -   Contrasts stage: `vignette("stage_contrasts")`

We encourage all users to read
[basics](https://books.ropensci.org/drake) of the `{drake}` package.
While it is not necessary to know all `{drake}` internals to
successfully run the `{scdrake}` pipeline, its knowledge is a plus. You
can read the minimum basics in `vignette("drake_basics")`.

Also, the prior knowledge of Bioconductor and its classes (especially
the
[SingleCellExperiment](https://bioconductor.org/packages/release/bioc/html/SingleCellExperiment.html))
is considerable.

------------------------------------------------------------------------

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
guide](.github/CONTRIBUTING.md), please. All pull requests are welcome!
:slightly_smiling_face:

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

-   The [Bioconductor](https://www.bioconductor.org) ecosystem.
-   The [*Orchestrating Single-Cell Analysis with
    Bioconductor*](https://bioconductor.org/books/release/OSCA) book.
-   The
    [scran](https://bioconductor.org/packages/release/bioc/html/scran.html),
    [scater](https://bioconductor.org/packages/release/bioc/html/scater.html),
    and other great packages from [Aaron
    Lun](https://orcid.org/0000-0002-3564-4813) et al.
-   The [drake](https://github.com/ropensci/drake) package.
-   The [rmarkdown](https://github.com/rstudio/rmarkdown) package, and
    other ones from the [tidyverse](https://www.tidyverse.org)
    ecosystem.

### Development tools

-   Continuous code testing is possible thanks to [GitHub
    Actions](https://github.com/features/actions) through `{usethis}`,
    `{remotes}`, and `{rcmdcheck}`. Customized to use [Bioconductor’s
    docker containers](https://www.bioconductor.org/help/docker).
-   The [documentation website](https://bioinfocz.github.io/scdrake) is
    generated by `{pkgdown}`.
-   The code is styled automatically thanks to `{styler}`.
-   The documentation is formatted thanks to `{devtools}` and
    `{roxygen2}`.

This package was developed using `{biocthis}`.

<sup>\[Page generated on 2022-12-15 16:52:48 UTC+0000\]</sup>
