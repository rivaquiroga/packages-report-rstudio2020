debug-example.R
================
2020-01-27

``` r
# dplyr::na_if(x, y) replaces NA values in `x` with `y`
# it works when x is a data.frame _without_ Date objects, but fails when there is a Date in the df
# Can you use our debugging tools to figure out where and why it is failing?
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
test <- tibble(a = lubridate::today() + runif(5) * 30, b = c(1:4, ""), c = c(runif(4), ""), d = c(sample(letters, 4, replace = TRUE), ""))
test
```

    ## # A tibble: 5 x 4
    ##   a          b     c                 d    
    ##   <date>     <chr> <chr>             <chr>
    ## 1 2020-02-17 1     0.842025624355301 z    
    ## 2 2020-02-17 2     0.538034547818825 w    
    ## 3 2020-02-25 3     0.483770739519969 n    
    ## 4 2020-01-31 4     0.762716533150524 b    
    ## 5 2020-02-14 ""    ""                ""

``` r
# test %>% na_if("")  # we find an error here: "Error in charToDate(x) : character string is not in a standard unambiguous format"

traceback()
```

    ## No traceback available

``` r
# The traceback is easier to understand without the pipe involved
# na_if(test, "")

# Also useful to look at the implementation of `na_if()`
# We get the same error from
# test[test == ""] <- NA
# traceback()

# And also from
# as.Date("")

# So it seems we need to only use na_if on _character_ columns
test %>%
  mutate_if(is.character, ~ na_if(.x, ""))
```

    ## # A tibble: 5 x 4
    ##   a          b     c                 d    
    ##   <date>     <chr> <chr>             <chr>
    ## 1 2020-02-17 1     0.842025624355301 z    
    ## 2 2020-02-17 2     0.538034547818825 w    
    ## 3 2020-02-25 3     0.483770739519969 n    
    ## 4 2020-01-31 4     0.762716533150524 b    
    ## 5 2020-02-14 <NA>  <NA>              <NA>

``` r
devtools::session_info()
```

    ## ─ Session info ──────────────────────────────────────────────────────────
    ##  setting  value                       
    ##  version  R version 3.6.2 (2019-12-12)
    ##  os       Ubuntu 18.04.2 LTS          
    ##  system   x86_64, linux-gnu           
    ##  ui       X11                         
    ##  language es_CL:es                    
    ##  collate  es_CL.UTF-8                 
    ##  ctype    es_CL.UTF-8                 
    ##  tz       America/Santiago            
    ##  date     2020-01-27                  
    ## 
    ## ─ Packages ──────────────────────────────────────────────────────────────
    ##  package     * version date       lib source        
    ##  assertthat    0.2.1   2019-03-21 [1] CRAN (R 3.6.0)
    ##  backports     1.1.4   2019-04-10 [1] CRAN (R 3.6.0)
    ##  callr         3.3.0   2019-07-04 [1] CRAN (R 3.6.0)
    ##  cli           1.1.0   2019-03-19 [1] CRAN (R 3.6.0)
    ##  crayon        1.3.4   2017-09-16 [1] CRAN (R 3.6.0)
    ##  desc          1.2.0   2018-05-01 [1] CRAN (R 3.6.0)
    ##  devtools      2.1.0   2019-07-06 [1] CRAN (R 3.6.0)
    ##  digest        0.6.20  2019-07-04 [1] CRAN (R 3.6.0)
    ##  dplyr       * 0.8.3   2019-07-04 [1] CRAN (R 3.6.0)
    ##  evaluate      0.14    2019-05-28 [1] CRAN (R 3.6.0)
    ##  fansi         0.4.0   2018-10-05 [1] CRAN (R 3.6.0)
    ##  fs            1.3.1   2019-05-06 [1] CRAN (R 3.6.0)
    ##  glue          1.3.1   2019-03-12 [1] CRAN (R 3.6.0)
    ##  highr         0.8     2019-03-20 [1] CRAN (R 3.6.0)
    ##  htmltools     0.3.6   2017-04-28 [1] CRAN (R 3.6.0)
    ##  knitr         1.23    2019-05-18 [1] CRAN (R 3.6.0)
    ##  lubridate     1.7.4   2018-04-11 [1] CRAN (R 3.6.0)
    ##  magrittr      1.5     2014-11-22 [1] CRAN (R 3.6.0)
    ##  memoise       1.1.0   2017-04-21 [1] CRAN (R 3.6.0)
    ##  pillar        1.4.2   2019-06-29 [1] CRAN (R 3.6.0)
    ##  pkgbuild      1.0.3   2019-03-20 [1] CRAN (R 3.6.0)
    ##  pkgconfig     2.0.2   2018-08-16 [1] CRAN (R 3.6.0)
    ##  pkgload       1.0.2   2018-10-29 [1] CRAN (R 3.6.0)
    ##  prettyunits   1.0.2   2015-07-13 [1] CRAN (R 3.6.0)
    ##  processx      3.4.0   2019-07-03 [1] CRAN (R 3.6.0)
    ##  ps            1.3.0   2018-12-21 [1] CRAN (R 3.6.0)
    ##  purrr         0.3.3   2019-10-18 [1] CRAN (R 3.6.2)
    ##  R6            2.4.0   2019-02-14 [1] CRAN (R 3.6.0)
    ##  Rcpp          1.0.1   2019-03-17 [1] CRAN (R 3.6.0)
    ##  remotes       2.1.0   2019-06-24 [1] CRAN (R 3.6.0)
    ##  rlang         0.4.2   2019-11-23 [1] CRAN (R 3.6.2)
    ##  rmarkdown     1.13    2019-05-22 [1] CRAN (R 3.6.0)
    ##  rprojroot     1.3-2   2018-01-03 [1] CRAN (R 3.6.0)
    ##  sessioninfo   1.1.1   2018-11-05 [1] CRAN (R 3.6.0)
    ##  stringi       1.4.3   2019-03-12 [1] CRAN (R 3.6.0)
    ##  stringr       1.4.0   2019-02-10 [1] CRAN (R 3.6.0)
    ##  testthat      2.1.1   2019-04-23 [1] CRAN (R 3.6.0)
    ##  tibble        2.1.3   2019-06-06 [1] CRAN (R 3.6.0)
    ##  tidyselect    0.2.5   2018-10-11 [1] CRAN (R 3.6.0)
    ##  usethis       1.5.1   2019-07-04 [1] CRAN (R 3.6.0)
    ##  utf8          1.1.4   2018-05-24 [1] CRAN (R 3.6.0)
    ##  vctrs         0.2.0   2019-07-05 [1] CRAN (R 3.6.0)
    ##  withr         2.1.2   2018-03-15 [1] CRAN (R 3.6.0)
    ##  xfun          0.8     2019-06-25 [1] CRAN (R 3.6.0)
    ##  yaml          2.2.0   2018-07-25 [1] CRAN (R 3.6.0)
    ##  zeallot       0.1.0   2018-01-28 [1] CRAN (R 3.6.0)
    ## 
    ## [1] /home/ciruelo/R/x86_64-pc-linux-gnu-library/3.6
    ## [2] /usr/local/lib/R/site-library
    ## [3] /usr/lib/R/site-library
    ## [4] /usr/lib/R/library
