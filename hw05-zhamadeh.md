hw05-zhamadeh
================

Lets start by bringing in all of our important packages

``` r
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(tidyverse))
suppressPackageStartupMessages(library(forcats))
suppressPackageStartupMessages(library(knitr))
suppressPackageStartupMessages(library(readr))
suppressPackageStartupMessages(library(scales))
suppressPackageStartupMessages(library(plotly))
```

Part 1 Factor Management
========================

With the data set of your choice, after ensuring the variable(s) you’re exploring are indeed factors, you are expected to:

Drop factor / levels;

-   Reorder levels based on knowledge from data.
-   We’ve elaborated on these steps for the gapminder and singer data sets below.

Be sure to also characterize the (derived) data before and after your factor re-leveling:

-   Explore the effects of arrange(). Does merely arranging the data have any effect on, say, a figure?
-   Explore the effects of reordering a factor and factor reordering coupled with arrange(). Especially, what effect does this have on a figure? These explorations should involve the data, the factor levels, and some figures.

### Elaboration of the gapminder dataset

Lets start out by looking at the variable, continent, in the gapminder dataset. The `class()` method tells us this is a factor variable. The `levels()` method shows us what the factor levels are of the continent variable.

``` r
class(gapminder$continent)
```

    ## [1] "factor"

``` r
levels(gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"   "Oceania"

*Dropping Oceania* First lets remove any observations that have Oceania as their continent by making use of the `filter()` method on the gapminder dataset and store this in a new variable.

``` r
filt_gapminder <- gapminder %>%
  filter(continent != "Oceania") #keeps all continents that are NOT Oceania
```

Looking at our new filtered dataset, we can see that Oceania is still a level in the continent variable but the `table()` function tells us it has zero observations.

``` r
levels(filt_gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"   "Oceania"

``` r
table(filt_gapminder$continent)
```

    ## 
    ##   Africa Americas     Asia   Europe  Oceania 
    ##      624      300      396      360        0

To remove this unused factor level we need to use the `droplevels()` method on our new dataset. A quick table view of the continents and their observation counts tells us Oceania was successfully removed.

``` r
filt_gapminder <- droplevels(filt_gapminder)
table(filt_gapminder$continent)
```

    ## 
    ##   Africa Americas     Asia   Europe 
    ##      624      300      396      360

A quick look at the number of rows in our new dataset compared to the original gapminder gives us more information. There are 1704 rows in the original gapminder dataset and only 1680 in our new filtered one suggesting 24 rows belonged to observations with Oceania as their continent. Comparing our levels of continent also shows that Oceania has been removed from our filtered dataset.

``` r
#difference in row number
nrow(gapminder)
```

    ## [1] 1704

``` r
nrow(filt_gapminder)
```

    ## [1] 1680

``` r
#different in factor levels
levels(gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"   "Oceania"

``` r
levels(filt_gapminder$continent)
```

    ## [1] "Africa"   "Americas" "Asia"     "Europe"

*Reorder the levels of `country` and `continent`* Here we can reorder the levels of our country variable according to another variable, GDP per capita, using the `fct_reorder()` method and then plotting the results.

``` r
filt_gapminder %>%
  mutate(country = fct_reorder(country, gdpPercap)) %>%
  ggplot(aes(gdpPercap, country)) + 
  geom_point()
```

![](hw05-zhamadeh_files/figure-markdown_github/unnamed-chunk-7-1.png)

Alternatively, we can arrange the levels of the continent factor by frequency using the `fct_infreq()` method and printing out the levels to confirm.

``` r
filt_gapminder$continent %>%
  fct_infreq() %>%
  levels()
```

    ## [1] "Africa"   "Asia"     "Europe"   "Americas"

Part 2 File I/O
===============

Lets save our filtered gapminder dataset to a comma seperated values file titled gapminder.csv.

``` r
gap_europe_2007 <- gapminder %>%
  filter(continent == "Europe", year == 2007)

readr::write_csv(gap_europe_2007, "europe_2007.csv")
```

Part 3 Visualization and Design
===============================

``` r
gap_europe_2007 %>%
  ggplot(aes(gdpPercap, lifeExp)) + geom_point(aes(colour=pop))
```

![](hw05-zhamadeh_files/figure-markdown_github/unnamed-chunk-10-1.png)

``` r
gap_europe_2007 %>%
  ggplot(aes(gdpPercap, lifeExp)) + geom_point(aes(colour=pop))+
  scale_x_continuous(
    breaks = 1:10 * 5000) +
  theme_bw()+
  theme(axis.text = element_text(size=8)) + 
  labs(title = "GDP Per Capita vs Life Expectancy in European Countries", x = "GDP Per Capita", y = "Life Expectancy") 
```

![](hw05-zhamadeh_files/figure-markdown_github/unnamed-chunk-10-2.png)

``` r
#ggplot(arrange_asia, aes(lifeExp, fct_reorder(country, lifeExp))) +
#       geom_point()
```

``` r
#readr::write_tsv(arrange_asia, "test.tsv")
```

``` r
#ggsave("graph.png", plot = , device = "png", width = , height = , dpi = 500])
```