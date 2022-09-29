Visualization I
================
Elaine Yanxi Chen
2022-09-29

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

Let’s make a scatterplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Let’s make the same scatterplot but different.

``` r
weather_df %>%
  drop_na() %>% 
  filter(name == "CentralPark_NY") %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

![](template_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Can save ggplot objects and continue to print or edit. Can have a base
figure and have different type of figures superimpose on top of it. Save
and export explicitly.

``` r
weather_scatterplot =
  weather_df %>%
  drop_na() %>% 
  ggplot(aes(x = tmin, y = tmax)) 

weather_scatterplot +
  geom_point()
```

![](template_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Let’s fancy this up a bit

Want colour: mapping variable to aesthetics

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Can define aesthetics mapping for specific geometry.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Add in a new aesthetics.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

But this only gives one smooth line for all three different groups. We
can change that.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

alpha is how transparent the points are. `se = FALSE` removes the error
bars from the plot.

Maybe make separate panels.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

`tmax` vs `tmin` is boring, let’s spice it up some.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, colour = name)) + 
  geom_point(aes(size = prcp), alpha = 0.3) +
  geom_smooth(se = FALSE) +
 facet_grid(. ~ name) +
 theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](template_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Last line from google stackoverflow to rotate x-axis labels.

## Some quick stuff

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](template_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Using hexagons instead of points when you have lots of rows, better for
visualization for huge dataset.

## Univariate plots …

Histograms, barplots, boxplots, violins, …

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) +
  geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](template_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

In histograms colour is the outside of the bar. Use fill more.

Let’s see more options!

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) +
  geom_density(alpha = 0.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](template_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

Boxplots?

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](template_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Violin plots?

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](template_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

With multiple comparisons, e.g. 20 - 30 distributions to stack up

OR

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](template_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->
