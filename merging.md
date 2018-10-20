Biased highschool teacher
================
Julian Barg
October 19, 2018

Read data
=========

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
    ## $ Attentive    <lgl> TRUE, TRUE, TRUE, TRUE, FALSE, FALSE, NA, FALSE, ...
    ## $ Annoying     <lgl> TRUE, FALSE, TRUE, FALSE, FALSE, FALSE, NA, TRUE,...
    ## $ SMART        <lgl> FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, TRUE, FALS...
    ## $ NICE         <lgl> FALSE, NA, FALSE, FALSE, FALSE, TRUE, FALSE, TRUE...

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
