---
title: "Some bar chart examples"
author: "mbod"
date: "10/17/2019"
output: 
  html_document:
    keep_md: yes
    toc: true
  pdf_document: default
---



# Some notes from lab session on 10/17/19

## Multiple output formats 

* I looked a bit more into this one and it looks like the __KNIT__ button will generate the first of the output formats in your `YAML` header.

* So if your header looks like:

```
---
title: "Some bar chart examples"
author: "mbod"
date: "10/17/2019"
output: 
  pdf_document: default
  html_document:
    keep_md: yes
---
```

and you hit ![](img/knitr.png), then it will attempt to create a PDF document from your RMarkdown but __NOT__ continue on and create the HTML and Markdown outputs.

* If you flip the order:

```
---
title: "Some bar chart examples"
author: "mbod"
date: "10/17/2019"
output:
  html_document:
    keep_md: yes
  pdf_document: default
---
```

then hitting ![](img/knitr.png) will create HTML and Markdown outputs only.


* You can select specific outputs not specified in your YAML header from the Knit menu:

![](img/knit_options.png)

#### Creating all outputs in your `YAML` header

* If you do want to create all the outputs listed in your RMarkdown document YAML header then you can use this function in the __Console__.<br/><br/>
  1. Make sure your current working directory is set to the location of your RMarkdown document:
     e.g.
```
       > getwd()
       
       [1] "/Users/mattodonnell"
```
    <br/>
      so need to set the working directory, e.g.:
```
      > setwd('/Users/mattodonnell/files/COMM783_describing_your_data_...etc..some_visualizations')
```
  <br/>
  2. Use the `rmarkdown::render()` function:
```
      rmarkdown::render('ggplot2_barplot_with_grouping.Rmd', outputs='all')
```
      
      

## Crosstabs and `ggplot2` bar plot examples

#### A question came up about how to create a crosstab of two category variables and then display the result in a grouped (but not stacked) bar graph.


1. First generate a table with two categorical variables: 
  * `cat1`
    * `levels: A, B, C, D, E`
  * `cat2`
    * `levels: Level_1, Level_2`



```r
df<- data.frame(cat1=sample(c('A','B','C','D', 'E','F'), 100, replace=TRUE),
           cat2=sample(c('Level_1','Level_2'), 100, replace=TRUE, prob = c(0.7,0.3))
)
```

2. Let's take a look at the top 12 rows and here use the tidyverse pipeline style:


```r
df %>% 
  head(12) %>% 
    kable() %>% 
      kable_styling(bootstrap_options = c("striped", "hover"))
```

<table class="table table-striped table-hover" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> cat1 </th>
   <th style="text-align:left;"> cat2 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> C </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> A </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> A </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> D </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> D </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> F </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> D </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> A </td>
   <td style="text-align:left;"> Level_1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_2 </td>
  </tr>
</tbody>
</table>

* The `kable` functions (see inclusion of `kable` and `kableExtra` libraries above in first code block) provide ways of producing nicely formatted and grouped table output.

* And it can apply to a range of outputs, e.g. HTML, PDF and Word. See https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html


3. Plot distributions of categorical variables.

* The `gridExtra` library provides a way of including more than one plot in a figure in various arrangements. This is really useful when the plots are sub-levels of a single or combination of variables where you might use _facets_ or _grouping_.


```r
p1 <- df %>% ggplot(aes(cat1)) + geom_histogram(stat='count', binwidth=1)
```

```
## Warning: Ignoring unknown parameters: binwidth, bins, pad
```

```r
p2 <- df %>% ggplot(aes(cat2)) + geom_histogram(stat='count', binwidth=1)
```

```
## Warning: Ignoring unknown parameters: binwidth, bins, pad
```

```r
grid.arrange(p1,p2, nrow=1)
```

![](ggplot2_barplot_with_grouping_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

4. Create a cross tabulation of the two categorical variables.

* In the tidyverse paradigm you can use the `group_by()` function followed by the `summarise()` function to crate a cross tabulation:


```r
df %>% group_by(cat1, cat2) %>%
  summarise(cnt=n())
```

```
## # A tibble: 12 x 3
## # Groups:   cat1 [6]
##    cat1  cat2      cnt
##    <fct> <fct>   <int>
##  1 A     Level_1    14
##  2 A     Level_2     9
##  3 B     Level_1    12
##  4 B     Level_2     7
##  5 C     Level_1     9
##  6 C     Level_2     2
##  7 D     Level_1    19
##  8 D     Level_2     3
##  9 E     Level_1     9
## 10 E     Level_2     4
## 11 F     Level_1     9
## 12 F     Level_2     3
```

* To create a crosstab like table you can add the `spread()` function to the end of the pipeline.


```r
df %>% group_by(cat1, cat2) %>%
  summarise(cnt=n()) %>%
    spread(cat2, cnt)
```

```
## # A tibble: 6 x 3
## # Groups:   cat1 [6]
##   cat1  Level_1 Level_2
##   <fct>   <int>   <int>
## 1 A          14       9
## 2 B          12       7
## 3 C           9       2
## 4 D          19       3
## 5 E           9       4
## 6 F           9       3
```

* Changing the order of the variables in the `group_by()` function will change the order of the rows and columns.

* You can also then add header and grouping rows to the table using `kable` and related functions (see https://cran.r-project.org/web/packages/kableExtra/vignettes/awesome_table_in_html.html#group_rows_via_multi-row_cell)


```r
df %>% group_by(cat2, cat1) %>%
  summarise(cnt=n()) %>%
      spread(cat1, cnt) %>%
        kable() %>%
            kable_styling() %>%
              add_header_above(c(' '=1, 'cat1'=length(levels(df$cat1))))
```

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
<tr>
<th style="border-bottom:hidden" colspan="1"></th>
<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="6"><div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">cat1</div></th>
</tr>
  <tr>
   <th style="text-align:left;"> cat2 </th>
   <th style="text-align:right;"> A </th>
   <th style="text-align:right;"> B </th>
   <th style="text-align:right;"> C </th>
   <th style="text-align:right;"> D </th>
   <th style="text-align:right;"> E </th>
   <th style="text-align:right;"> F </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 9 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
</tbody>
</table>

5. Add a new column to the grouped table using the `mutate()` function.

  * Here we first `group_by()` the two categorical variables
  * and then create a summary variable `cnt` that counts the number of grouped rows in each combination
  * and finally we can add a new column `percent` with the formula:
    ```
    cnt/sum(cnt)
    ```



```r
df %>% group_by(cat1, cat2) %>%
  summarise(cnt=n()) %>% 
    mutate(percent=round(100*cnt/sum(cnt),2)) %>%
      kable() %>%
        kable_styling('border')
```

<table class="table table-bordered" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> cat1 </th>
   <th style="text-align:left;"> cat2 </th>
   <th style="text-align:right;"> cnt </th>
   <th style="text-align:right;"> percent </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> A </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 60.87 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> A </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 39.13 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 63.16 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> B </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 36.84 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> C </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 81.82 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> C </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 18.18 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> D </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:right;"> 86.36 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> D </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 13.64 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> E </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 69.23 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> E </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 30.77 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> F </td>
   <td style="text-align:left;"> Level_1 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 75.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> F </td>
   <td style="text-align:left;"> Level_2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 25.00 </td>
  </tr>
</tbody>
</table>

* See http://analyticswithr.com/contingencytables.html for more examples and explanation.

6. Now we can create a grouped bar chart using the same steps and adding the `ggplot` functions to the end of the pipeline.


```r
df %>% group_by(cat2, cat1) %>%
  summarise(cnt=n()) %>% 
    mutate(percent=cnt/sum(cnt)*100) %>%
     ggplot(aes(x=cat1, y=percent)) + 
        geom_bar(stat='identity', aes(fill=cat2), position='dodge')
```

![](ggplot2_barplot_with_grouping_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

* Here the `percent` values add up to 100% within the `cat2` variable (`Level_1` and `Level_2` respectively).


* If you flip the order of these categorical variables back in the `group_by()` function then this will flip that so the percentages are within levels of the `cat1` variable. 


```r
df %>% group_by(cat1, cat2) %>%
  summarise(cnt=n()) %>% 
    mutate(percent=cnt/sum(cnt)*100) %>%
     ggplot(aes(x=cat1, y=percent)) + 
        geom_bar(stat='identity', aes(fill=cat2), position='dodge')
```

![](ggplot2_barplot_with_grouping_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
* In which case you probably do want a stacked bar graph.



```r
df %>% group_by(cat1, cat2) %>%
  summarise(cnt=n()) %>% 
    mutate(percent=cnt/sum(cnt)*100) %>%
     ggplot(aes(x=cat1, y=percent)) + 
        geom_bar(stat='identity', aes(fill=cat2))
```

![](ggplot2_barplot_with_grouping_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
