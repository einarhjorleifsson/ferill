
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ramb

<!-- badges: start -->
<!-- badges: end -->

The goal of {ramb} is to return various “move”-objects to tibbles for
further downstream processing using the tidyverse lingo.

## Installation

You can install the development version of {ramb} from
[GitHub](https://github.com/einarhjorleifsson/ramb) with:

``` r
# install.packages("devtools")
devtools::install_github("einarhjorleifsson/ramb")
```

## Example

Basic example of workflow:

``` r
library(tidyverse)
library(ramb)
d <- 
  creel %>% 
  rename(ID = id) %>% 
  as.data.frame() %>% 
  moveHMM::prepData(type = "UTM", coordNames = c("x", "y")) %>% 
  filter(ID == 1) %>%
  filter(!is.na(step)) %>%
  # convert step length from meters to knots (nautical miles per hour)
  mutate(speed = step/ 60 * 1.9438)
mixtools::normalmixEM(d$speed, mu = c(1,4,8), sigma = c(1,1,1)) %>%
  tidy_normalmixEM() %>% 
  glimpse()
#> number of iterations= 18 
#> Rows: 1,746
#> Columns: 6
#> $ x     <dbl> 7.684481, 7.485738, 7.317747, 7.291460, 7.085823, 7.137934, 7.17…
#> $ modes <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
#> $ mu    <dbl> 0.5455891, 0.5455891, 0.5455891, 0.5455891, 0.5455891, 0.5455891…
#> $ lamda <dbl> 0.6213695, 0.6213695, 0.6213695, 0.6213695, 0.6213695, 0.6213695…
#> $ sigma <dbl> 0.1543559, 0.1543559, 0.1543559, 0.1543559, 0.1543559, 0.1543559…
#> $ y     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0…
```

``` r
o <-
  creel %>%
  filter(id == 1) %>%
  select(time, lon, lat) %>%
  as.data.frame() %>%
  EMbC::stbc(., info = -1)
#> [1]   0  -0.0000e+00       4       583
#> [1] ... Stable clustering
o %>% 
  tidy_bin_clst_path() %>% 
  glimpse()
#> Rows: 583
#> Columns: 10
#> $ dTm   <dttm> 2017-05-19 06:50:28, 2017-05-19 06:51:28, 2017-05-19 06:52:28, …
#> $ lon   <dbl> -4.113320, -4.116755, -4.120097, -4.123131, -4.125934, -4.128799…
#> $ lat   <dbl> 58.20003, 58.20115, 58.20224, 58.20348, 58.20486, 58.20612, 58.2…
#> $ spn   <dbl> 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, 60, …
#> $ dst   <dbl> 236.8297, 230.7055, 225.5699, 224.7963, 218.4333, 220.0266, 221.…
#> $ hdg   <dbl> 5.265419, 5.268006, 5.374033, 5.462746, 5.405811, 5.373317, 5.37…
#> $ W     <dbl[,4]> <matrix[26 x 4]>
#> $ A     <dbl> 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, 3, …
#> $ turn  <dbl> 0.000000000, 0.002587027, 0.106026693, 0.088713472, 0.056934632,…
#> $ speed <dbl> 3.947162, 3.845092, 3.759498, 3.746605, 3.640556, 3.667111, 3.68…
```
