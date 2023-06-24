---
title: "Data Visualization for Exploratory Data Analysis"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---

# Data Visualization Project 03


In this exercise you will explore methods to create different types of data visualizations (such as plotting text data, or exploring the distributions of continuous variables).


## PART 1: Density Plots

Using the dataset obtained from FSU's [Florida Climate Center](https://climatecenter.fsu.edu/climate-data-access-tools/downloadable-data), for a station at Tampa International Airport (TPA) for 2022, attempt to recreate the charts shown below which were generated using data from 2016. You can read the 2022 dataset using the code below: 


```r
library(tidyverse)
library(ggridges)
library(tidytext)
weather_tpa <- read_csv("https://raw.githubusercontent.com/reisanar/datasets/master/tpa_weather_2022.csv")
# random sample 
sample_n(weather_tpa, 4)
```

```
## # A tibble: 4 × 7
##    year month   day precipitation max_temp min_temp ave_temp
##   <dbl> <dbl> <dbl>         <dbl>    <dbl>    <dbl>    <dbl>
## 1  2022    12    15          1.09       76       64     70  
## 2  2022     4    18          0          91       73     82  
## 3  2022     4    21          0          87       67     77  
## 4  2022     6    22          0          92       77     84.5
```

See https://www.reisanar.com/slides/relationships-models#10 for a reminder on how to use this type of dataset with the `lubridate` package for dates and times (example included in the slides uses data from 2016).

Using the 2022 data: 

(a) Create a plot like the one below:

<img src="https://github.com/reisanar/figs/raw/master/tpa_max_temps_facet.png" width="80%" style="display: block; margin: auto;" />

Hint: the option `binwidth = 3` was used with the `geom_histogram()` function.



```r
library(lubridate)
tpa_clean <- weather_tpa %>% 
  mutate(month = month(month, label = TRUE, abbr = FALSE),
         day = wday(day, label = TRUE, abbr = FALSE))
```





```r
ggplot(tpa_clean, aes(x = max_temp, fill = month)) +
  geom_histogram(binwidth = 3, color = "white", boundary = 2) +
  guides(fill = "none") +
  facet_wrap(vars(month)) +
  theme_bw() +
  labs(x = "Maximum Temperatures", y = "Number of Days")
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-4-1.png)<!-- -->


(b) Create a plot like the one below:

<img src="https://github.com/reisanar/figs/raw/master/tpa_max_temps_density.png" width="80%" style="display: block; margin: auto;" />

Hint: check the `kernel` parameter of the `geom_density()` function, and use `bw = 0.5`.


```r
ggplot(tpa_clean, aes(x = max_temp)) +
  geom_density(color = "grey20", fill = "grey50",
               bw = 0.5, kernel = "epanechnikov")+
  theme_minimal() +
   labs(x = "Maximum temperature")
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


(c) Create a plot like the one below:

<img src="https://github.com/reisanar/figs/raw/master/tpa_max_temps_density_facet.png" width="80%" style="display: block; margin: auto;" />

Hint: default options for `geom_density()` were used. 


```r
ggplot(tpa_clean, aes(x = max_temp, fill = month)) +
  geom_density() +
  guides(fill = "none") +
  facet_wrap(vars(month)) +
  theme_bw() +
   labs(x = "Maximum temperature", y = "", title = "Density plots for each month in 2022")
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-8-1.png)<!-- -->


(d) Generate a plot like the chart below:


<img src="https://github.com/reisanar/figs/raw/master/tpa_max_temps_ridges_plasma.png" width="80%" style="display: block; margin: auto;" />

Hint: use the`{ggridges}` package, and the `geom_density_ridges()` function paying close attention to the `quantile_lines` and `quantiles` parameters. The plot above uses the `plasma` option (color scale) for the _viridis_ palette.


```r
ggplot(tpa_clean, aes(x = max_temp, y = month, fill = after_stat(x))) +
  geom_density_ridges_gradient(quantile_lines = TRUE, quantiles = 2) +
  scale_fill_viridis_c(option = "plasma", name = "") +
  labs(x = "Maximum temperature (in Fahrenheit degrees)", y = NULL, color = "Temp") +
  theme_minimal()
```

```
## Picking joint bandwidth of 1.93
```

```
## Warning: Using the `size` aesthetic with geom_segment was deprecated in ggplot2 3.4.0.
## ℹ Please use the `linewidth` aesthetic instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-10-1.png)<!-- -->



(e) Create a plot of your choice that uses the attribute for precipitation _(values of -99.9 for temperature or -99.99 for precipitation represent missing data)_.


```r
ggplot(data=tpa_clean,
      aes(precipitation, month, fill = month)) +
      geom_bar(stat="identity") +
  labs(x = "Precipitation in Inches", y = "Month", title = "Total Precipitation by Month in Tampa for 2022", caption = "Source: FSU Florida Climate Center") +
  theme_minimal() +
  theme(legend.position = "none")
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


## PART 2 

> **You can choose to work on either Option (A) or Option (B)**. Remove from this template the option you decided not to work on. 


### Option (A): Visualizing Text Data

Review the set of slides (and additional resources linked in it) for visualizing text data: https://www.reisanar.com/slides/text-viz#1

Choose any dataset with text data, and create at least one visualization with it. For example, you can create a frequency count of most used bigrams, a sentiment analysis of the text data, a network visualization of terms commonly used together, and/or a visualization of a topic modeling approach to the problem of identifying words/documents associated to different topics in the text data you decide to use. 

Make sure to include a copy of the dataset in the `data/` folder, and reference your sources if different from the ones listed below:

- [Billboard Top 100 Lyrics](https://github.com/reisanar/datasets/blob/master/BB_top100_2015.csv)

- [RateMyProfessors comments](https://github.com/reisanar/datasets/blob/master/rmp_wit_comments.csv)

- [FL Poly News Articles](https://github.com/reisanar/datasets/blob/master/flpoly_news_SP23.csv)


(to get the "raw" data from any of the links listed above, simply click on the `raw` button of the GitHub page and copy the URL to be able to read it in your computer using the `read_csv()` function)


```r
lyrics_2015 <- read_csv("https://raw.githubusercontent.com/reisanar/datasets/master/BB_top100_2015.csv")
```

```
## Rows: 100 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (3): Song, Artist, Lyrics
## dbl (3): Rank, Year, Source
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
lyrics_2015 <- select(lyrics_2015, -Year, -Source)
```


```r
lyrics_2015 %>% 
  filter(Rank %in% 1:10)
```

```
## # A tibble: 10 × 4
##     Rank Song              Artist                             Lyrics            
##    <dbl> <chr>             <chr>                              <chr>             
##  1     1 uptown funk       mark ronson featuring bruno mars   this hit that ice…
##  2     2 thinking out loud ed sheeran                         when your legs do…
##  3     3 see you again     wiz khalifa featuring charlie puth its been a long d…
##  4     4 trap queen        fetty wap                          im like hey wassu…
##  5     5 sugar             maroon 5                           im hurting baby i…
##  6     6 shut up and dance walk the moon                      oh dont you dare …
##  7     7 blank space       taylor swift                       nice to meet you …
##  8     8 watch me          silento                            now watch me whip…
##  9     9 earned it         the weeknd                         you make it look …
## 10    10 the hills         the weeknd                         your man on the r…
```


```r
lyrics_2015 %>% 
  filter(Rank %in% 1:10) %>% 
  unnest_tokens(word, Lyrics)
```

```
## # A tibble: 4,413 × 4
##     Rank Song        Artist                           word    
##    <dbl> <chr>       <chr>                            <chr>   
##  1     1 uptown funk mark ronson featuring bruno mars this    
##  2     1 uptown funk mark ronson featuring bruno mars hit     
##  3     1 uptown funk mark ronson featuring bruno mars that    
##  4     1 uptown funk mark ronson featuring bruno mars ice     
##  5     1 uptown funk mark ronson featuring bruno mars cold    
##  6     1 uptown funk mark ronson featuring bruno mars michelle
##  7     1 uptown funk mark ronson featuring bruno mars pfeiffer
##  8     1 uptown funk mark ronson featuring bruno mars that    
##  9     1 uptown funk mark ronson featuring bruno mars white   
## 10     1 uptown funk mark ronson featuring bruno mars gold    
## # ℹ 4,403 more rows
```


```r
lyrics_2015 %>%
  filter(Rank %in% 1:10) %>%
  unnest_tokens(word, Lyrics, token = "words") %>%
  filter(!word %in% stop_words$word, str_detect(word, "[a-z]"))
```

```
## # A tibble: 1,777 × 4
##     Rank Song        Artist                           word    
##    <dbl> <chr>       <chr>                            <chr>   
##  1     1 uptown funk mark ronson featuring bruno mars hit     
##  2     1 uptown funk mark ronson featuring bruno mars ice     
##  3     1 uptown funk mark ronson featuring bruno mars cold    
##  4     1 uptown funk mark ronson featuring bruno mars michelle
##  5     1 uptown funk mark ronson featuring bruno mars pfeiffer
##  6     1 uptown funk mark ronson featuring bruno mars white   
##  7     1 uptown funk mark ronson featuring bruno mars gold    
##  8     1 uptown funk mark ronson featuring bruno mars hood    
##  9     1 uptown funk mark ronson featuring bruno mars girls   
## 10     1 uptown funk mark ronson featuring bruno mars girls   
## # ℹ 1,767 more rows
```


```r
bb_top_10 <- lyrics_2015 %>%
  filter(Rank %in% 1:10) %>%
  unnest_tokens(word, 
                Lyrics, 
                token = "words") %>%
  filter(!word %in% stop_words$word, 
         str_detect(word, "[a-z]"))
```


```r
bb_top_10 %>% 
  group_by(word) %>% 
  summarise(uses = n()) %>% 
  arrange(desc(uses)) %>% 
  head(10)
```

```
## # A tibble: 10 × 2
##    word    uses
##    <chr>  <int>
##  1 watch     98
##  2 im        55
##  3 love      43
##  4 dont      38
##  5 ooh       33
##  6 funk      31
##  7 bop       30
##  8 uptown    29
##  9 baby      26
## 10 nae       24
```

```r
bb_top_10 %>% 
  group_by(word) %>% 
  summarise(uses = n()) %>% 
  arrange(desc(uses)) %>% 
  slice(1:15) %>% 
  ggplot() + 
  geom_bar(aes(x = word, y = uses, fill = word), 
           stat = "identity") + 
  coord_flip() + 
  scale_x_discrete(breaks=c("youre","yeah","whip", "watch", "uptown", "ooh", "nae", "love", "im", "ill", "girl", "funk", "dont", "bop", "baby"),
        labels=c("You're","Yeah","Whip", "Watch", "Uptown", "Ooh", "Nae", "Love", "I'm", "I'll", "Girl", "Funk", "Don't", "Bop", "Baby")) +
   labs(x = "Word", y = "Number of Uses", title = "Most Used Words in Billboard Top 10 Songs for 2015", subtitle = "(1. Uptown Funk, 2. Thinking Out Loud, 3. See You Again, 4. Trap Queen, 5. Sugar, \n 6. Shut Up and Dance, 7. Blank Space, 8. Watch Me, 9. Earned It, 10. The Hills)", caption = "Source: Billboard Top 100, 2015") +
     theme_minimal() +
  theme(legend.position = "none")
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

```r
bb_top_10 %>%
  inner_join(get_sentiments("bing")) %>%
  count(Song, sentiment) %>%
  spread(sentiment, n, fill = 0) %>%
  mutate(sentiment = positive - negative)
```

```
## Joining with `by = join_by(word)`
```

```
## # A tibble: 10 × 4
##    Song              negative positive sentiment
##    <chr>                <dbl>    <dbl>     <dbl>
##  1 blank space             34       20       -14
##  2 earned it               10       17         7
##  3 see you again            4        5         1
##  4 shut up and dance        1        5         4
##  5 sugar                   11       19         8
##  6 the hills                3        9         6
##  7 thinking out loud       11       18         7
##  8 trap queen              18        8       -10
##  9 uptown funk             16       23         7
## 10 watch me                14        0       -14
```


```r
bb_top_10 %>%
  inner_join(get_sentiments("bing")) %>%
  count(Song, sentiment) %>%
  spread(sentiment, n, fill = 0) %>%
  mutate(sentiment = positive - negative) %>% 
  ggplot() + 
  geom_bar(aes(x = reorder(Song, sentiment), 
               y = sentiment, fill = Song), 
           stat = "identity") + 
  coord_flip() + 
  scale_x_discrete(breaks=c("sugar","uptown funk","thinking out loud", "earned it", "the hills", "shut up and dance", "see you again", "trap queen", "watch me", "blank space"),
        labels=c("Sugar","Uptown Funk","Thinking Out Loud", "Earned It", "The Hills", "Shut Up and Dance", "See You Again", "Trap Queen", "Watch Me", "Blank Space")) +
  labs(x = "", y = "Sentiment", title = "Sentiment Analysis of Billboard Top 10 Songs in 2015", subtitle = "(Using Bing Lexicon)", caption = "Source: Billboard Top 100, 2015") + 
  theme_minimal() +
  theme(legend.position = "none")
```

```
## Joining with `by = join_by(word)`
```

![](hardesty_project_03_files/figure-html/unnamed-chunk-20-1.png)<!-- -->
