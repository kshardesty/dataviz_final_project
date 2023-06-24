---
title: "Mini-Project 02"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---

# Data Visualization Project 02

## Spatial Visualization


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
library(plotly)
```

```
## 
## Attaching package: 'plotly'
## 
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following object is masked from 'package:graphics':
## 
##     layout
```

```r
library(htmlwidgets)
library(broom)
# Load and clean internet user data
poverty <- read_csv("data/Poverty.csv") %>%
  # Rename country code column to ISO_A3 so it matches what's in the Natural Earth shapefile
  rename(users = `Ratio`,
         ISO_A3 = Code)
```

```
## Rows: 266 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): Country Name, Code, Indicator Name, Indicator Code
## dbl (2): Year, Ratio
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
library(sf)
# Load world shapefile from Natural Earth
# https://www.naturalearthdata.com/downloads/110m-cultural-vectors/
world_shapes <- read_sf("data/ne_110m_admin_0_countries/ne_110m_admin_0_countries.shp")
```


```r
poverty_2018 <- poverty %>%
  filter(Year == 2018)

poverty_map <- world_shapes %>%
  left_join(poverty_2018, by = "ISO_A3") %>%
  filter(ISO_A3 != "ATA")  
```


```r
 ggplot() +
    geom_sf(data = poverty_map, aes(fill = users)) +
  scale_fill_viridis_c(option = "plasma") +
  labs(fill = "Percent of Population") +
  theme_void() +
  theme(legend.position = "bottom") +
  labs(caption = "Source: The World Bank", title = "Share of Population Living in Extreme Poverty, 2018",  subtitle = "(Defined as living below the International Poverty Line of $2.15 per day. Grey represents missing data.)")
```

![](hardesty_project_02_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

## Interactive Plot


```r
poverty_indicators <- read_csv("data/poverty_indicators.csv")
```

```
## Rows: 41 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (3): Country, Code, Region
## dbl (3): Year, Gini, Slums
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```



```r
my_plot <- ggplot(
  data = poverty_indicators,
  mapping = aes(x = Gini, y = Slums, 
                color = Region, label = Country)) +
  geom_point(size=3) +
  theme_minimal() +
  scale_color_viridis_d(option = "plasma", name = "") +
  labs(x = "Gini Index",
       y = "Percentage of Population Living in Slums",
       title = "Comparison of Income Inequality and Slums, 2018", subtitle = "Based on global World Bank indicators") 
```


```r
ggplotly(my_plot, tooltip = c("Region", "Country"))  %>% 
  layout(legend = list(orientation = "h", x = 0.15, y = -0.2)) %>%
  layout(title = list(text = paste0('Comparison of Income Inequality and Slums, 2018',
                                    '<br>',
                                    '<sup>',
                                    'Based on global World Bank indicators',
                                    '</sup>')))
```

```{=html}
<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-87743f4dde80d07415bc" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-87743f4dde80d07415bc">{"x":{"data":[{"x":[51.3,37.8,47.3,37.2,29.6,34.8,36.1,37.3,35.1,38.1,35.7,37.5,42.4,40.5],"y":[62.6,68.3,32.1,53.2,48.2,64.4,46.1,70.4,51.5,35.2,53.4,82,40.1,43],"text":["Region: Africa<br />Country: Angola","Region: Africa<br />Country: Benin","Region: Africa<br />Country: Burkina Faso","Region: Africa<br />Country: Cote d'Ivoire","Region: Africa<br />Country: Guinea","Region: Africa<br />Country: Guinea-Bissau","Region: Africa<br />Country: Mali","Region: Africa<br />Country: Niger","Region: Africa<br />Country: Nigeria","Region: Africa<br />Country: Senegal","Region: Africa<br />Country: Sierra Leone","Region: Africa<br />Country: Chad","Region: Africa<br />Country: Togo","Region: Africa<br />Country: Tanzania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(13,8,135,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(13,8,135,1)"}},"hoveron":"points","name":"Africa","legendgroup":"Africa","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[38.4,34.6,27.8,27.7,38.8,32.7,29.6,42.3,36.4,35.7],"y":[20.2,49,0.8,6.9,23.6,21.9,57.5,37.3,7.8,5.8],"text":["Region: Asia<br />Country: Indonesia","Region: Asia<br />Country: India","Region: Asia<br />Country: Kazakhstan","Region: Asia<br />Country: Kyrgyz Republic","Region: Asia<br />Country: Lao PDR","Region: Asia<br />Country: Mongolia","Region: Asia<br />Country: Pakistan","Region: Asia<br />Country: Philippines","Region: Asia<br />Country: Thailand","Region: Asia<br />Country: Vietnam"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(156,23,158,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(156,23,158,1)"}},"hoveron":"points","name":"Asia","legendgroup":"Asia","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[30.1,34.4,25.2,36.4,30.6,25.7,36.8,41.9,26.1],"y":[5.3,8.4,3.8,7.8,8.5,6.5,10,14.1,1.1],"text":["Region: Europe<br />Country: Albania","Region: Europe<br />Country: Armenia","Region: Europe<br />Country: Belarus","Region: Europe<br />Country: Georgia","Region: Europe<br />Country: Ireland","Region: Europe<br />Country: Moldova","Region: Europe<br />Country: Montenegro","Region: Europe<br />Country: Turkiye","Region: Europe<br />Country: Ukraine"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(237,121,83,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(237,121,83,1)"}},"hoveron":"points","name":"Europe","legendgroup":"Europe","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.4,48,43.7,48.9,46.7,49.2,46,38.6],"y":[9.7,4.5,11.2,31.5,17.6,16.3,15.1,16.5],"text":["Region: Latin America and Caribbean<br />Country: Colombia","Region: Latin America and Caribbean<br />Country: Costa Rica","Region: Latin America and Caribbean<br />Country: Dominican Republic","Region: Latin America and Caribbean<br />Country: Honduras","Region: Latin America and Caribbean<br />Country: Mexico","Region: Latin America and Caribbean<br />Country: Panama","Region: Latin America and Caribbean<br />Country: Paraguay","Region: Latin America and Caribbean<br />Country: El Salvador"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(240,249,33,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(240,249,33,1)"}},"hoveron":"points","name":"Latin America and Caribbean","legendgroup":"Latin America and Caribbean","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":37.2602739726027},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Comparison of Income Inequality and Slums, 2018<br><sup>Based on global World Bank indicators<\/sup>","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[23.895,52.605],"tickmode":"array","ticktext":["25","30","35","40","45","50"],"tickvals":[25,30,35,40,45,50],"categoryorder":"array","categoryarray":["25","30","35","40","45","50"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"Gini Index","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-3.26,86.06],"tickmode":"array","ticktext":["0","20","40","60","80"],"tickvals":[0,20,40,60,80],"categoryorder":"array","categoryarray":["0","20","40","60","80"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Percentage of Population Living in Slums","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"title":{"text":"","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"orientation":"h","x":0.15,"y":-0.2},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"11198328a7d71":{"x":{},"y":{},"colour":{},"label":{},"type":"scatter"}},"cur_data":"11198328a7d71","visdat":{"11198328a7d71":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```


```r
interactive_plot <- ggplotly(
  my_plot, tooltip = c("Region", "Country"))  %>% 
  layout(legend = list(orientation = "h", x = 0.375, y = -0.2)) %>%
  layout(title = list(text = paste0('Comparison of Income Inequality and Slums, 2018',
                                    '<br>',
                                    '<sup>',
                                    'Based on global World Bank indicators',
                                    '</sup>')))
interactive_plot
```

```{=html}
<div class="plotly html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-27577f4a77364a500040" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-27577f4a77364a500040">{"x":{"data":[{"x":[51.3,37.8,47.3,37.2,29.6,34.8,36.1,37.3,35.1,38.1,35.7,37.5,42.4,40.5],"y":[62.6,68.3,32.1,53.2,48.2,64.4,46.1,70.4,51.5,35.2,53.4,82,40.1,43],"text":["Region: Africa<br />Country: Angola","Region: Africa<br />Country: Benin","Region: Africa<br />Country: Burkina Faso","Region: Africa<br />Country: Cote d'Ivoire","Region: Africa<br />Country: Guinea","Region: Africa<br />Country: Guinea-Bissau","Region: Africa<br />Country: Mali","Region: Africa<br />Country: Niger","Region: Africa<br />Country: Nigeria","Region: Africa<br />Country: Senegal","Region: Africa<br />Country: Sierra Leone","Region: Africa<br />Country: Chad","Region: Africa<br />Country: Togo","Region: Africa<br />Country: Tanzania"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(13,8,135,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(13,8,135,1)"}},"hoveron":"points","name":"Africa","legendgroup":"Africa","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[38.4,34.6,27.8,27.7,38.8,32.7,29.6,42.3,36.4,35.7],"y":[20.2,49,0.8,6.9,23.6,21.9,57.5,37.3,7.8,5.8],"text":["Region: Asia<br />Country: Indonesia","Region: Asia<br />Country: India","Region: Asia<br />Country: Kazakhstan","Region: Asia<br />Country: Kyrgyz Republic","Region: Asia<br />Country: Lao PDR","Region: Asia<br />Country: Mongolia","Region: Asia<br />Country: Pakistan","Region: Asia<br />Country: Philippines","Region: Asia<br />Country: Thailand","Region: Asia<br />Country: Vietnam"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(156,23,158,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(156,23,158,1)"}},"hoveron":"points","name":"Asia","legendgroup":"Asia","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[30.1,34.4,25.2,36.4,30.6,25.7,36.8,41.9,26.1],"y":[5.3,8.4,3.8,7.8,8.5,6.5,10,14.1,1.1],"text":["Region: Europe<br />Country: Albania","Region: Europe<br />Country: Armenia","Region: Europe<br />Country: Belarus","Region: Europe<br />Country: Georgia","Region: Europe<br />Country: Ireland","Region: Europe<br />Country: Moldova","Region: Europe<br />Country: Montenegro","Region: Europe<br />Country: Turkiye","Region: Europe<br />Country: Ukraine"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(237,121,83,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(237,121,83,1)"}},"hoveron":"points","name":"Europe","legendgroup":"Europe","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[50.4,48,43.7,48.9,46.7,49.2,46,38.6],"y":[9.7,4.5,11.2,31.5,17.6,16.3,15.1,16.5],"text":["Region: Latin America and Caribbean<br />Country: Colombia","Region: Latin America and Caribbean<br />Country: Costa Rica","Region: Latin America and Caribbean<br />Country: Dominican Republic","Region: Latin America and Caribbean<br />Country: Honduras","Region: Latin America and Caribbean<br />Country: Mexico","Region: Latin America and Caribbean<br />Country: Panama","Region: Latin America and Caribbean<br />Country: Paraguay","Region: Latin America and Caribbean<br />Country: El Salvador"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(240,249,33,1)","opacity":1,"size":11.3385826771654,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(240,249,33,1)"}},"hoveron":"points","name":"Latin America and Caribbean","legendgroup":"Latin America and Caribbean","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":37.2602739726027},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Comparison of Income Inequality and Slums, 2018<br><sup>Based on global World Bank indicators<\/sup>","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[23.895,52.605],"tickmode":"array","ticktext":["25","30","35","40","45","50"],"tickvals":[25,30,35,40,45,50],"categoryorder":"array","categoryarray":["25","30","35","40","45","50"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"Gini Index","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-3.26,86.06],"tickmode":"array","ticktext":["0","20","40","60","80"],"tickvals":[0,20,40,60,80],"categoryorder":"array","categoryarray":["0","20","40","60","80"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Percentage of Population Living in Slums","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"title":{"text":"","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"orientation":"h","x":0.375,"y":-0.2},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"111989ea431c":{"x":{},"y":{},"colour":{},"label":{},"type":"scatter"}},"cur_data":"111989ea431c","visdat":{"111989ea431c":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```


```r
saveWidget (interactive_plot, "my_plot.html")
```

Note: I altered the legend position for the .HTML version because I thought it looked better in the .HTML file (even though it looks weird here).

## Visualization of a Model


```r
literacy_v_life <- read_csv("data/literacy_v_life.csv")
```

```
## Rows: 12 Columns: 4
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): Region, Code
## dbl (2): Literacy, Life
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
lvl_model <- lm(Life ~ Literacy,
                data = literacy_v_life)
```


```r
ggplot(literacy_v_life, aes(x = Literacy, y = Life)) +
  geom_point() +
  geom_smooth(method = "lm", 
              formula = "y ~ x") + 
       annotate(
    "text", label = "Least Developed Countries",
    x = 66.5, y = 68, size = 3, colour = "#440154"
  ) +
  annotate(
    "text", label = "Fragile and Conflict Affected Situations",
    x = 70, y = 59.2, size = 3, colour = "#440154"
  ) +
  annotate(
    "text", label = "Heavily Indebted\nPoor Countries",
    x = 60.4, y = 65, size = 3, colour = "#440154"
  ) +
  guides(col = FALSE)   +
   annotate(geom = "segment", 
           x = 64.2, xend = 64.2, 
           y = 60, yend = 61.5, 
           color = "#440154",
           arrow = arrow(length = unit(0.5, "lines"))) +
   annotate(geom = "segment", 
           x = 64.1, xend = 64.1, 
           y = 66.9, yend = 65.4, 
           color = "#440154",
           arrow = arrow(length = unit(0.5, "lines"))) +  
  annotate(geom = "segment", 
           x = 61.2, xend = 62.7, 
           y = 63, yend = 63, 
           color = "#440154",
           arrow = arrow(length = unit(0.5, "lines"))) +  
  theme_minimal() +
  scale_color_viridis_d(option = "plasma") +
      labs(x = "Adult Literacy Rate (Age 15+)",
       y = "Life Expectancy at Birth (Years)",
       title = "Comparison of Adult Literacy and Life Expectancy, 2018", caption = "Source: The World Bank", subtitle = "World Bank indicators for 3 special country classifications (noted) and 9 regions") +
  theme (plot.subtitle = element_text(color = "blue", face = "italic"))
```

```
## Warning: The `<scale>` argument of `guides()` cannot be `FALSE`. Use "none" instead as
## of ggplot2 3.3.4.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](hardesty_project_02_files/figure-html/unnamed-chunk-11-1.png)<!-- -->



```r
tidy(lvl_model, conf.int = TRUE)
```

```
## # A tibble: 2 × 7
##   term        estimate std.error statistic    p.value conf.low conf.high
##   <chr>          <dbl>     <dbl>     <dbl>      <dbl>    <dbl>     <dbl>
## 1 (Intercept)   33.6      4.08        8.25 0.00000897   24.6      42.7  
## 2 Literacy       0.452    0.0520      8.70 0.00000563    0.336     0.568
```


```r
glance(lvl_model)
```

```
## # A tibble: 1 × 12
##   r.squared adj.r.squared sigma statistic    p.value    df logLik   AIC   BIC
##       <dbl>         <dbl> <dbl>     <dbl>      <dbl> <dbl>  <dbl> <dbl> <dbl>
## 1     0.883         0.872  2.64      75.6 0.00000563     1  -27.6  61.2  62.6
## # ℹ 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>
```


```r
lvl_coefs <- tidy(lvl_model, conf.int = TRUE) %>% 
  filter(term != "(Intercept)")

lvl_coefs
```

```
## # A tibble: 1 × 7
##   term     estimate std.error statistic    p.value conf.low conf.high
##   <chr>       <dbl>     <dbl>     <dbl>      <dbl>    <dbl>     <dbl>
## 1 Literacy    0.452    0.0520      8.70 0.00000563    0.336     0.568
```


```r
ggplot(lvl_coefs,
       aes(x = estimate, 
           y = fct_rev(term))) +
  geom_pointrange(aes(xmin = conf.low, 
                      xmax = conf.high)) +
  geom_vline(xintercept = 0, 
             color = "purple") + 
  theme_minimal()
```

![](hardesty_project_02_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

# Report: Exploration of Global World Bank Indicators


For this project, I decided to continue my exploration of various indicator datasets from The World Bank (https://data.worldbank.org/indicator), this time focusing on worldwide trends rather than country-specific. To meet the assignment guidelines, I worked with multiple datasets, **which are described and linked in the "Data Stories" section below.** For all visuals, I focused on data for the year 2018, primarily because I had fuller data for this particular year.

I chose to complete the project in RStudio, even though I knew that would limit the overall project sophistication. However, I felt I still had a lot to learn in the program. I installed several new packages during this process, including **jtools**, **htmlwidgets**, **plotly**, **ggrepel**, and **broom**. I learned a lot.

## Data Exploration

I began by working with the extreme [poverty](https://data.worldbank.org/topic/poverty?view=chart) indicators available on The World Bank's website. I had originally planned to generate all of my graphics using this data, but I ultimately expanded to include other indicators related to health and education. I knew these datasets would be ideal for creating spatial, map-based visualizations. However, I also spent some time exploring and comparing different indicators to see if they suggested correlations. 

I did a lot of my data cleaning manually in the .CSV files. For example, I removed unnecessary header lines and limited the data to my target year and geographic regions. I also truncated some of the long decimal data to the tenth place (primarily to make the display more used-friendly on my interactive plot). I know that most of this data cleaning can be completed in R to target only necessary data, such as using the `filter()` function, but it was just easier for me to work directly from a file that was already "cleaned." Note: the next course module specifically recommended NOT doing what I did, as it does not support reproducible research. Oops.

## Data Stories

I generated three visualizations, a map, an interactive scatter plot, and a linear regression model, to explore some of the data. The level of sophistication in these graphics was still limited by my ability in R. However, it was a bit easier for me this time, and I do see some data stories emerging. 

The spatial visualization (world map) incorporates data from the World Bank's [poverty indicator of headcount ratio](https://data.worldbank.org/indicator/SI.POV.DDAY?view=chart) at what's considered the "International Poverty Line," or those living on $2.15 or less a day. As you can see from my map (and other graphics on the World Bank site), the data for this indicator is incomplete. There are a lot of grey areas for countries with no indicator data in 2018. (Yes, France IS missing from this one!) However, the map still tells a story of where international poverty is clustered, in Africa and parts of Asia and Central America. I think the dark blue in the map also tells a story of relative wealth.

The second visualization, the interactive scatterplot, compares two different poverty indicators: the [percentage of the population living in slums](https://data.worldbank.org/indicator/EN.POP.SLUM.UR.ZS?view=chart) and the [Gini Index](https://data.worldbank.org/indicator/SI.POV.GINI?view=chart). The Gini Index is a summary measure of income inequality, representing the dispersion of income across the entire country's income distribution (U.S. Census Bureau). The Gini coefficient ranges from 1 to 0, with 0 indicating perfect equality (i.e., the lower the Gini coefficient, the greater the income equality). I grouped the data points by region and only included a total of 41 countries for which both of these indicators are available for 2018. Therefore, it's important to note that this isn't a perfect worldwide picture.

At first glance, the scatterplot doesn't immediately suggest a relationship between the two indicators. However, when I considered it more, two things stood out. First, it seems as though the percentage of population living in slums seems to begin rising exponentially once the Gini Index reaches a coefficient of around 35+. Also, the colors representing different regions are clustered. For example, countries in Latin America and the Caribbean are clustered around the bottom right, suggesting that they have a higher rate of income inequality but relatively lower percentage of population living in slums. In contrast, African nations are clusterd in the top middle-to-right, suggesting a higher presence of both income inequality and people living in slums. However, this might just be a reflection of differences in the quantities of urban populations in these regions.

The third plot is a linear regression model comparing the indicators for [life expectancy](https://data.worldbank.org/indicator/SP.DYN.LE00.IN?view=chart) at birth (total years, all population) and [literacy rates](https://data.worldbank.org/indicator/SE.ADT.LITR.ZS?view=chart) for the total adult population age 15+. I grouped the data by region and also included countries grouped in the special classifications of "Fragile and Conflict Affected Situations," "Heavily Indebted Poor Countries," and "Least Developed Countries." As might be expected, the trend line suggests a strong positive correlation between the indicators. The higher the life expectancy, the higher the literacy rate. I followed the steps in our lecture to further explore the data using the **broom** package. Creating a coefficient plot for one variable probably isn't helpful, but I followed the steps for practice.

It would be interesting to develop time-based trends for these datasets. I focused on a single year (2018), but I think many rich and interactive visualizations could incorporate indicator data over time. This would be fun to explore using a program like Tableau. Looking at multiple years may help with the issue of gaps in the individual country data as well. 

I also explored some of the datasets on Our World in Data. Some of their time-series maps and charts that use the same World Bank data are amazing. For example, I envied some of the graphics on their [Income Inequality](https://ourworldindata.org/income-inequality) page.  

## Application of Design Principles

My ability to apply appropriate data visualization principles was still limited by my knowledge of R, but perhaps less so with this project. I again experimented quite a bit with the application of color. While I explored a number of potential color palettes, this time I unified the theme among the plots using the `plasma` option for the *viridis* palette. I again preferred a bold color scheme with colors that are easily distinguishable from each other. I applied the various labels and manually made changes to the legends. 

One of the project requirements was to add annotations to one of the plots. I tried to include the region names or region code labels in the linear model (third graphic). I spent a great deal of time working with the `geom_text_repel()` and `geom_label_repel()` functions to try to add labels that looked good. No matter how much I tweaked the different arguments, I couldn't get it to look like I wanted it to. So, I settled for annotations highlighting where the three special country classifications in the dataset are located on the plot with arrows added. I could also consider adding annotations to label the other points, but that would be a very tedious way to do it. 

### **References**

Gini Index. U.s. Census Bureau. https://www.census.gov/topics/income-poverty/income-inequality/about/metrics/gini-index

Global Data Indicators. The World Bank. https://data.worldbank.org/indicator 

