
<!-- README.md is generated from README.Rmd. Please edit that file -->

# flex\_bib

`flex_bib` is being developed by [Paul C. Bauer](http://paulcbauer.eu/)
and [Denis Cohen](https://denis-cohen.github.io/). It deals with two
problems:

1.  We normally use (one or several) large `.bib` files as input for our
    paper `.rmd` files. These are based on one or several authors’
    literature databases. However, we ideally want to end up with one
    single (max. two) `.bib` file(s) that includes only those references
    that were cited in our paper, e.g., if we prepare reproduction
    files.
2.  Papers normally have at least two sections – namely a main text and
    an appendix – that often have separate bibliographies. Ideally, in
    compiling our RMarkdown paper, we want two independent
    bibliographies that correspond to those sections and are located at
    their end. While one way of achieving this is two set up seprate
    `.rmd` files for the main text and the appendix, this approach does
    not allow for cross-referencing across the two parts.

`flex_bib` offers an automated solution to these problems. Based on the
citations from one or several input bib file(s) in a `.rmd` file, it
creates new output bib files that only contain the cited works within
predefined reference sections.

## Installation and dependencies

The function `flex_bib()` is currently not part of a package. You can
simply run the code below to source the function from GitHub:

``` r
library(devtools)
source_url("https://raw.githubusercontent.com/paulcbauer/flex_bib/master/flex_bib.R")
```

The use of `flex_bib` requires the installation of the following R
packages:

  - [`bib2df`](https://cran.r-project.org/web/packages/bib2df/index.html).
    *Note:* Until [this
    issue](https://github.com/ropensci/bib2df/issues/37) is resolved,
    please make sure to install this issue-specific version of the
    `bib2df` package via
    `remotes::install_github("ropensci/bib2df@issue_#29")`.
  - [`dplyr`](https://cran.r-project.org/web/packages/dplyr/index.html)
  - [`stringr`](https://cran.r-project.org/web/packages/stringr/index.html)

Additionally, `flex_bib` uses the lua-filter
[`multiple-bibliographies`](https://github.com/pandoc/lua-filters/tree/master/multiple-bibliographies)
for the creation and inclusion of multiple bibliographies using
`pandoc-citeproc`.

  - Download the file `multiple-bibliographies.lua` and store it as
    `multiple-bibliographies.lua` (not as a `.txt` file) in the same
    folder as your RMarkdown file.

## Contributors

  - **Paul C. Bauer**: Mannheim Centre for European Social Research,
    University of Mannheim
      - [paulcbauer.eu](https://sites.google.com/view/paulcbauer)
      - [@p\_c\_bauer](https://twitter.com/p_c_bauer)
  - **Denis Cohen**: Mannheim Centre for European Social Research,
    University of Mannheim
      - [denis-cohen.github.io](https://denis-cohen.github.io)
      - [@denis\_cohen](https://twitter.com/denis_cohen)

## Using `flex_bib()`: A short walkthrough

In this walkthrough, we use the `flex_bib()` function to

1.  combine two separate `.bib`-files, `references1.bib` and
    `references2.bib`;
2.  clean and repair the resulting bib-files (e.g., by removing unwanted
    fields such as the ISSN, ISBN, DOI, and URL);
3.  freely cite works from the combined `.bib`-files in an RMarkdown
    document which consists of a main text and an appendix;
4.  create separate `.bib`-files for the main text and the appendix,
    each containing only those entries which were cited in the
    respective sections;
5.  embed the corresponding separate bibliographies for the main text
    and the appendix.

### YAML Header

In order for this to work, we must specify some arguments in the YAML
header of our `.Rmd` file:

``` r
output:
  bookdown::html_document2:
    fig_caption: yes
    number_sections: no
    toc: false
    pandoc_args: --lua-filter=multiple-bibliographies.lua

bibliography_main:
  - partial_bib_1.bib
bibliography_app:
  - partial_bib_2.bib
link-citations: yes
linkcolor: blue
```

These are the important additions:

1.  The argument `pandoc_args: --lua-filter=multiple-bibliographies.lua`
    calls the lua-filter
    [`multiple-bibliographies`](https://github.com/pandoc/lua-filters/tree/master/multiple-bibliographies)
    for the creation and inclusion of multiple bibliographies using
    `pandoc-citeproc`.
2.  The definitions of `bibliography_main` and `bibliography_app`
    specify the suffixes of our partial bibliographies as well as the
    file names of the corresponding `.bib`files, which will be produced
    by `flex_bib()` in the next step.

### `flex_bib()` code chunk

After the YAML header, make sure to include a (hidden, yet evaluating) R
code chunk in your RMarkdown document. Here, we specify the following:

``` r
library(devtools)
source_url("https://raw.githubusercontent.com/paulcbauer/flex_bib/master/flex_bib.R")
flex_bib(
  rmarkdown_file = "manuscript.Rmd",
  bib_input = c("references1.bib", "references2.bib"),
  bib_output = "partial_bib.bib",
  by_sections = c("<!--- flex_bib appendix split -->"),
  repair = TRUE,
  removeISSN = TRUE,
  removeISBN = TRUE,
  removeDOI = TRUE,
  removeURL = TRUE
)
```

  - `rmarkdown_file = "manuscript.Rmd"` specifies that the very same
    RMarkdown script in which we are writing our paper will be scanned
    for citations.
  - `bib_input = c("references1.bib", "references2.bib")` means that we
    supply two larger bib files which will be combined and cleaned
    before the entries matching the citations in `manuscript.Rmd` will
    be extracted.
  - `bib_output = "partial_bib.bib"` specifies the name of the newly
    created (partial) `.bib` files. If we request multiple separate
    `.bib` files for different sections of the document, these will by
    default be saved with numerical suffixes before the file extensions,
    e.g. `partial_bib_1.bib`, `partial_bib_2.bib`, etc. *Note that we
    must already supply matching file names for these bibliographies in
    our YAML header.*
  - `by_sections = c("<!--- flex_bib appendix split -->")` defines the
    split point included in our `.Rmd` script. Here, we only supply one
    split point, which means that `flex_bib()` will extract citations
    separately before and after the split point (and thus produce two
    separate `.bib` files). Note that we can easily add more split
    points, e.g., by including distinct comments in the `.Rmd` script
    and adding them to the input vector for the `by_sections` argument.
  - `repair`, `removeISSN`, `removeISBN`, `removeDOI`, and `remove URL`
    are additional options that define how we want to tidy up our new
    `.bib` file(s).

### Document body

The following is the text body of the script `manuscript.Rmd`:

``` r
## Main Text

This is the main text. It cites one paper by @Athey2019-fy inline.
We also cite a book by @Berelsonetal1954. 

Let's also cite a report in parentheses [@Gallup2019].
Here is also some more intricate stuff with prefixes and suffixes
[e.g., @Hargittai2008-fa, pp. 31-57].

## Main Text References
<div id="refs_main"></div>

<!--- flex_bib appendix split -->
## Appendix

This is the appendix. It cites some other paper [@Friedman2009-gx].

## Appendix References
<div id="refs_app"></div>
```

In the document body, we can then freely include citations as we usually
would. When it comes to printing our bibliographies, there are two
things that we handle slightly differently from the default way of
including bibliographies in Markdown.

1.  `<!--- flex_bib appendix split -->`, supplied as a Markdown comment,
    defines the split point.
2.  We override the default of printing bibliographies at the end of the
    document by adding `<div id="refs_main"></div>` and `<div
    id="refs_app"></div>`, respectively. This ensure that the two
    bibliographies will be printed where we want them to be printed.
    Note that the names of the arguments must match those defined in the
    YAML header, just like the file names of the corresponding `.bib`
    files must match those of the new `.bib` files produced by
    `flex_bib`.

[Here](https://github.com/paulcbauer/flex_bib/blob/master/manuscript.pdf),
you can see the knitted PDF output generated from `manuscript.Rmd`.

## Caveats

There are [different bibtex
formats](https://tex.stackexchange.com/questions/109064/is-there-a-difference-between-and-in-bibtex).
`flex_bib` likes bib entries of the following format (with braces and
w/o line breaks in entries as `bib2df()` has problems with those):

``` r
@INCOLLECTION{Athey2017-sy,
  title     = {The Econometrics of Randomized Experiments},
  booktitle = {Handbook of Economic Field Experiments},
  author    = {Athey, Susan and Imbens, Guido W},
  publisher = {Elsevier},
  volume    = {1},
  pages     = {73--140},
  year      = {2017}
}
```

If your entries look more like this, i.e., include lines breaks and
quotation marks (sometimes not) as separators…

``` r
@ARTICLE{Ermisch2009-qf,
  title       = "Measuring people's trust",
  author      = "Ermisch, John and Gambetta, Diego and Laurie, Heather and
                 Siedler, Thomas and Uhrig, S C Noah",
  journal     = "Journal of the Royal Statistical Society",
  volume      =  172,
  number      =  4,
  pages       = "749--769",
  year        =  2009,
}
```

…you can run your bib files through the following functions:

``` r
library(devtools)
source_url("https://raw.githubusercontent.com/paulcbauer/flex_bib/master/replace_bib_braces.R")
source_url("https://raw.githubusercontent.com/paulcbauer/flex_bib/master/merge_bib_lines.R")
x <- stringi::stri_read_lines("references.bib")
x <- replace_bib_braces(x = x) # Replace (non-existant) quotes by braces
x  <- merge_bib_lines(x = x) # merge line breaks
stringi::stri_write_lines(x, "references_test.bib")
```

The resulting bib files can then be used as input for `flex_bib`.

## Vignette

Additional information on how to use the function can be found in the
vignette:

  - PDF:
    [Link](https://github.com/paulcbauer/flex_bib/blob/master/vignette.pdf)
    ([Download
    link](https://github.com/paulcbauer/flex_bib/raw/master/vignette.pdf))
  - HTML:
    [Link](https://htmlpreview.github.io/?https://github.com/paulcbauer/flex_bib/blob/master/vignette_html.html)
