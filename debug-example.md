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
    ## 1 2020-02-17 1     0.543946952791885 s    
    ## 2 2020-02-14 2     0.940974850673229 h    
    ## 3 2020-02-16 3     0.256544091040269 y    
    ## 4 2020-02-19 4     0.895076693035662 q    
    ## 5 2020-02-12 ""    ""                ""

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
    ## 1 2020-02-17 1     0.543946952791885 s    
    ## 2 2020-02-14 2     0.940974850673229 h    
    ## 3 2020-02-16 3     0.256544091040269 y    
    ## 4 2020-02-19 4     0.895076693035662 q    
    ## 5 2020-02-12 <NA>  <NA>              <NA>
