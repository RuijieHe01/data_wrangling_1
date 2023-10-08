Tidy Data
================

Focus on readr.

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
#Have `tidyr`, `dplyr`, `haven`, `readr`
```

## `pivot_longer` function

Import and load the PULSE data

``` r
pulse_data =
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

Wide format to long format …

``` r
pulse_data_tidy =
  pulse_data %>% 
  pivot_longer(
    bdi_score_bl :bdi_score_12m, #from bl to 12m
    names_to ="visit", #create new variable from existing
    names_prefix = "bdi_score_", #remove the matched start of each variable name
    values_to = "bdi"
  )
```

## Put everything into a single chunk

**Rewrite, combine, and extend (to add a mutate)**

``` r
pulse_data =
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>%
  pivot_longer(
    bdi_score_bl :bdi_score_12m, #from bl to 12m
    names_to ="visit", #create new variable from existing
    names_prefix = "bdi_score_", #remove the matched start of each variable name
    values_to = "bdi"
  ) %>% 
  relocate(id, visit) %>% #select to have `id`, `visit` in the first
  mutate(visit = recode(visit, "bl" = "00m"))
```

DONE with `pivot_longer` example

## `pivot_wider` function

Make up some data (using `tibble` function)

``` r
analysis_result =
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4) #Add some observational time
  )

analysis_result %>% 
  pivot_wider(
    names_from = "time",
    values_from = "mean"
  )
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

## (BINDING ROWS) In multiple tables and stack rows from thses table up

**Binding rows using the LotR data.**

First step: import each table

``` r
fellowship_ring =
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship_ring")

two_towers =
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "two_towers")

return_king =
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")
```

Next step: Bind all the rows together

``` r
lotr_tidy =
  bind_rows(fellowship_ring, two_towers, return_king) %>%
  janitor::clean_names() %>% 
  relocate(movie) %>% 
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )
```
