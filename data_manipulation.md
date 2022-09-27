Data Manipulation with `dplyr`
================

Once you’ve imported data, you’re going to need to do some cleaning up.

\#setting how to knit - formatting to make the document look nicer

## Overview

### Learning Objectives

Clean and organize data using `dplyr` verbs and piping.

## Example

For this example, I’ll start a new R Markdown file to the repo / project
I started for the Data Wrangling I topic; this will make it easy to load
example data sets using the code I wrote in Data Import.

Once again we’re going to be using the `tidyverse`, so we’ll load that
at the outset. We’re going to be looking at a lot of output, so I’ll
print only three lines of each tibble by default. Lastly, we’ll focus on
the data in `FAS_litters.csv` and `FAS_pups.csv`, so we’ll load those
data and clean up the column names using what we learned in Data Import.

``` r
library(tidyverse) #loading tidyverse
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
## ✔ readr   2.1.2      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()

options(tibble.print_min = 3) #print the first three rows

litters_data = read_csv("./data/FAS_litters.csv") #creates tibble "litters_data"
## Rows: 49 Columns: 8
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): Group, Litter Number
## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

litters_data = janitor::clean_names(litters_data) #cleans up data (janitor package)
#telling R to go into janitor package and calling the specific function clean_names
#"::" means calling and will pull a specific function from package
#makes data look clean and consistent

pups_data = read_csv("./data/FAS_pups.csv")
## Rows: 313 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): Litter Number
## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
pups_data = janitor::clean_names(pups_data)
```

### `select`

\#first verb in the dplyr pacakge For a given analysis, you may only
need a subset of the columns in a data table; extracting only what you
need can helpfully de-clutter, especially when you have large datasets.
Select columns using `select`. \#want to select specific COLUMNS

You can specify the columns you want to keep by naming all of them:

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
## # A tibble: 49 × 4
##   group litter_number gd0_weight pups_born_alive
##   <chr> <chr>              <dbl>           <dbl>
## 1 Con7  #85                 19.7               3
## 2 Con7  #1/2/95/2           27                 8
## 3 Con7  #5/5/3/83/3-3       26                 6
## # … with 46 more rows
```

\#first argument in the function is the dataframe/dataset you are
working on \#in the example above, the data “litters_data” is being used
\#list the dataset/dataframe you want to use first and then the columns
you want to select \#afterwards

\#not changing the dataset \#not overriding the dataset \#just in the
output when looking at the data it will be changed/manipulated \#don’t
want to override the original dataset \#if you want to save to a
dataset/dataframe, save the manipulation to another name

\#shortcut to listing a bunch of columns You can specify the specify a
range of columns to keep:

``` r
select(litters_data, group:gd_of_birth)
## # A tibble: 49 × 5
##   group litter_number gd0_weight gd18_weight gd_of_birth
##   <chr> <chr>              <dbl>       <dbl>       <dbl>
## 1 Con7  #85                 19.7        34.7          20
## 2 Con7  #1/2/95/2           27          42            19
## 3 Con7  #5/5/3/83/3-3       26          41.4          19
## # … with 46 more rows
```

\#select all the variables between group and gd_of_birth \#R is looking
at the positioning of the variables in the dataset \#in this example,
will take the first 5 columns

\#removing columnms You can also specify columns you’d like to remove:

``` r
select(litters_data, -pups_survive)
## # A tibble: 49 × 7
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive pups_…¹
##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20               3       4
## 2 Con7  #1/2/95/2           27          42            19               8       0
## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6       0
## # … with 46 more rows, and abbreviated variable name ¹​pups_dead_birth
```

\#use the “-” sign in front of the colummn you would like to remove

You can rename variables as part of this process:

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
## # A tibble: 49 × 2
##   GROUP LiTtEr_NuMbEr
##   <chr> <chr>        
## 1 Con7  #85          
## 2 Con7  #1/2/95/2    
## 3 Con7  #5/5/3/83/3-3
## # … with 46 more rows
```

\#left side of the equal sign = name that it is currently listed under
\#right side of the equal sign = name that you would like to rename to

If all you want to do is rename something, you can use `rename` instead
of `select`. This will rename the variables you care about, and keep
everything else:

``` r
rename(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
## # A tibble: 49 × 8
##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
##   <chr> <chr>              <dbl>       <dbl>       <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20       3       4       3
## 2 Con7  #1/2/95/2           27          42            19       8       0       7
## 3 Con7  #5/5/3/83/3-3       26          41.4          19       6       0       5
## # … with 46 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth, ³​pups_survive
```

There are some handy helper functions for `select`; read about all of
them using `?select_helpers`. I use `starts_with()`, `ends_with()`, and
`contains()` often, especially when there variables are named with
suffixes or other standard patterns:

``` r
select(litters_data, starts_with("gd"))
## # A tibble: 49 × 3
##   gd0_weight gd18_weight gd_of_birth
##        <dbl>       <dbl>       <dbl>
## 1       19.7        34.7          20
## 2       27          42            19
## 3       26          41.4          19
## # … with 46 more rows
select(litters_data, ends_with("weight"))
## # A tibble: 49 × 2
##   gd0_weight gd18_weight
##        <dbl>       <dbl>
## 1       19.7        34.7
## 2       27          42  
## 3       26          41.4
## # … with 46 more rows
```

I also frequently use is `everything()`, which is handy for reorganizing
columns without discarding anything:

``` r
select(litters_data, litter_number, pups_survive, everything())
## # A tibble: 49 × 8
##   litter_number pups_survive group gd0_weight gd18_wei…¹ gd_of…² pups_…³ pups_…⁴
##   <chr>                <dbl> <chr>      <dbl>      <dbl>   <dbl>   <dbl>   <dbl>
## 1 #85                      3 Con7        19.7       34.7      20       3       4
## 2 #1/2/95/2                7 Con7        27         42        19       8       0
## 3 #5/5/3/83/3-3            5 Con7        26         41.4      19       6       0
## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth
```

\#take dataframe and pulling variables you want to the front \#changing
the ordering of the variables \#using “everything()” to keep all other
variables \#this statement is taking the two variables listed and
pulling it to the front and putting \#everything else to the back
(listing after the two variables listed)

Lastly, like other functions in `dplyr`, `select` will export a
dataframe even if you only select one column. Mostly this is fine, but
sometimes you want the vector stored in the column. To pull a single
variable, use `pull`.

### `filter`

\#focusing on rows \#first argument in the () is the dataset/tibble you
want to work on \#needs to state a specific criteria using logical
expressions

Some data tables will include rows you don’t need for your current
analysis. Although you could remove specific row numbers using base R,
you shouldn’t – this might break if the raw data are updated, and the
thought process isn’t transparent. Instead, you should filter rows based
on logical expressions using the `filter` function. Like `select`, the
first argument to `filter` is the dataframe you’re filtering; all
subsequent arguments are logical expressions.

You will often filter using comparison operators (`>`, `>=`, `<`, `<=`,
`==`, and `!=`). You may also use `%in%` to detect if values appear in a
set, and `is.na()` to find missing values. The results of comparisons
are logical – the statement is `TRUE` or `FALSE` depending on the values
you compare – and can be combined with other comparisons using the
logical operators `&` and `|`, or negated using `!`. \#single “=” is
assigning something to something \#double “==” means it is equal to

\#`%in%` –\> used to see if something is present within the dataset
\#`is.na()` –\> used to find missing values

Some ways you might filter the litters data are:

-   `gd_of_birth == 20` \#if this is true, it will be placed in the new
    dataset \#if this is false, it will be dropped

-   `pups_born_alive >= 2`

-   `pups_survive != 4`

-   `!(pups_survive == 4)` \#selecting all the rows/filter the rows
    where the number of pups is anything but 4 \#negating a whole
    expression using “!” \#will do the same thing as pups_survive != 4

\*`!((pups_survive == 4) & (gd_of_birth == 20))` \#take all of the rows
where all the number of pups_of_survive = 4 AND gd_of_birth = 28 \#they
will be dropped/filtered out

-   `group %in% c("Con7", "Con8")` \#select all the rows where group is
    equal to Con7 or Con8

-   `group == "Con7" & gd_of_birth == 20`

Let’s try one or two…

``` r
filter(litters_data, gd_of_birth == 20)
## # A tibble: 32 × 8
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² pups_…³
##   <chr> <chr>              <dbl>       <dbl>       <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                 19.7        34.7          20       3       4       3
## 2 Con7  #4/2/95/3-3         NA          NA            20       6       0       6
## 3 Con7  #2/2/95/3-2         NA          NA            20       6       0       4
## # … with 29 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth, ³​pups_survive
```

``` r
filter(litters_data, group == "Con7" & gd_of_birth == 20)
## # A tibble: 4 × 8
##   group litter_number   gd0_weight gd18_weight gd_of_b…¹ pups_…² pups_…³ pups_…⁴
##   <chr> <chr>                <dbl>       <dbl>     <dbl>   <dbl>   <dbl>   <dbl>
## 1 Con7  #85                   19.7        34.7        20       3       4       3
## 2 Con7  #4/2/95/3-3           NA          NA          20       6       0       6
## 3 Con7  #2/2/95/3-2           NA          NA          20       6       0       4
## 4 Con7  #1/5/3/83/3-3/2       NA          NA          20       9       0       9
## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
## #   ³​pups_dead_birth, ⁴​pups_survive
```

\#by using filter, selecting rows not columns

A very common filtering step requires you to omit missing observations.
You *can* do this with `filter`, but I recommend using `drop_na` from
the `tidyr` package:

\#subfunctions that are better at working with missing values \*
`drop_na(litters_data)` will remove any row with a missing value
\#delete any row with any missing data in any variable \#global drop of
missing data

-   `drop_na(litters_data, wt_increase)` will remove rows for which
    `wt_increase` is missing. \#focus on variable wt_increase \#will
    drop rows that have missing values based on this one variable

Filtering can be helpful for limiting a dataset to only those
observations needed for an analysis. However, I recommend against the
creation of many data subsets (e.g. one for each group). This can
clutter up your workspace, and we’ll see good tools for the analysis of
subsets before long.

### `mutate`

Sometimes you need to select columns; sometimes you need to change them
or create new ones. You can do this using `mutate`.

The example below creates a new variable measuring the difference
between `gd18_weight` and `gd0_weight` and modifies the existing `group`
variable.

``` r
#litter_data2 =
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group),
 # wt_gain_kg = wt_gain * 2.2 can also include this step into this dataframe
 #R allows you to string together multiple functions and will carry them out
)
## # A tibble: 49 × 9
##   group litter_number gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴ pups_…⁵ wt_gain
##   <chr> <chr>              <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
## 1 con7  #85                 19.7    34.7      20       3       4       3    15  
## 2 con7  #1/2/95/2           27      42        19       8       0       7    15  
## 3 con7  #5/5/3/83/3-3       26      41.4      19       6       0       5    15.4
## # … with 46 more rows, and abbreviated variable names ¹​gd18_weight,
## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth, ⁵​pups_survive
```

\#str = string \#str_to_lower(group) = changing variables to lower case

A few things in this example are worth noting:

-   Your new variables can be functions of old variables
-   New variables appear at the end of the dataset in the order that
    they are created
-   You can overwrite old variables
-   You can create a new variable and immediately refer to (or change)
    it

Creating a new variable that does exactly what you need can be a
challenge; the more functions you know about, the easier this gets.

### `arrange`

\#rearranges rows in data \#resorting the data In comparison to the
preceding, arranging is pretty straightforward. You can arrange the rows
in your data according to the values in one or more columns:

``` r
head(arrange(litters_data, group, pups_born_alive), 10)
## # A tibble: 10 × 8
##    group litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
##    <chr> <chr>                <dbl>       <dbl>    <dbl>   <dbl>   <dbl>   <dbl>
##  1 Con7  #85                   19.7        34.7       20       3       4       3
##  2 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
##  4 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
##  5 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
##  6 Con7  #1/2/95/2             27          42         19       8       0       7
##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
##  8 Con8  #2/2/95/2             NA          NA         19       5       0       4
##  9 Con8  #1/6/2/2/95-2         NA          NA         20       7       0       6
## 10 Con8  #3/6/2/2/95-3         NA          NA         20       7       0       7
## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
## #   ³​pups_dead_birth, ⁴​pups_survive
```

\#sort on group first then pups_born_alive second \#head() = will print
the first 10 observations

\#in this example, sorting by alphabetical for group and ascending by
pups_born_alive \#ascending: A to Z / smallest to largest \#descending :
Z to A / largest to smallest

You can also sort in descending order if you’d like.

``` r
head(arrange(litters_data, desc(group), pups_born_alive), 10)
## # A tibble: 10 × 8
##    group litter_number gd0_weight gd18_weight gd_of_bi…¹ pups_…² pups_…³ pups_…⁴
##    <chr> <chr>              <dbl>       <dbl>      <dbl>   <dbl>   <dbl>   <dbl>
##  1 Mod8  #7/82-3-2           26.9        43.2         20       7       0       7
##  2 Mod8  #97                 24.5        42.8         20       8       1       8
##  3 Mod8  #5/93/2             NA          NA           19       8       0       8
##  4 Mod8  #7/110/3-2          27.5        46           19       8       1       8
##  5 Mod8  #82/4               33.4        52.7         20       8       0       6
##  6 Mod8  #2/95/2             28.5        44.5         20       9       0       9
##  7 Mod8  #5/93               NA          41.1         20      11       0       9
##  8 Mod7  #3/82/3-2           28          45.9         20       5       0       5
##  9 Mod7  #5/3/83/5-2         22.6        37           19       5       0       5
## 10 Mod7  #106                21.7        37.8         20       5       0       2
## # … with abbreviated variable names ¹​gd_of_birth, ²​pups_born_alive,
## #   ³​pups_dead_birth, ⁴​pups_survive
```

\#by default, in ascending order \#need to use asc to go the other way

### `%>%`

\#piping \#connect multiple data manipulations/functions together
\#output a single dataset

We’ve seen several commands you will use regularly for data manipulation
and cleaning. You will rarely use them in isolation. For example,
suppose you want to load the data, clean the column names, remove
`pups_survive`, and create `wt_gain`. There are a couple of options for
this kind of multi-step data manipulation:

-   define intermediate datasets (or overwrite data at each stage)
-   nest function calls

The following is an example of the first option:

``` r
litters_data_raw = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data_clean_names = janitor::clean_names(litters_data_raw)
litters_data_selected_cols = select(litters_data_clean_names, -pups_survive)
litters_data_with_vars = 
  mutate(
    litters_data_selected_cols, 
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group))
litters_data_with_vars_without_missing = 
  drop_na(litters_data_with_vars, wt_gain)
litters_data_with_vars_without_missing
## # A tibble: 31 × 8
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
## 1 con7  #85                 19.7        34.7          20       3       4    15  
## 2 con7  #1/2/95/2           27          42            19       8       0    15  
## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth
```

\#1st option: create multiple iterations of the dataset \#this is not
the best option because it is a bit more complicated and hard to read
\#want to avoid doing this in practice

Below, we try the second option: \#this is nesting function within a
function \#also not the best \#working backwards and may get confusing
\#clunky and hard to read \#so many parentheses and may end up being
difficult to keep track of

``` r
litters_data_clean = 
  drop_na(
    mutate(
      select(
        janitor::clean_names(
          read_csv("./data/FAS_litters.csv", col_types = "ccddiiii")
          ), 
      -pups_survive
      ),
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)
    ),
  wt_gain
  )
litters_data_clean
## # A tibble: 31 × 8
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
## 1 con7  #85                 19.7        34.7          20       3       4    15  
## 2 con7  #1/2/95/2           27          42            19       8       0    15  
## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth
```

These are both confusing and bad: the first gets confusing and clutters
our workspace, and the second has to be read inside out.

\#best solution - use piping `%>%` Piping solves this problem. It allows
you to turn the nested approach into a sequential chain by passing the
result of one function call as an argument to the next function call:

``` r
litters_data = 
  #assigning all the following functions into this tibble called "litters_data"
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>% 
      #creates tibble and " %>% " will pipe into the next function
  janitor::clean_names() %>% 
      #R knows the previous line is a tibble that will be used in this line
      #key to piping
  select(-pups_survive) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(wt_gain) #want to save this tibble and that is done on the first line
#results in one dataset 
litters_data
## # A tibble: 31 × 8
##   group litter_number gd0_weight gd18_weight gd_of_birth pups_…¹ pups_…² wt_gain
##   <chr> <chr>              <dbl>       <dbl>       <int>   <int>   <int>   <dbl>
## 1 con7  #85                 19.7        34.7          20       3       4    15  
## 2 con7  #1/2/95/2           27          42            19       8       0    15  
## 3 con7  #5/5/3/83/3-3       26          41.4          19       6       0    15.4
## # … with 28 more rows, and abbreviated variable names ¹​pups_born_alive,
## #   ²​pups_dead_birth
```

\#stringing together multiple functions \#tibble in, tibble out = all
designed in this matter in dplyr \#first argument in each function is a
tibble - piping works

\#%\>% shorthand: command + shift + m

All three approaches result in the same dataset, but the piped commands
are by far the most straightforward. The easiest way to read `%>%` is
“then”; the keyboard shortcuts are Ctrl + Shift + M (Windows) and Cmd +
Shift + M (Mac).

The functions in `dplyr` (and much of the `tidyverse`) are designed to
work smoothly with the pipe operator. By default, the pipe will take the
result of one function call and use that as the first argument of the
next function call; by design, functions in `dplyr` will take a tibble
as an input and return a tibble as a result. As a consequence, functions
in `dplyr` are easy to connect in a data cleaning chain. You can make
this more explicit by using `.` as a placeholder for the result of the
preceding call:

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") %>%
  janitor::clean_names(dat = .) %>%
  select(.data = ., -pups_survive) %>%
  mutate(.data = .,
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) %>% 
  drop_na(data = ., wt_gain)
```

In this example, the dataset argument is called `dat` in
`janitor::clean_names`, `.data` in the `dplyr` functions, and `data` in
`drop_na` – which is definitely confusing. In the majority of cases (and
everywhere in the tidyverse) you’ll elide the first argument and be
happy with life, but there are some cases where the placeholder is
necessary. For example, to regress `wt_gain` on `pups_born_alive`, you
might use:

``` r
litters_data %>%
  lm(wt_gain ~ pups_born_alive, data = .) %>%
  broom::tidy()
## # A tibble: 2 × 5
##   term            estimate std.error statistic  p.value
##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3
```

\#lm = linear model (not a tidyverse function - exists on base R)
\#however, piping can be used \#linear model on weight gain \#specify
data with “data=” \#data = . –\> telling R to use previous thing and put
it in this function \#use the tibble that I just created and store that
has the data \#pipe the result into “broom::tidy()” –\> taking lm()
output and making it look nice

There are limitations to the pipe. You shouldn’t have sequences that are
too long; there isn’t a great way to deal with multiple inputs and
outputs; and (since it’s not base R) not everyone will know what `%>%`
means or does. That said, compared to days when R users only had the
first two options, life is much better!
