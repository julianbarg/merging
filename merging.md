Biased highschool teacher
================
Julian Barg
October 19, 2018

Read data
=========

First, we read a file with student names

``` r
library(readr)
library(tidyverse)
students <- read_csv('students.csv')
glimpse(students)
```

    ## Observations: 14
    ## Variables: 8
    ## $ `First Name` <chr> "Sarah", "Ming", "Amir", "Ellen", NA, "Sophia", "...
    ## $ `Last Name`  <chr> "Seven", "Cong", NA, "Macdonald", "Lee", "Li", "A...
    ## $ Tall         <lgl> TRUE, FALSE, FALSE, TRUE, TRUE, TRUE, TRUE, FALSE...
    ## $ Gender       <chr> "F", "M", "M", "F", "M", "F", "F", "F", "M", "F",...
    ## $ Attentive    <lgl> TRUE, NA, NA, NA, NA, FALSE, NA, FALSE, NA, NA, T...
    ## $ Annoying     <lgl> NA, NA, NA, NA, FALSE, FALSE, NA, TRUE, NA, FALSE...
    ## $ SMART        <lgl> NA, FALSE, NA, NA, TRUE, TRUE, TRUE, NA, NA, TRUE...
    ## $ NICE         <lgl> NA, NA, FALSE, FALSE, NA, TRUE, NA, TRUE, TRUE, T...

``` r
preferences <- read_csv('preferences.csv')
glimpse(preferences)
```

    ## Observations: 5
    ## Variables: 4
    ## $ Characteristic <chr> "Tall", "Attentive", "Annoying", "Smart", "Nice"
    ## $ Fail           <lgl> TRUE, FALSE, TRUE, FALSE, FALSE
    ## $ Priority       <int> 4, 2, 1, 3, 5
    ## $ Who            <chr> "Seoyun", NA, "Zoe", NA, NA

``` r
library(reshape2)
```

    ## 
    ## Attaching package: 'reshape2'

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     smiths

``` r
students_long <- melt(students, id.vars=c('First Name', 'Last Name'))
head(students_long)
```

    ##   First Name Last Name variable value
    ## 1      Sarah     Seven     Tall  TRUE
    ## 2       Ming      Cong     Tall FALSE
    ## 3       Amir      <NA>     Tall FALSE
    ## 4      Ellen Macdonald     Tall  TRUE
    ## 5       <NA>       Lee     Tall  TRUE
    ## 6     Sophia        Li     Tall  TRUE

``` python
import pandas as pd
preferences_py = r.preferences
print(preferences_py)
```

    ##   Characteristic   Fail  Priority     Who
    ## 0           Tall   True         4  Seoyun
    ## 1      Attentive  False         2      NA
    ## 2       Annoying   True         1     Zoe
    ## 3          Smart  False         3      NA
    ## 4           Nice  False         5      NA
