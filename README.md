
<!-- README.md is generated from README.Rmd. Please edit that file -->

# tidy\_bib

The package/function is being developed by [Paul C.
Bauer](http://paulcbauer.eu/) and [Denis
Cohen](https://denis-cohen.github.io/) .

It deals with two problems:

1.  We normally use (one or several) large .bib files as input for our
    paper .rmd files. These are based on one or several authors’
    literature databases. However, we ideally want to end up with one
    single .bib file that includes only those references that were cited
    in our paper, e.g., if we prepare reproduction files.
2.  Papers normally have at least two sections namely a main section and
    an appendix that also have independent bibliographies. Ideally, in
    compiling our RMarkdown paper we want two independent bibliographies
    that correspond to those sections and are located at their end.

`tidy_bib` can solve those problems automatically for us namely to tidy
up (delete unused references from) a larger bib file based on the
citations in a .rmd file. It creates a new cleaned bib file(s).
`tidy_bib` can also rely on several bib files as input and merge them.

## Installation

The function `tidy_bib_file` is currently not part of a package. So
simply run the code below to source the function from github. You will
need the `df2bib` package.

``` r
library(devtools)
source_url("https://raw.githubusercontent.com/paulcbauer/tidy_bib/master/tidy_bib.R")
```

## How to

Information on how to use the function can be found in the vignette:
<https://github.com/paulcbauer/tidy_bib/blob/master/vignette.pdf>
([Download
link](https://github.com/paulcbauer/tidy_bib/raw/master/vignette.pdf))
