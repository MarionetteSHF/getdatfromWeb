data from web
================
hanfu shi
2021/10/19

\#Setting

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.4     v dplyr   1.0.7
    ## v tidyr   1.1.3     v stringr 1.4.0
    ## v readr   2.0.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(rvest)
```

    ## 
    ## 载入程辑包：'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = 0.6,
  out.width = "90%"
)
theme_set(theme_minimal() + theme(legend.position = "bottom"))
options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)
scale_colour_discrete = scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

\#Scrape a table I want the first table from[this
page](http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm)

``` r
url="http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

extract the table(s):focus on the first one

``` r
tabl_marj=
drug_use_html %>%
  html_nodes(css = "table")%>%
  first()%>%
  html_table()%>%
  slice(-1)%>%
  as_tibble()
```

## star wars movies info

I want the data from[here](https://www.imdb.com/list/ls070150896/)

``` r
url = "https://www.imdb.com/list/ls070150896/"

sw_html =read_html(url)
```

Grab elements that I want

``` r
title_vec = 
  sw_html %>%
  html_nodes(css = ".lister-item-header a") %>%
  html_text()
  
gross_rev_vec =
  sw_html %>%
  html_nodes(css= ".text-muted .ghost~ .text-muted+ span")%>%
  html_text()
gross_rev_vec
```

    ## [1] "$474.54M" "$310.68M" "$380.26M" "$322.74M" "$290.48M" "$309.13M" "$936.66M"
    ## [8] "$620.18M" "$515.20M"

``` r
runtime_vec =
  sw_html %>%
  html_nodes(css = ".runtime")%>%
  html_text()

swf_df =
  tibble(
    title= title_vec,
    gross_rev = gross_rev_vec,
    runtime = runtime_vec
  )
```

\#get NY water data from API usE CSV FILE!!!!!!!!

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv")%>%
  content("parsed")
```

    ## Rows: 42 Columns: 4

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
#if only has json file
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json")%>%
  content("text")%>%
  jsonlite::fromJSON()%>%
  as_tibble()
```

\#\#BRFSS same process API may have its own parameter

``` r
nrfss_2010 =
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000))%>%
  content("parsed")
```

    ## Rows: 5000 Columns: 23

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.
