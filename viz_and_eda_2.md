viz part 1
================
Yuxuan Du
2023-09-28

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
library(ggridges)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

load the data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/daviddu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:20:07.77072 (8.524)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/daviddu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:20:13.636014 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/daviddu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:20:15.584735 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

make a plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

pipes and stuff

``` r
weather_df |>
  filter(name == "CentralPark_NY") |>
  ggplot(aes(x = tmin, y = tmax))+
  geom_point()
```

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

``` r
gg_nyc_weather = 
  weather_df |>
  filter(name == "CentralPark_NY") |>
  ggplot(aes(x = tmin, y = tmax))+
  geom_point()
```

## Fancy plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

plot with facets

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .3) +
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

try different variables

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = .3) +
  geom_smooth() + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

``` r
weather_df |>
  filter(name == "Molokai_HI") |>
  ggplot(aes(x = date, y = tmax))+
  geom_line()
```

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />
\## univariate plotting

``` r
ggplot(weather_df, aes(x = tmax, fill = name))+
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

``` r
ggplot(weather_df, aes(x = tmax, fill = name))+
  geom_density(alpha = .3, adjust = .75)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-10-2.png" width="90%" />

using box plots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

violin plots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_violin(aes(fill = name), alpha = .5)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

ridge plot

``` r
ggplot(weather_df, aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

## saving plots

``` r
ggplot_weather = 
  weather_df |>
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()

ggplot_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

``` r
ggsave("results/ggp_weather.pdf", ggplot_weather)
```

    ## Saving 6 x 3.6 in image

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

``` r
ggplot_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

## same plot from last time

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Min daily temp (degree C)", 
    y = "Max daily temp (degree C)", 
    color = "Location", 
    caption = "Max vs Min daily temp; data from moaa"
  ) + 
  scale_x_continuous(
    breaks = c(-15, 0, 15), 
    labels = c("-15 C", "0 C", "15 C")
  ) + 
  scale_y_continuous(
    position = "right", 
    trans = "sqrt"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 142 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

changing colors

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Min daily temp (degree C)", 
    y = "Max daily temp (degree C)", 
    color = "Location", 
    caption = "Max vs Min daily temp; data from moaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />

## Themes

``` r
weather_df |>
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) + 
  labs(
    title = "Temperature plot", 
    x = "Min daily temp (degree C)", 
    y = "Max daily temp (degree C)", 
    color = "Location", 
    caption = "Max vs Min daily temp; data from moaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

<img src="viz_and_eda_2_files/figure-gfm/unnamed-chunk-18-1.png" width="90%" />