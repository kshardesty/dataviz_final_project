---
title: "Mini-Project 01"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---

# Data Visualization Project 01



```r
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.2     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
## ✔ purrr     1.0.1     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
# long dataset
bl_1 <- read_csv("data/barro_lee_take_two.csv")
```

```
## Rows: 432 Columns: 4
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): Name, Indicator
## dbl (2): Year, Value
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
# wide dataset with names of indicators of interest
bl_2 <- read_csv("data/barro_lee.csv")
```

```
## Rows: 9 Columns: 50
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## dbl (50): year, BAR.NOED.15UP.FE.ZS, BAR.NOED.15UP.ZS, BAR.NOED.25UP.FE.ZS, ...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
head(bl_1)
```

```
## # A tibble: 6 × 4
##    Year Name                                                     Indicator Value
##   <dbl> <chr>                                                    <chr>     <dbl>
## 1  2010 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  36.6
## 2  2005 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  43.2
## 3  2000 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  50.8
## 4  1995 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  52.2
## 5  1990 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  58.6
## 6  1985 Barro-Lee: Percentage of female population age 15+ with… BAR.NOED…  65.9
```


```r
head(bl_2)
```

```
## # A tibble: 6 × 50
##    year BAR.NOED.15UP.FE.ZS BAR.NOED.15UP.ZS BAR.NOED.25UP.FE.ZS
##   <dbl>               <dbl>            <dbl>               <dbl>
## 1  2010                36.6             25.7                52.0
## 2  2005                43.2             30.5                51.7
## 3  2000                50.8             36.0                58.0
## 4  1995                52.2             38.7                64.7
## 5  1990                58.6             43.8                70.7
## 6  1985                65.9             48.5                76.7
## # ℹ 46 more variables: BAR.NOED.25UP.ZS <dbl>, BAR.POP.15UP.FE <dbl>,
## #   BAR.POP.15UP <dbl>, BAR.POP.25UP.FE <dbl>, BAR.POP.25UP <dbl>,
## #   BAR.PRM.CMPT.15UP.FE.ZS <dbl>, BAR.PRM.CMPT.15UP.ZS <dbl>,
## #   BAR.PRM.CMPT.25UP.FE.ZS <dbl>, BAR.PRM.CMPT.25UP.ZS <dbl>,
## #   BAR.PRM.ICMP.15UP.FE.ZS <dbl>, BAR.PRM.ICMP.15UP.ZS <dbl>,
## #   BAR.PRM.ICMP.25UP.FE.ZS <dbl>, BAR.PRM.ICMP.25UP.ZS <dbl>,
## #   BAR.PRM.SCHL.15UP.FE <dbl>, BAR.PRM.SCHL.15UP <dbl>, …
```

```r
kh_indicators <- colnames(bl_2)
# remove first entry
kh_indicators <- kh_indicators[-1]
# chech the names by "printing" the vector
print(kh_indicators)
```

```
##  [1] "BAR.NOED.15UP.FE.ZS"     "BAR.NOED.15UP.ZS"       
##  [3] "BAR.NOED.25UP.FE.ZS"     "BAR.NOED.25UP.ZS"       
##  [5] "BAR.POP.15UP.FE"         "BAR.POP.15UP"           
##  [7] "BAR.POP.25UP.FE"         "BAR.POP.25UP"           
##  [9] "BAR.PRM.CMPT.15UP.FE.ZS" "BAR.PRM.CMPT.15UP.ZS"   
## [11] "BAR.PRM.CMPT.25UP.FE.ZS" "BAR.PRM.CMPT.25UP.ZS"   
## [13] "BAR.PRM.ICMP.15UP.FE.ZS" "BAR.PRM.ICMP.15UP.ZS"   
## [15] "BAR.PRM.ICMP.25UP.FE.ZS" "BAR.PRM.ICMP.25UP.ZS"   
## [17] "BAR.PRM.SCHL.15UP.FE"    "BAR.PRM.SCHL.15UP"      
## [19] "BAR.PRM.SCHL.25UP.FE"    "BAR.PRM.SCHL.25UP"      
## [21] "BAR.SCHL.15UP.FE"        "BAR.SCHL.15UP"          
## [23] "BAR.SCHL.25UP.FE"        "BAR.SCHL.25UP"          
## [25] "BAR.SEC.CMPT.15UP.FE.ZS" "BAR.SEC.CMPT.15UP.ZS"   
## [27] "BAR.SEC.CMPT.25UP.FE.ZS" "BAR.SEC.CMPT.25UP.ZS"   
## [29] "BAR.SEC.ICMP.15UP.FE.ZS" "BAR.SEC.ICMP.15UP.ZS"   
## [31] "BAR.SEC.ICMP.25UP.FE.ZS" "BAR.SEC.ICMP.25UP.ZS"   
## [33] "BAR.SEC.SCHL.15UP.FE"    "BAR.SEC.SCHL.15UP"      
## [35] "BAR.SEC.SCHL.25UP.FE"    "BAR.SEC.SCHL.25UP"      
## [37] "BAR.TER.CMPT.15UP.FE.ZS" "BAR.TER.CMPT.15UP.ZS"   
## [39] "BAR.TER.CMPT.25UP.FE.ZS" "BAR.TER.CMPT.25UP.ZS"   
## [41] "BAR.TER.ICMP.15UP.FE.ZS" "BAR.TER.ICMP.15UP.ZS"   
## [43] "BAR.TER.ICMP.25UP.FE.ZS" "BAR.TER.ICMP.25UP.ZS"   
## [45] "BAR.TER.SCHL.15UP.FE"    "BAR.TER.SCHL.15UP"      
## [47] "BAR.TER.SCHL.25UP.FE"    "BAR.TER.SCHL.25UP"      
## [49] "SE.PRM.ENRL.TC.ZS"
```

```r
bl_1 %>% 
  filter(Indicator %in% kh_indicators)
```

```
## # A tibble: 432 × 4
##     Year Name                                                    Indicator Value
##    <dbl> <chr>                                                   <chr>     <dbl>
##  1  2010 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  36.6
##  2  2005 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  43.2
##  3  2000 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  50.8
##  4  1995 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  52.2
##  5  1990 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  58.6
##  6  1985 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  65.9
##  7  1980 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  71.6
##  8  1975 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  78.3
##  9  1970 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  83.3
## 10  2010 Barro-Lee: Percentage of population age 15+ with no ed… BAR.NOED…  25.7
## # ℹ 422 more rows
```


```r
# build a vector with the exact names of the indicators of interest
rsa_indicators <- c("BAR.NOED.15UP.FE.ZS", 
                   "BAR.NOED.15UP.ZS", 
                   "BAR.NOED.25UP.FE.ZS", 
                   "BAR.NOED.25UP.ZS")
```


```r
bl_1 %>% 
  filter(Indicator %in% rsa_indicators)
```

```
## # A tibble: 36 × 4
##     Year Name                                                    Indicator Value
##    <dbl> <chr>                                                   <chr>     <dbl>
##  1  2010 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  36.6
##  2  2005 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  43.2
##  3  2000 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  50.8
##  4  1995 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  52.2
##  5  1990 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  58.6
##  6  1985 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  65.9
##  7  1980 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  71.6
##  8  1975 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  78.3
##  9  1970 Barro-Lee: Percentage of female population age 15+ wit… BAR.NOED…  83.3
## 10  2010 Barro-Lee: Percentage of population age 15+ with no ed… BAR.NOED…  25.7
## # ℹ 26 more rows
```


```r
my_data <- bl_1 %>% 
  filter(Indicator %in% rsa_indicators)
```


```r
ggplot(my_data, aes(x = Year, y = Value)) + 
  geom_line(aes(color = Indicator), size=1) + 
  scale_color_manual (name = "", values = c("#CC0066", "#0045ff", "#8900ff", "#0cbd18"),  labels = c("Female Age 15+", "All Age 15+", "Female Age 25+", "All Age 25+")) +
  labs(x = "Year",
       y = "Percentage of Pop. With No Education",
       caption = "Source: The World Bank",
       title = "Haiti Barro-Lee Indicators for No Education") +
  theme_minimal() +
theme(legend.position = "bottom")
```

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](hardesty_project_01_files/figure-html/unnamed-chunk-9-1.png)<!-- -->


```r
female_indicators <- c("BAR.PRM.ICMP.15UP.FE.ZS", 
                   "BAR.SEC.ICMP.15UP.FE.ZS", 
                   "BAR.TER.ICMP.15UP.FE.ZS")
```


```r
my_data_female <- bl_1 %>% 
  filter(Indicator %in% female_indicators)
```


```r
ggplot(my_data_female, aes(x = Year, y = Value)) + 
  geom_line(aes(color = Indicator), size=1) + 
  scale_color_manual (name = "", values = c("#CC0066", "#0045ff", "#0cbd18"), labels = c("Some Primary Schooling", "Some Secondary Schooling", "Some Tertiary Schooling")) +
 theme_minimal() +
   theme(legend.text = element_text(size=9, 
                                     face="bold")) +
  labs(x = "Year",
       y = "Percentage of Female Population 15+",
       caption = "Source: The World Bank",
       title = "Haiti Barro-Lee Indicators for Some Education") +
theme(legend.position = "bottom")
```

![](hardesty_project_01_files/figure-html/unnamed-chunk-12-1.png)<!-- -->


```r
 average_indicators <- c("BAR.SCHL.15UP", "BAR.SEC.SCHL.15UP")
```


```r
my_data_average <- bl_1 %>% 
  filter(Indicator %in% average_indicators)
```



```r
ggplot(my_data_average, aes(x=Year, y=Value, fill=Indicator)) +
         geom_bar(stat="identity") +
  scale_fill_manual(name = "", values=c("#fc5394", "#acfc60"), labels = c("Years of Primary Schooling", "Years of Secondary Schooling")) +
  geom_text(aes(label=Value), vjust=1.5, colour="black", size=2.5) +
labs(x = "Year",
       y = "Average Years of Schooling for All Population 15+",
       caption = "Source: The World Bank",
       title = "Haiti Barro-Lee Indicators for Average Years of Education") +
  theme_minimal() +
  theme(legend.position = "bottom")
```

![](hardesty_project_01_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

# Report: Haiti Barro-Lee Education Indicators Exploration

My dataset is based on the Barro-Lee Dataset, which "gives a comprehensive picture of the distribution of education attainment and human capital among age groups and genders in 144 countries from 1970" (The World Bank). The dataset tracks educational attainment of each country's total population and female population using 5-year age groups and eight indicators: No Education, Completed Primary, Incomplete and Completed Primary, Completed Secondary, Incomplete and Completed Secondary, Completed Tertiary, Incomplete and Completed Tertiary, and Average Years of Schooling. Specifically, I examined the dataset for the country of Haiti. 

I found my original dataset on The Humanitarian Data Exchange, which included the Barro-Lee indicators from 1970-2010, as well as a great deal of additional data related to Haiti's education system and other demographics. From this original dataset, I narrowed down my focus by creating CSV files in long form and wide form that included only data for all Barro-Lee indicators and only for the 15+ and 25+ age groups for both total population and female population.  

## Data Exploration

I was primarily interested in exploring the question of changing access to education over time in Haiti. I was particularly interested in whether discrepancies were apparent in access between students identified as female and the rest of the population. Because I was working with a fairly straightforward, time-based dataset, I knew my data would be ideal for visuals that track trends over time (e.g., line graphs). However, I had a great deal of difficulty figuring out how to "wrangle" the data and gather specific data points together, such as specific indicators, to generate my visualizations. With Dr. Sanchez-Arias' assistance, I learned how to filter the long dataset and create appropriate data frames using the specific indicators I wanted to focus on, as appears in the code "story" above.

## Data Stories

I generated three visualizations, two line graphs and one bar chart, to explore some of the data. The level of sophistication in these graphics was definitely limited by my ability in R. However, I do see some data stories emerging. The first line graph indicates clearly that the percentage of Haiti's population with no education at all has declined considerably from 1970 to 2010, suggesting that citizens now have greater access to at least some education. For both age groups considered, female students do appear to lag behind total population. 

In the second line graph, I examined the percentage of the female population age 15+ who had completed at least some primary, secondary, or tertiary education but had not graduated. The trend lines suggest that female students have had increasing access to education over time for primary and (to a lesser degree) secondary education, but they have had almost no tertiary education regardless of year. The dip in primary school completion around 2000 is also interesting and made me wonder what factor(s) contributed to the decline.

The bar graph considers average years of education in primary and secondary schooling for the total population age 15+. Again, we see some expanding access to education over time for primary and (to a lesser degree) secondary education. I originally included tertiary schooling in the plot, but it barely registered on the chart. This suggests a significant barrier to tertiary education in Haiti for all citizens regardless of gender. It would be interesting to compare this data for average years of schooling to other Caribbean countries. Unfortunately, I suspect that Haiti's statistics are still relatively low when compared both regionally and globally.

## Application of Design Principles

Again, my ability to apply appropriate data visualization principles was significantly limited by my knowledge of R thus far. However, I experimented quite a bit with the application of color. While I explored a number of potential color palettes, I ultimately preferred to pick my own colors using the hex codes. I was going for a bold color scheme, and in particular I wanted the lines to be easily distinguishable from each other. I increased the line size for visibility. I had also experimented with adding different line types (e.g., dashed, dotted), but then I decided it wasn't necessary to the communication goals. I learned how to apply the various labels and manually make changes to the legends. I also figured out how to add labels to the columns of the bar chart to support readability.


### **References**

Humanitarian Data Exchange. Haiti - Education. United Nations Office for the Coordination of Humanitarian Affairs. https://data.humdata.org/dataset/world-bank-education-indicators-for-haiti

Education Statistics (EdStats). Barro-Lee Dataset. The World Bank.  https://datatopics.worldbank.org/education/wProjection/bpopmodel
