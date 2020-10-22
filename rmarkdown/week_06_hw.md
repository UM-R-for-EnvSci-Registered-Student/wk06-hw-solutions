---
title: "Week 06 - Homework"
author: "Jose Luis Rodriguez Gil"
date: "21/10/2020"
output: 
  html_document:
    keep_md: true
  
---





# Data loading

First we load both datasets that we will be using for this assignment


```r
decapod <- read_delim(here("data", "decapod.txt"), delim = "\t")
```

```
## Parsed with column specification:
## cols(
##   .default = col_double()
## )
```

```
## See spec(...) for full column specifications.
```

```r
sst_data <- read_csv(here("data", "sst_data.csv"))
```

```
## Parsed with column specification:
## cols(
##   year = col_double(),
##   station_a = col_double(),
##   station_b = col_double(),
##   station_c = col_double()
## )
```

## The data

All data for this assignment is directly taken or modified from the Zur et al (2007) book [1](https://login.uml.idm.oclc.org/login?url=https://www.springer.com/gp/book/9780387459677)

For the first 3 figures we will use a dataset consisting of species counts for a number of decapod species (those starting with "f_") measured in 45 samples. Abiotic data for those sampling locations is also provided (eg, the temperature and salinity at 1m: t1m, s1m)

For the fourth figure, we will use a dataset of mean annual sea surface temperatures (sst) collected at three different stations.


# Figure one - Species counts with total counts per site

For this figure we will use the `geom_col()` with its default `possition = "stack"` behaviour to plot the species counts for the first 14 samples in the data set, showing the total counts for each sample.

First we will do a bit of clean-up and pivoting of the decapod data. As usual we want to pivot our species columns into longer form without touching all other columns relating to specific info about the site (e.g. sample, t1m, s1m, etc.). 

Because the samples are labeled as numbers, R will believe they are a numeric variable, but they are not, they are individual and distinct samples, so we will make sure to make this a factor.

For this plot we will be plotting only data for the first 14 samples, so we will have to filter out the rest.

For the fill of the columns we will use the viridis palette, so remember to load the `{viridis}` package. And remember we are ploting a discrete variable, so make sure to tell viridis about it!


```r
decapod %>% 
  clean_names() %>% 
  pivot_longer(cols = c(-sample, -t1m, -t45_35m, -s1m, -s45_35m, -ch0_10m, -year, - location),
               names_to = "species",
               values_to = "counts") %>%
  filter(sample <= 14) %>% 
  mutate(sample = as.factor(sample)) %>% 
  ggplot() +
  geom_col(aes(x = sample, y = counts, fill = species), possition = "stack") +
  scale_y_continuous(expand = expansion(mult = 0, add = 0)) +
  scale_fill_viridis(discrete = TRUE) +
  theme_bw()
```

```
## Warning: Ignoring unknown parameters: possition
```

![](week_06_hw_files/figure-html/figure_1-1.png)<!-- -->

# Figure two - Species counts with only proportions

For this figure we are going to do, exactly the same, but we are going to change the position  option within the `geom_col()` to 'position = "fill"`


```r
decapod %>% 
  clean_names() %>% 
  pivot_longer(cols = c(-sample, -t1m, -t45_35m, -s1m, -s45_35m, -ch0_10m, -year, - location),
               names_to = "species",
               values_to = "counts") %>%
  filter(sample <= 14) %>% 
  mutate(sample = as.factor(sample)) %>% 
  ggplot() +
  geom_col(aes(x = sample, y = counts, fill = species), position = "fill") +
  scale_y_continuous(expand = expansion(mult = 0, add = 0)) +
  scale_fill_viridis(discrete = TRUE) +
  theme_bw()
```

![](week_06_hw_files/figure-html/figure_2-1.png)<!-- -->

# Figure three - Ridgeplots of salinit at 1m for two years

For this figure we will be using the `{ggridge}` package to create a density-ridge plot showing the salinity concentration at 1 m (variable named "s1m") at the two sampled locations. We will make a two-pannel figure showing each of the sampled years.

Again, we will use the viridis palette


```r
decapod %>% 
  clean_names() %>% 
  pivot_longer(cols = c(-sample, -t1m, -t45_35m, -s1m, -s45_35m, -ch0_10m, -year, - location),
               names_to = "species",
               values_to = "counts") %>% 
  mutate(sample = as.factor(sample),
         location = as.factor(location)) %>% 
  ggplot() +
  facet_wrap(~year) +
  geom_density_ridges(aes(x = s1m , y = location, fill = location), alpha = 0.6) +
  scale_fill_viridis(discrete = TRUE) +
  theme_classic()
```

```
## Picking joint bandwidth of 0.0743
```

```
## Picking joint bandwidth of 0.0611
```

![](week_06_hw_files/figure-html/figure_3-1.png)<!-- -->

## Figure 4 - Sea Surface Temperature time series

Using the sea surface temperature data, we are going to generate a time series plot showing the three different stations in three colours from the Viridis palette.


```r
sst_data %>% 
  pivot_longer(cols = -year, names_to = "station", values_to = "temperature") %>% 
  ggplot() +
  geom_line(aes(x = year, y = temperature, colour = station)) +
  geom_vline(aes(xintercept = 1965), linetype = "dotdash") +
  scale_colour_viridis(discrete = TRUE) +
  theme_minimal()
```

![](week_06_hw_files/figure-html/figure_4-1.png)<!-- -->
