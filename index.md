---
title       : Data Visualization
subtitle    : "Lecture 3: Taste of R Workshop, UO COE"
author      : Daniel Anderson
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : zenburn      # 
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
--- 
<style>
em {
  font-style: italic
}
</style>

<style>
strong {
  font-weight: bold;
}
</style>




## Agenda
* Review of tidy data
* Introduce the *ggplot2* package
* Model-based plotting and an advanced example with  *purrr*  (time permitting)

**Note:** There is a *lot* that we won't cover (title and labels, for example). This is meant to be a broad overview. Get in contact with me if you want to know more (I have other slides). 


---- 
## Tidy data

# Definition
1. Each variable is a column
2. Each observation is a row
3. Each type of observational unit forms a table

<div align = "left">
<img src = assets/img/tidy_data.png width = 1000 height = 350>
</div>

----
## Quick example (review)


|religion                | <$10k| $10-20k| $20-30k| $30-40k| $40-50k| $50-75k| $75-100k| $100-150k| >150k| Don't know/refused|
|:-----------------------|-----:|-------:|-------:|-------:|-------:|-------:|--------:|---------:|-----:|------------------:|
|Agnostic                |    27|      34|      60|      81|      76|     137|      122|       109|    84|                 96|
|Atheist                 |    12|      27|      37|      52|      35|      70|       73|        59|    74|                 76|
|Buddhist                |    27|      21|      30|      34|      33|      58|       62|        39|    53|                 54|
|Catholic                |   418|     617|     732|     670|     638|    1116|      949|       792|   633|               1489|
|Don’t know/refused      |    15|      14|      15|      11|      10|      35|       21|        17|    18|                116|
|Evangelical Prot        |   575|     869|    1064|     982|     881|    1486|      949|       723|   414|               1529|
|Hindu                   |     1|       9|       7|       9|      11|      34|       47|        48|    54|                 37|
|Historically Black Prot |   228|     244|     236|     238|     197|     223|      131|        81|    78|                339|
|Jehovah's Witness       |    20|      27|      24|      24|      21|      30|       15|        11|     6|                 37|
|Jewish                  |    19|      19|      25|      25|      30|      95|       69|        87|   151|                162|
|Mainline Prot           |   289|     495|     619|     655|     651|    1107|      939|       753|   634|               1328|
|Mormon                  |    29|      40|      48|      51|      56|     112|       85|        49|    42|                 69|
|Muslim                  |     6|       7|       9|      10|       9|      23|       16|         8|     6|                 22|
|Orthodox                |    13|      17|      23|      32|      32|      47|       38|        42|    46|                 73|
|Other Christian         |     9|       7|      11|      13|      13|      14|       18|        14|    12|                 18|
|Other Faiths            |    20|      33|      40|      46|      49|      63|       46|        40|    41|                 71|
|Other World Religions   |     5|       2|       3|       4|       2|       7|        3|         4|     4|                  8|
|Unaffiliated            |   217|     299|     374|     365|     341|     528|      407|       321|   258|                597|

----
## The tidied version


|religion |income             | freq|
|:--------|:------------------|----:|
|Agnostic |<$10k              |   27|
|Agnostic |$10-20k            |   34|
|Agnostic |$20-30k            |   60|
|Agnostic |$30-40k            |   81|
|Agnostic |$40-50k            |   76|
|Agnostic |$50-75k            |  137|
|Agnostic |$75-100k           |  122|
|Agnostic |$100-150k          |  109|
|Agnostic |>150k              |   84|
|Agnostic |Don't know/refused |   96|
|Atheist  |<$10k              |   12|
|Atheist  |$10-20k            |   27|

----
## Why are we reviewing this?
* The *ggplot2* package can work with data in a variety of formats. However, it is *optimal* to work with a tidy data frame.

* Tidy data work best because it allows for simple definitions of group aesthetics, and faceting (both of which we'll talk about shortly).

* Having your data in a tidy format can reduce the amount of code you need to write to get the same plot, and can make your code more clear.

---- .segue
# ggplot2

----
## The *ggplot2* package
Today, we'll primarily be covering the basics of the *ggplot2* package. Much of this presentation is based on examples from the new *ggplot2* book.

![ggplotBook](./assets/img/ggplotBook.png)


----
## Part of the many reasons Hadley is a cool guy

<div align = "left">
<img src = assets/img/freeggplot.png width = 1000 height = 500>
</div>

(It's no longer there, but if you want access to it let me know)


----
## Other resources
The *ggplot2* package is one of the most popular R packages. There are a plethora of resources to learn the syntax. 

* Perhaps the most definitive, and indexes all the capabilities of ggplot2, along with multiple examples 
	+ http://docs.ggplot2.org/current/index.html#

* RStudio cheat sheet can also be helpful
	+ https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf


---- 
## Components
Every *ggplot* plot has three components

1. data
	* The data used to produce the plot
2. aesthetic mappings
	* between variables and visual properties
3. layer(s)
	* usually through the geom function to produce geometric shape to be rendered


----
## Basic syntax
![ggplotBasicSyntax](./assets/img/ggplotBasicSyntax.png)

Note that Hadley recommends putting each `geom_XXX` on a separate line to ease clarity. I agree with this suggestion, but will not be following it in these slides to help save space.

----
## mpg data
Very similar to the *mtcars* data, but with more cases and a few more interesting variables


```r
library(ggplot2)
data(mpg)
head(mpg)
```

```
## # A tibble: 6 × 11
##   manufacturer model displ  year   cyl      trans   drv   cty   hwy    fl
##          <chr> <chr> <dbl> <int> <int>      <chr> <chr> <int> <int> <chr>
## 1         audi    a4   1.8  1999     4   auto(l5)     f    18    29     p
## 2         audi    a4   1.8  1999     4 manual(m5)     f    21    29     p
## 3         audi    a4   2.0  2008     4 manual(m6)     f    20    31     p
## 4         audi    a4   2.0  2008     4   auto(av)     f    21    30     p
## 5         audi    a4   2.8  1999     6   auto(l5)     f    16    26     p
## 6         audi    a4   2.8  1999     6 manual(m5)     f    18    26     p
## # ... with 1 more variables: class <chr>
```

----
## Quick example


```r
data(mpg)
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point()
```

![plot of chunk mpgEx1](assets/fig/mpgEx1-1.png)

----
## Quick example 2
Note that the only thing that has changed here is the `geom` 


```r
data(mpg)
ggplot(mpg, aes(x = displ, y = hwy)) + geom_smooth()
```

![plot of chunk mpgEx2](assets/fig/mpgEx2-1.png)

----
## Add an additional layer


```r
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point() + geom_smooth()
```

![plot of chunk mpgEx3](assets/fig/mpgEx3-1.png)

----
## Add an additional aesthetic


```r
ggplot(mpg, aes(x = displ, y = hwy, color = class)) + geom_point() 
```

![plot of chunk mpgEx4](assets/fig/mpgEx4-1.png)

----
## Add smooth line for each class
# Too busy
Note the below spits out some warnings because of the sparsity of the data. I've suppressed them here.


```r
ggplot(mpg, aes(x = displ, y = hwy, color = class)) + geom_point() +
 geom_smooth()
```
![plot of chunk mpgEx5b](assets/fig/mpgEx5b-1.png)

----
## Remove SE


```r
ggplot(mpg, aes(x = displ, y = hwy, color = class)) + geom_point() +
 geom_smooth(se = FALSE)
```

![plot of chunk mpgEx6b, ](assets/fig/mpgEx6b, -1.png)

----
## Change the color of all points


```r
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point(color = "purple") +
 geom_smooth(se = FALSE)
```

![plot of chunk mpgEx7](assets/fig/mpgEx7-1.png)

---- .segue
# Can you guess how we would change the line color?

----


```r
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point(color = "purple") +
 geom_smooth(se = FALSE, color = "gray", size = 2, linetype = "dashed")
```

![plot of chunk mpgEx8](assets/fig/mpgEx8-1.png)

----
Worth mentioning, traditional calls to line color/type/size also work


```r
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point(color = "purple") +
 geom_smooth(se = FALSE, col = "gray", lwd = 2, lty = "dashed")
```

![plot of chunk mpgEx9](assets/fig/mpgEx9-1.png)

----
## Change the "wiggliness" of the smoother


```r
ggplot(mpg, aes(x = displ, y = hwy)) + geom_point(color = "purple") +
 geom_smooth(span = 0.2)
```

![plot of chunk mpgEx10](assets/fig/mpgEx10-1.png)

----
## Geoms for two continuous variables


|Geoms     |Description                                                              |Code             |
|:---------|:------------------------------------------------------------------------|:----------------|
|jitter    |Jitter points (to avoid overlapping)                                     |geom_jitter()    |
|point     |Plot points at each x&#124;y intersection                                |geom_point()     |
|quantile  |Plot lines from quantile regression                                      |geom_quantile()  |
|rug       |Plot 1d scatterplot on margins (stripchart)                              |geom_rug()       |
|smooth    |Plot a smoothing function (many smoothers available)                     |geom_smooth()    |
|text      |Add text annotations                                                     |geom_text()      |
|bin2d     |Bin observations that are close together and color according the density |geom_bin2d()     |
|density2d |Contour lines of the data density                                        |geom_density2d() |
|hex       |Hexagonal bins of data colored according to their density                |geom_hex()       |

----
## Guided practice
* Load (install first, if necessary) the *ggplot2* package. 
* Load the diamonds dataset with `data(diamonds)`
* Set and store the data and aesthetics, in an object `p`, using the following code


```r
data(diamonds)
p <- ggplot(diamonds, aes(carat, price))
```
* Print `p`. What do you see?
* Explore different geoms, with `p + geom_XXX()`. For example, a basic scatterplot could be produced with


```r
p + geom_point()
```

* Add at least one additional layer (i.e., produce a plot with at least two layers)


-----
## Some possibilities


```r
p + geom_point()
```

![plot of chunk guidedp1_3a](assets/fig/guidedp1_3a-1.png)

----
## Probably better


```r
p + geom_hex()
```

![plot of chunk guidedp1_3b](assets/fig/guidedp1_3b-1.png)

----
## Another similar alternative

```r
p + geom_bin2d()
```

![plot of chunk guidedp1_3c](assets/fig/guidedp1_3c-1.png)

----
## Yet another alternative


```r
p + geom_point(alpha = 0.01) + geom_density2d(color = "red")
```

![plot of chunk guidedp1_3d](assets/fig/guidedp1_3d-1.png)

-----
## Quantiles
Defaults to the 25th, 50th, and 75th percentiles
 

```r
p + geom_quantile()
```

![plot of chunk guidedp1_4](assets/fig/guidedp1_4-1.png)

-----
## Quantiles
Change the quantiles to deciles (from 10th to 90th)


```r
p + geom_quantile(quantiles = seq(0.1, 0.9, 0.1))
```

![plot of chunk guidedp1_5](assets/fig/guidedp1_5-1.png)

----
## Add an extra layer


```r
p + geom_point() + geom_rug() + geom_smooth()
```

![plot of chunk guidedp1_6](assets/fig/guidedp1_6-1.png)

----
## Color by cut


```r
p2cut <- ggplot(diamonds, aes(carat, price, color = cut))
p2cut + geom_point()
```

![plot of chunk guidedp1_7](assets/fig/guidedp1_7-1.png)

----
## Color by color


```r
p2color <- ggplot(diamonds, aes(carat, price, color = color))
p2color + geom_point()
```

![plot of chunk guidedp1_8](assets/fig/guidedp1_8-1.png)

----
## Color by clarity


```r
p2clarity <- ggplot(diamonds, aes(carat, price, color = clarity))
p2clarity + geom_point()
```

![plot of chunk guidedp1_9](assets/fig/guidedp1_9-1.png)

----
## geoms: One variable


|Geoms                  |Description                                                |Code                    |
|:----------------------|:----------------------------------------------------------|:-----------------------|
|area                   |Filled area plot                                           |geom_area(stat = "bin") |
|density                |Density plot                                               |geom_density()          |
|dotplot                |Stacked dotplot, with each dot representing an observation |geom_dotplot()          |
|polygon of Frequencies |Polygon of frequencies                                     |geom_freqpoly           |
|histogram              |Standard histogram                                         |geom_histogram          |
|barplot                |Standard barchart                                          |geom_bar                |

----
## Area plot

```r
price <- ggplot(diamonds, aes(price)) 
price + geom_area(stat = "bin")
```

![plot of chunk geom_area](assets/fig/geom_area-1.png)

----
# Frequency polygons

```r
price + geom_freqpoly()
```

![plot of chunk geom_freqPoly](assets/fig/geom_freqPoly-1.png)

----
## Evaluate frequencies by *cut*


```r
price2 <- ggplot(diamonds, aes(price, color = cut))
price2 + geom_freqpoly(bins = 50)
```

![plot of chunk geom_freqPolyClarity](assets/fig/geom_freqPolyClarity-1.png)

----
## Histograms

```r
price + geom_histogram()
```

![plot of chunk geom_histogram1](assets/fig/geom_histogram1-1.png)

----
# Change binwidth

```r
price + geom_histogram(binwidth = 5)
```

![plot of chunk geom_histogram2](assets/fig/geom_histogram2-1.png)

----
## Barplots


```r
ggplot(mpg, aes(trans)) + geom_bar()
```

![plot of chunk barplot1](assets/fig/barplot1-1.png)

---- .segue
# Plotting categorical variables

----
## boxplots
Note that the categorical variable must be categorical or declared as a factor


```r
bp <- ggplot(mpg, aes(drv, hwy))
bp + geom_boxplot()
```

![plot of chunk boxplots1](assets/fig/boxplots1-1.png)

---- &twocol
## stripcharts

*** =left


```r
bp + geom_point()
```

![plot of chunk stripcharts1](assets/fig/stripcharts1-1.png)

*** =right


```r
bp + geom_jitter()
```

![plot of chunk jitterchart1](assets/fig/jitterchart1-1.png)

-----
## Control the amount of jittering


```r
bp + geom_jitter(width = 0.3)
```

![plot of chunk jitter](assets/fig/jitter-1.png)

----
## Useful together


```r
bp + geom_boxplot() + geom_jitter(width = 0.3)
```

![plot of chunk boxplotJitter](assets/fig/boxplotJitter-1.png)

----
## Usually better: Violin plots


```r
bp + geom_violin()
```

![plot of chunk violin](assets/fig/violin-1.png)

--- .segue
# Faceting

----
## Produce separate plots according to a specific variable
Back to the mpg dataset:
* Produce a separate plot of the relation between engine size and highway miles per gallon for each car type.


```r
hwy <- ggplot(mpg, aes(displ, hwy))
hwy + geom_point() + facet_wrap(~class)
```

![plot of chunk faceting1](assets/fig/faceting1-1.png)

----
## Add a linear function to each plot


```r
hwy <- ggplot(mpg, aes(displ, hwy))
hwy + geom_point() + geom_smooth(method = "lm") + facet_wrap(~class)
```

![plot of chunk faceting2](assets/fig/faceting2-1.png)

----
## Example with growth modeling
The data




```r
# Loaded earlier
head(d, n = 15)
```

```
##         SID   TID ScID    Months RIT
## 2873 332347 30867 1117 8.7387097 192
## 355  400047 69957 1077 1.6387097 182
## 387  400047 69957 1077 4.3225806 179
## 162  400047 69957 1077 8.9677419 191
## 648  400277 69957 1077 1.2053763 196
## 230  400277 69957 1077 4.3225806 197
## 780  400277 69957 1077 8.9677419 209
## 8878 400597 32247 1257 0.9720430 197
## 8914 400597 32247 1257 4.2258065 202
## 8790 400597 32247 1257 8.3053763 211
## 348  400807 28767 1077 1.3387097 178
## 168  400807 28767 1077 4.1935484 190
## 803  400807 28767 1077 8.8053763 178
## 1150 401227 32787 1087 0.9387097 187
## 1269 401227 32787 1087 4.1612903 192
```

----
## Random sampling
There are far too many students to plot every one. Instead, we'll take a random sample of 16 (a couple of times)


```r
set.seed(100)
samp1 <- d[d$SID %in% sample(levels(d$SID), 16), ]
samp2 <- d[d$SID %in% sample(levels(d$SID), 16), ]
samp3 <- d[d$SID %in% sample(levels(d$SID), 16), ]
head(samp1)
```

```
##         SID   TID ScID    Months RIT
## 2803 503177 30867 1117 1.1053763 198
## 2829 503177 30867 1117 4.3548387 202
## 2761 503177 30867 1117 8.7387097 214
## 9275 516037 34167 1267 0.9387097 214
## 9191 516037 34167 1267 4.1612903 205
## 9614 516037 34167 1267 8.5720430 209
```

----
## Sample 1

```r
ggplot(samp1, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 facet_wrap(~SID)
```

![plot of chunk DissPLot1](assets/fig/DissPLot1-1.png)

----
## Sample 2

```r
ggplot(samp2, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 facet_wrap(~SID)
```

![plot of chunk DissPLot2, ](assets/fig/DissPLot2, -1.png)

----
## Sample 3

```r
ggplot(samp3, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 facet_wrap(~SID)
```

![plot of chunk DissPLot3, ](assets/fig/DissPLot3, -1.png)

----
## Alternative: Connect the dots
Note that `group` is specified in the aesthetic now.


```r
ggplot(samp1, aes(Months, RIT, group = SID)) + geom_point() + 
	geom_line()
```

![plot of chunk connectDots](assets/fig/connectDots-1.png)

----
Or, put all lines in a single plot


```r
ggplot(samp1, aes(Months, RIT, group = SID)) + geom_point() + 
	geom_smooth(se = FALSE, span = 2)
```

![plot of chunk smoothDots](assets/fig/smoothDots-1.png)

----
## What happens if you forget to specify the group?


```r
ggplot(samp1, aes(Months, RIT)) + geom_point() + 
	geom_line()
```

![plot of chunk connectDotsError](assets/fig/connectDotsError-1.png)

----
## How bad is the nonlinearity, really?
Overlay a linear trend


```r
ggplot(samp1, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 geom_smooth(method = "lm", se = FALSE) + facet_wrap(~SID)
```

![plot of chunk DissPLot4](assets/fig/DissPLot4-1.png)

----

```r
ggplot(samp2, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 geom_smooth(method = "lm", se = FALSE) + facet_wrap(~SID)
```

![plot of chunk DissPLot5, ](assets/fig/DissPLot5, -1.png)

----

```r
ggplot(samp3, aes(Months, RIT)) + 
	geom_point() + geom_smooth(color = "red", span = 2, se = FALSE) +
	 geom_smooth(method = "lm", se = FALSE) + facet_wrap(~SID)
```

![plot of chunk DissPLot6, ](assets/fig/DissPLot6, -1.png)

----
## A few notes
* Equivalent plots could be produced at other levels (in this case, the classroom or school levels)
* The previous plots did produce some warnings (only 3 points with the LOESS)


```r
lfit <- loess(RIT ~ Months, data = samp1, subset = SID == 503177, span = 2)
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : span too small. fewer data values than degrees of freedom.
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : Chernobyl! trL>n 3

## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : Chernobyl! trL>n 3
```

```
## Warning in sqrt(sum.squares/one.delta): NaNs produced
```

----
## Different faceting
`facet_wrap` vs `facet_grid`

![faceting](./assets/img/faceting.png)

----
## Options for `facet_wrap`
Change the number of columns


```r
f <- ggplot(mpg, aes(displ, hwy)) + geom_point()
f + facet_wrap(~class, ncol = 4)
```

![plot of chunk faceting3](assets/fig/faceting3-1.png)

----
Change the direction plots are produced


```r
f + facet_wrap(~class, ncol = 4, dir = "v")
```

![plot of chunk faceting4](assets/fig/faceting4-1.png)

----
## Facet Grid
Note that `.` must be included on the left, if only a single variable is used


```r
f + facet_grid(. ~ cyl)
```

![plot of chunk facetGrid1](assets/fig/facetGrid1-1.png)

----

```r
f + facet_grid(drv ~ .)
```

![plot of chunk facetGrid2](assets/fig/facetGrid2-1.png)

----
## Two variables


```r
f + facet_grid(drv ~ cyl)
```

![plot of chunk facetGrid3](assets/fig/facetGrid3-1.png)

----
## Two variables

(The LOESS estimator spits out warnings here again)


```r
f + geom_smooth(span = 2) + facet_grid(drv ~ cyl)
```

![plot of chunk facetGrid4](assets/fig/facetGrid4-1.png)

-----
## Scales with faceting
Allow all scales to be produced independently (freely)


```r
f + facet_wrap(~ cyl, scales = "free")
```

![plot of chunk facetScales1](assets/fig/facetScales1-1.png)

----
Free y-axis scale, fixed x


```r
f + facet_wrap(~ cyl, scales = "free_y")
```

![plot of chunk facetScales2](assets/fig/facetScales2-1.png)

----
Free x-axis scale, fixed y


```r
f + facet_wrap(~ cyl, scales = "free_x")
```

![plot of chunk facetScales3](assets/fig/facetScales3-1.png)

----
Change spacing with `facet_grid`


```r
f + facet_grid(drv ~ cyl, scales = "free", space = "free")
```

![plot of chunk facetGrid5](assets/fig/facetGrid5-1.png)

---- &twocol
## Faceting vs Grouping

*** =left


```r
ggplot(mpg, 
	aes(displ, hwy, color = factor(drv))) + 
	geom_point()
```

![plot of chunk facetGrouping1](assets/fig/facetGrouping1-1.png)

*** =right


```r
ggplot(mpg, aes(displ, hwy)) + 
	geom_point() + 
	facet_wrap(~ drv)
```

![plot of chunk facetGrouping2](assets/fig/facetGrouping2-1.png)

----
## Guided practice

You can view the probability densities of diamond price by cut with the following code.


```r
base <- ggplot(diamonds, aes(price, color = cut))
base + geom_density()
```

* Use faceting to view the densities by *clarity*.
* Modify the code to group by *clarity* and facet by *cut*. Which do you 
  prefer?

You can view the relation between *price* and *carat* with the following code


```r
ggplot(diamonds, aes(carat, price)) + geom_point()
```
* Modify the code so the points are colored according to their color.
* Use faceting to produce separate plots for each color.

----
Use faceting to view the densities by *clarity*.


```r
base <- ggplot(diamonds, aes(price, color = cut))
base + geom_density() + facet_wrap(~ clarity)
```

![plot of chunk guidedp2_4](assets/fig/guidedp2_4-1.png)

----
Modify the code to group by *clarity* and facet by *cut*.


```r
base2 <- ggplot(diamonds, aes(price, color = clarity))
base2 + geom_density() + facet_wrap(~ cut)
```

![plot of chunk guidedp2_5](assets/fig/guidedp2_5-1.png)

----
Modify the code so the points are colored according to their color.


```r
ggplot(diamonds, aes(carat, price, color = color)) + geom_point()
```

![plot of chunk guidedp2_6](assets/fig/guidedp2_6-1.png)

----
Use faceting to produce separate plots for each color.


```r
ggplot(diamonds, aes(carat, price)) + geom_point() + facet_wrap(~ color)
```

![plot of chunk guidedp2_7](assets/fig/guidedp2_7-1.png)

---- .segue
# Themes

----
## Overview of themes
* Themes do not change how the data are rendered
* Only change visual properties
* Many built-in themes
	+ Even more available through extension packages (specifically *ggthemes*)
* Fully customizable (though the syntax becomes lengthier)

----
## theme_gray (default)


```r
baseP <- ggplot(economics, aes(date, unemploy)) + geom_line()
baseP + theme_gray()
```

![plot of chunk theme_gray](assets/fig/theme_gray-1.png)

----
## theme_bw


```r
baseP + theme_bw()
```

![plot of chunk theme_bw](assets/fig/theme_bw-1.png)

----
## theme_classic


```r
baseP + theme_classic()
```

![plot of chunk theme_classic](assets/fig/theme_classic-1.png)

----
## theme_dark


```r
baseP + theme_dark()
```

![plot of chunk theme_dark](assets/fig/theme_dark-1.png)

----
## theme_minimal


```r
baseP + theme_minimal()
```

![plot of chunk theme_minimal](assets/fig/theme_minimal-1.png)

----
## Further customization
* See http://docs.ggplot2.org/dev/vignettes/themes.html
* See *ggplot2* book, Chapter 8

<br>
**Take Home Message:** If you want it to look a certain way, you can do it (essentially nothing is impossible). Often there are others who have developed themes that will be close to what you want, which is easier than developing your own theme (although that can be rewarding in its own right).

--- .segue
# Exploratory versus model-based plotting

----
## Underlying theory
* *ggplot2* is optimized for speed in exploratory plotting.
* Make ugly plots when exploring, but take the time to beautify them for publication
* Model-based plotting is essentially confirmatory, but can help you understand and communicate the results better.

----
## Load and tidy SEDA Data


```r
library(tidyverse)
d <- read_csv("./course_materials/data/district means national-referenced by year grade subject (long file).csv")
td <- d %>% 
  gather(mean_link_ela:se_link_math, 
    key = "key",
    value = "score") %>% 
  separate(key, c("variable", "discard", "subject"), sep = "_") %>% 
  mutate(time = grade - 3,
  		 leaname = as.factor(leaname),
  		 stateabb = as.factor(stateabb)) %>% 
  spread(variable, score) %>% 
  select(-discard)
kable(head(td))
```



|  leaid|leaname                | fips|stateabb | year| grade|subject | time|     mean|       se|
|------:|:----------------------|----:|:--------|----:|-----:|:-------|----:|--------:|--------:|
| 100002|ALABAMA YOUTH SERVICES |    1|AL       | 2009|     8|ela     |    5| 210.5474| 6.723581|
| 100002|ALABAMA YOUTH SERVICES |    1|AL       | 2011|     8|ela     |    5| 231.6601| 6.741922|
| 100002|ALABAMA YOUTH SERVICES |    1|AL       | 2012|     8|ela     |    5| 226.1813| 6.657756|
| 100005|ALBERTVILLE CITY       |    1|AL       | 2009|     3|ela     |    0| 204.4659| 2.747565|
| 100005|ALBERTVILLE CITY       |    1|AL       | 2009|     4|ela     |    1| 207.4045| 2.939638|
| 100005|ALBERTVILLE CITY       |    1|AL       | 2009|     5|ela     |    2| 216.8594| 2.599224|

----
## `lm`-based plot

If we fit a model like `lm(mean ~ time, data = td)`, then we can actually visualize this without even fitting the model, using `ggplot`


```r
ggplot(td, aes(time, mean)) + geom_point() + geom_smooth(method = "lm")
```

![plot of chunk lin_mod_vis_seda](assets/fig/lin_mod_vis_seda-1.png)

---- &twocol
## `lm`-based plot (continued)

Or perhaps we're interested in separate slopes for reading and math.

*** =left


```r
ggplot(td, aes(time, mean, color = factor(subject))) + 
	geom_point() + 
	geom_smooth(method = "lm")
```

![plot of chunk lin_mod_vis_by_sub_seda1](assets/fig/lin_mod_vis_by_sub_seda1-1.png)

*** =right


```r
ggplot(td, aes(time, mean)) + 
	geom_point() + 
	geom_smooth(method = "lm") +
	facet_wrap(~subject)
```

![plot of chunk lin_mod_vis_by_sub_seda2](assets/fig/lin_mod_vis_by_sub_seda2-1.png)

----
## Model-output plotting
While the previous plots are basically model-based plots, they can also be  exploratory, and often we want to plot results from the model. As mentioned last time, the *broom* package can help.


```r
m <- lm(mean ~ stateabb, data = td)
library(broom)
tidy_m <- tidy(m, conf.int = TRUE)

ggplot(tidy_m[-1, ], aes(estimate, term, color = term)) + # drop intercept
	geom_point() +
  	geom_errorbarh(aes(xmin = conf.low, xmax = conf.high)) +
    geom_vline(xintercept = 0)
```

----

![plot of chunk point_est_eval](assets/fig/point_est_eval-1.png)

---- .segue
# Fitting and visualizing multiple models (advanced)

----
## Fitting and plotting multiple models 
(advanced: illustration only)
* Fit separate models for each state, using `time` to predict `mean`.
* Summarize and plot $R^2$ for each model


```r
library(purrr)
by_state <- td %>% 
	group_by(stateabb) %>% 
	nest()
by_state
```

```
## # A tibble: 52 × 2
##    stateabb                  data
##      <fctr>                <list>
## 1        AL  <tibble [7,956 × 9]>
## 2        AK  <tibble [2,122 × 9]>
## 3        AZ  <tibble [8,768 × 9]>
## 4        AR <tibble [15,350 × 9]>
## 5        CA <tibble [43,806 × 9]>
## 6        CO  <tibble [7,372 × 9]>
## 7        CT  <tibble [9,076 × 9]>
## 8        DE    <tibble [938 × 9]>
## 9        DC     <tibble [60 × 9]>
## 10       FL  <tibble [4,322 × 9]>
## # ... with 42 more rows
```

----
## Fit multiple models


```r
by_state <- by_state %>% 
	mutate(model = map(data, ~lm(mean ~ time, data = .)))
by_state
```

```
## # A tibble: 52 × 3
##    stateabb                  data    model
##      <fctr>                <list>   <list>
## 1        AL  <tibble [7,956 × 9]> <S3: lm>
## 2        AK  <tibble [2,122 × 9]> <S3: lm>
## 3        AZ  <tibble [8,768 × 9]> <S3: lm>
## 4        AR <tibble [15,350 × 9]> <S3: lm>
## 5        CA <tibble [43,806 × 9]> <S3: lm>
## 6        CO  <tibble [7,372 × 9]> <S3: lm>
## 7        CT  <tibble [9,076 × 9]> <S3: lm>
## 8        DE    <tibble [938 × 9]> <S3: lm>
## 9        DC     <tibble [60 × 9]> <S3: lm>
## 10       FL  <tibble [4,322 × 9]> <S3: lm>
## # ... with 42 more rows
```

----

```r
by_state <- by_state %>% 
	mutate(r2 = map(model, summary) %>% 
				map_dbl("r.squared"))
by_state
```

```
## # A tibble: 52 × 4
##    stateabb                  data    model        r2
##      <fctr>                <list>   <list>     <dbl>
## 1        AL  <tibble [7,956 × 9]> <S3: lm> 0.5292111
## 2        AK  <tibble [2,122 × 9]> <S3: lm> 0.4486915
## 3        AZ  <tibble [8,768 × 9]> <S3: lm> 0.5183148
## 4        AR <tibble [15,350 × 9]> <S3: lm> 0.3704695
## 5        CA <tibble [43,806 × 9]> <S3: lm> 0.3802063
## 6        CO  <tibble [7,372 × 9]> <S3: lm> 0.5869236
## 7        CT  <tibble [9,076 × 9]> <S3: lm> 0.6090015
## 8        DE    <tibble [938 × 9]> <S3: lm> 0.6587978
## 9        DC     <tibble [60 × 9]> <S3: lm> 0.7073657
## 10       FL  <tibble [4,322 × 9]> <S3: lm> 0.6107051
## # ... with 42 more rows
```

----
## Plot results


```r
ggplot(by_state, aes(stateabb, r2)) + geom_point()
```

![plot of chunk plot_r2](assets/fig/plot_r2-1.png)
