
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

For whom is `{scdrake}` purposed? It is primarily intended for
tech-savvy users (bioinformaticians), who pass on the results (reports,
images) to non-technical persons (biologists). At the same time,
bioinformaticians can quickly react to biologists’ needs by changing the
parameters of the pipeline, which then efficiently skips already
finished parts. This dialogue between the biologist and the
bioinformatician is indispensable during scRNA-seq data analysis.
`{scdrake}` ensures that this communication is performed in an effective
and reproducible manner.

The pipeline structure along with
[diagrams](https://github.com/bioinfocz/scdrake/blob/main/diagrams/README.md)
and links to outputs is described in `vignette("pipeline_overview")`
([link](https://bioinfocz.github.io/scdrake/articles/pipeline_overview.html)).

Huge thanks go to the authors of the [Orchestrating Single-Cell Analysis
with Bioconductor](https://bioconductor.org/books/3.15/OSCA) book on
whose methods and recommendations is `{scdrake}` largely based.

------------------------------------------------------------------------

# Installation instructions

## Using a Docker image (recommended)

A Docker image based on the [official Bioconductor
image](https://bioconductor.org/help/docker/) (version 3.15) is
available. This is the most handy and reproducible way how to use
`{scdrake}` as all the dependencies are already installed and their
versions are fixed. In addition, the parent Bioconductor image comes
bundled with RStudio Server.

The complete guide to the usage of `{scdrake}`’s Docker image can be
found in the [Docker
vignette](https://bioinfocz.github.io/scdrake/articles/scdrake_docker.html).
**We strongly recommend to go through even if you are an experienced
Docker user.** Below you can find just the basic command to download the
image and to run a detached container with RStudio in Docker or to run
`{scdrake}` in Singularity.

You can also run the image in
[SingularityCE](https://docs.sylabs.io/guides/latest/user-guide/quick_start.html)
(without RStudio) - see the Singularity section in the Docker vignette
above. If the image is already downloaded in the local Docker storage,
you can use `singularity pull docker-daemon:<image>`

You can pull the Docker image with the latest stable `{scdrake}` version
using

``` bash
docker pull jirinovo/scdrake:1.5.0-bioc3.15
singularity pull docker:jirinovo/scdrake:1.5.0-bioc3.15
```

or list available versions in [our Docker Hub
repository](https://hub.docker.com/r/jirinovo/scdrake/tags).

For the latest development version use

``` bash
docker pull jirinovo/scdrake:latest-bioc3.15
singularity pull docker:jirinovo/scdrake:latest-bioc3.15
```

**Note for Mac users with M1 chipsets**: you can use the `arm64` version
of the image:

``` bash
docker pull jirinovo/scdrake:1.5.0-bioc3.15-arm64
singularity pull docker:jirinovo/scdrake:1.5.0-bioc3.15-arm64
docker pull jirinovo/scdrake:latest-bioc3.15-arm64
singularity pull docker:jirinovo/scdrake:latest-bioc3.15-arm64
```

### Running the container

For the most common cases of host machines: Linux running Docker Engine,
and Windows or MacOS running Docker Desktop.

First make a shared directory that will be mounted to the container:

``` bash
mkdir ~/scdrake_projects
cd ~/scdrake_projects
```

And run the image that will expose RStudio Server on port 8787 on your
host:

``` bash
docker run -d \
  -v $(pwd):/home/rstudio/scdrake_projects \
  -p 8787:8787 \
  -e USERID=$(id -u) \
  -e GROUPID=$(id -g) \
  -e PASSWORD=1234 \
  jirinovo/scdrake:1.5.0-bioc3.15
```

For Singularity, also make shared directories and execute the container
(“run and forget”):

``` bash
mkdir -p ~/scdrake_singularity
cd ~/scdrake_singularity
mkdir -p home/${USER} scdrake_projects
singularity exec \
    -e \
    --no-home \
    --bind "home/${USER}/:/home/${USER},scdrake_projects/:/home/${USER}/scdrake_projects" \
    --pwd "/home/${USER}/scdrake_projects" \
    path/to/scdrake_image.sif \
    scdrake <args> <command>
```

## Installing `{scdrake}` manually (not recommended)

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
download.file("https://raw.githubusercontent.com/bioinfocz/scdrake/1.5.0/renv.lock")
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
installed from the lockfile).

``` r
remotes::install_github(
  "bioinfocz/scdrake@1.5.0",
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
is installed into `~/.local/bin`, which is usually present in the `PATH`
environment variable. In case it isn’t, just add to your `~/.bashrc`:
`export PATH="${HOME}/.local/bin:${PATH}"`

**Every time you will be using the CLI make sure your current working
directory is inside an `{renv}` project.** You can read the reasons
below.

<details>
<summary>
Show details
</summary>

You might notice that a per-project `{renv}` library and an installed
CLI are “disconnected” and if you install `{scdrake}` and its CLI within
multiple projects (`{renv}` libraries), then the CLI scripts in
`~/.local/bin` will be overwritten each time. But when you run the
`scdrake` command inside an `{renv}` project, the `renv` directory is
automatically detected and the `{renv}` library is activated by
`renv::load()`, so the proper, locally installed `{scdrake}` package is
then used.

Also, there is a built-in guard: the version of the CLI must match the
version of the bundled CLI scripts inside the installed `{scdrake}`
package. Anyway, we think changes in the CLI won’t be very frequent, so
this shouldn’t be a problem most of the time.

</details>

> TIP: To save time and space, you can symlink the `renv/library`
> directory to multiple `{scdrake}` projects.

</details>

------------------------------------------------------------------------

## Quickstart

First run the `scdrake` image in Docker or Singularity - see the [Docker
vignette](https://bioinfocz.github.io/scdrake/articles/scdrake_docker.html)

Then you can go through the [Get Started
vignette](https://bioinfocz.github.io/scdrake/articles/scdrake.html)

------------------------------------------------------------------------

## Vignettes and other readings

See <https://bioinfocz.github.io/scdrake> for a documentation website
where links to vignettes below become real :-)

-   Guides:
    -   Using the Docker image:
        <https://bioinfocz.github.io/scdrake/articles/scdrake_docker.html>
        (or `vignette("scdrake_docker")`)
    -   01 Quick start (single-sample pipeline): `vignette("scdrake")`
    -   02 Integration pipeline guide: `vignette("scdrake_integration")`
    -   Advanced topics: `vignette("scdrake_advanced")`
    -   Extending the pipeline: `vignette("scdrake_extend")`
    -   `{drake}` basics: `vignette("drake_basics")`
        -   Or the official `{drake}` book:
            <https://books.ropensci.org/drake/>
-   General information:
    -   Pipeline overview: `vignette("pipeline_overview")`
    -   FAQ & Howtos: `vignette("scdrake_faq")`
    -   Command line interface (CLI): `vignette("scdrake_cli")`
    -   Config files (internals): `vignette("scdrake_config")`
    -   Environment variables: `vignette("scdrake_envvars")`
-   General configs:
    -   Pipeline config -\> `vignette("config_pipeline")`
    -   Main config -\> `vignette("config_main")`
-   Pipelines and stages:
    -   Single-sample pipeline:
        -   Stage `01_input_qc`: reading in data, filtering, quality
            control -\> `vignette("stage_input_qc")`
        -   Stage `02_norm_clustering`: normalization, HVG selection,
            dimensionality reduction, clustering, cell type annotation
            -\> `vignette("stage_norm_clustering")`
    -   Integration pipeline:
        -   Stage `01_integration`: reading in data and integration -\>
            `vignette("stage_integration")`
        -   Stage `02_int_clustering`: post-integration clustering and
            cell annotation -\> `vignette("stage_int_clustering")`
    -   Common stages:
        -   Stage `cluster_markers` -\>
            `vignette("stage_cluster_markers")`
        -   Stage `contrasts` (differential expression) -\>
            `vignette("stage_contrasts")`

We encourage all users to read
[basics](https://books.ropensci.org/drake) of the `{drake}` package.
While it is not necessary to know all `{drake}` internals to
successfully run the `{scdrake}` pipeline, its knowledge is a plus. You
can read the minimum basics in `vignette("drake_basics")`.

Also, the prior knowledge of Bioconductor and its classes (especially
the
[SingleCellExperiment](https://bioconductor.org/packages/3.15/bioc/html/SingleCellExperiment.html))
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
    Bioconductor*](https://bioconductor.org/books/3.15/OSCA) book.
-   The
    [scran](https://bioconductor.org/packages/3.15/bioc/html/scran.html),
    [scater](https://bioconductor.org/packages/3.15/bioc/html/scater.html),
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
