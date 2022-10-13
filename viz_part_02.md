via_part_02
================
Elaine Yanxi Chen
2022-10-04

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6     ✔ purrr   0.3.4
    ## ✔ tibble  3.1.7     ✔ dplyr   1.0.9
    ## ✔ tidyr   1.2.0     ✔ stringr 1.4.0
    ## ✔ readr   2.1.2     ✔ forcats 0.5.1
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

Copy code from the course website to download data.

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-09-22 20:15:00 (8.402)

    ## file min/max dates: 1869-01-01 / 2022-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-22 20:15:04 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-22 20:15:06 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

``` r
weather_df %>% view
```

## Scatterplot

But better this time

can play around with various scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = "Minimum Daily Temp(C)",
    y = "Maximum Daily Temp(C)",
    title = "Scatterplot of daily temp extremes",
    caption = "data come from the rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-10, 0, 15),
    labels = c("-10C", "0", "15") 
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_02_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Can also change colours Make a new version

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = "Minimum Daily Temp(C)",
    y = "Maximum Daily Temp(C)",
    title = "Scatterplot of daily temp extremes",
    caption = "data come from the rnoaa package"
  ) +
  viridis::scale_colour_viridis(
    name = "Location",
    discrete = TRUE)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_02_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Themes

``` r
ggp_weather = weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  labs(
    x = "Minimum Daily Temp(C)",
    y = "Maximum Daily Temp(C)",
    title = "Scatterplot of daily temp extremes",
    caption = "data come from the rnoaa package"
  ) +
  viridis::scale_colour_viridis(
    name = "Location",
    discrete = TRUE)
```

``` r
ggp_weather +
  theme_minimal() +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_02_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

can get rid of the legend with “none” the order of the theme commands
matter; need to be careful;

## Data in geom()

``` r
central_park_df = weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki_df = 
  weather_df %>% filter(name == "Waikiki_HA")

ggplot(waikiki_df, aes(x = date, y = tmax)) +
  geom_point() +
  geom_line(data = central_park_df)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_part_02_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Useful for calculating monthly average, etc

## Patchwork…

``` r
tmax_tmin_plot =
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point() +
  theme(legend.position = "none")

prcp_density_plot = 
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.5) +
  theme(legend.position = "none")

seasonality_plot = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  geom_smooth(se = FALSE) +
  theme(legend.position = "none")

(tmax_tmin_plot + prcp_density_plot) / seasonality_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_part_02_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Data manipulation

``` r
weather_df %>% 
  mutate(name = fct_relevel(name, "Waikiki_HA")) %>% 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_part_02_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
weather_df %>% 
  mutate(name = fct_reorder(name, tmax)) %>% 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_part_02_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

``` r
pulse_df = 
  haven::read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi",
    names_prefix = "bdi_score_"
  ) %>% 
  select(id, visit, everything()) %>% 
  mutate(visit = fct_relevel(visit, "bl"))

pulse_df %>% 
  ggplot(aes(x = visit, y = bdi)) +
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite values (stat_boxplot).

![](viz_part_02_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->
