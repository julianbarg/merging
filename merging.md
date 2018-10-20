Biased highschool teacher
================
Julian Barg
October 19, 2018

Read data
---------

First, we read a file with student names and attributes.

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
    ## $ Smart        <lgl> NA, FALSE, NA, NA, TRUE, TRUE, TRUE, NA, NA, TRUE...
    ## $ Nice         <lgl> NA, NA, FALSE, FALSE, NA, TRUE, NA, TRUE, TRUE, T...

Next, we read the file with the teachers preferences into memory.

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

Transform dataframe
-------------------

We tranform the students dataframe to the long format.

``` r
library(reshape2)
```

    ## 
    ## Attaching package: 'reshape2'

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     smiths

``` r
students_long <- melt(students, 
                      id.vars=c('First Name', 'Last Name'), 
                      variable.name='Characteristic')
head(students_long)
```

    ##   First Name Last Name Characteristic value
    ## 1      Sarah     Seven           Tall  TRUE
    ## 2       Ming      Cong           Tall FALSE
    ## 3       Amir      <NA>           Tall FALSE
    ## 4      Ellen Macdonald           Tall  TRUE
    ## 5       <NA>       Lee           Tall  TRUE
    ## 6     Sophia        Li           Tall  TRUE

Next, we remove rows where the value is missing or false, because the preferences dataframe only makes statements about TRUE values.

``` r
students_long <- subset(students_long, value==TRUE, drop=TRUE)
head(students_long)
```

    ##   First Name Last Name Characteristic value
    ## 1      Sarah     Seven           Tall  TRUE
    ## 4      Ellen Macdonald           Tall  TRUE
    ## 5       <NA>       Lee           Tall  TRUE
    ## 6     Sophia        Li           Tall  TRUE
    ## 7        Ava     Aight           Tall  TRUE
    ## 9     Vihaan     Smith           Tall  TRUE

We can even drop the value column then.

``` r
students_long <- students_long[ ,1:3]
head(students_long, 2)
```

    ##   First Name Last Name Characteristic
    ## 1      Sarah     Seven           Tall
    ## 4      Ellen Macdonald           Tall

Merging options
---------------

Now, we can use every merging option to join the two dataframe.

### Inner:

``` r
inner <- inner_join(students_long, preferences)
```

    ## Warning: Column `Characteristic` joining factor and character vector,
    ## coercing into character vector

``` r
str(inner)
```

    ## 'data.frame':    23 obs. of  6 variables:
    ##  $ First Name    : chr  "Sarah" "Ellen" NA "Sophia" ...
    ##  $ Last Name     : chr  "Seven" "Macdonald" "Lee" "Li" ...
    ##  $ Characteristic: chr  "Tall" "Tall" "Tall" "Tall" ...
    ##  $ Fail          : logi  TRUE TRUE TRUE TRUE TRUE TRUE ...
    ##  $ Priority      : int  4 4 4 4 4 4 4 2 2 2 ...
    ##  $ Who           : chr  "Seoyun" "Seoyun" "Seoyun" "Seoyun" ...

### Full (outer):

``` r
full <- full_join(students_long, preferences)
```

    ## Warning: Column `Characteristic` joining factor and character vector,
    ## coercing into character vector

``` r
str(full)
```

    ## 'data.frame':    23 obs. of  6 variables:
    ##  $ First Name    : chr  "Sarah" "Ellen" NA "Sophia" ...
    ##  $ Last Name     : chr  "Seven" "Macdonald" "Lee" "Li" ...
    ##  $ Characteristic: chr  "Tall" "Tall" "Tall" "Tall" ...
    ##  $ Fail          : logi  TRUE TRUE TRUE TRUE TRUE TRUE ...
    ##  $ Priority      : int  4 4 4 4 4 4 4 2 2 2 ...
    ##  $ Who           : chr  "Seoyun" "Seoyun" "Seoyun" "Seoyun" ...

### Left:

``` r
left <- left_join(students_long, preferences)
```

    ## Warning: Column `Characteristic` joining factor and character vector,
    ## coercing into character vector

``` r
str(left)
```

    ## 'data.frame':    23 obs. of  6 variables:
    ##  $ First Name    : chr  "Sarah" "Ellen" NA "Sophia" ...
    ##  $ Last Name     : chr  "Seven" "Macdonald" "Lee" "Li" ...
    ##  $ Characteristic: chr  "Tall" "Tall" "Tall" "Tall" ...
    ##  $ Fail          : logi  TRUE TRUE TRUE TRUE TRUE TRUE ...
    ##  $ Priority      : int  4 4 4 4 4 4 4 2 2 2 ...
    ##  $ Who           : chr  "Seoyun" "Seoyun" "Seoyun" "Seoyun" ...

### Right:

``` r
right <- right_join(students_long, preferences)
```

    ## Warning: Column `Characteristic` joining factor and character vector,
    ## coercing into character vector

``` r
str(right)
```

    ## 'data.frame':    23 obs. of  6 variables:
    ##  $ First Name    : chr  "Sarah" "Ellen" NA "Sophia" ...
    ##  $ Last Name     : chr  "Seven" "Macdonald" "Lee" "Li" ...
    ##  $ Characteristic: chr  "Tall" "Tall" "Tall" "Tall" ...
    ##  $ Fail          : logi  TRUE TRUE TRUE TRUE TRUE TRUE ...
    ##  $ Priority      : int  4 4 4 4 4 4 4 2 2 2 ...
    ##  $ Who           : chr  "Seoyun" "Seoyun" "Seoyun" "Seoyun" ...

'Who' column
------------

Notice the unwanted Who column though. It seems the teacher wrote down the name of a student that had the characteristic as an example here. To add those rows, we rename that column and carry out an additional outer join on all rows, which effectively adds all rows from the second dataframe. Then we could clean up the dataframe and obtain our final dataframe.

``` r
join_2 <- union_all(full, rename(preferences, 'First Name' = Who))
join_2 <- join_2[ ,1:5]
print(join_2)
```

    ##    First Name Last Name Characteristic  Fail Priority
    ## 1       Sarah     Seven           Tall  TRUE        4
    ## 2       Ellen Macdonald           Tall  TRUE        4
    ## 3        <NA>       Lee           Tall  TRUE        4
    ## 4      Sophia        Li           Tall  TRUE        4
    ## 5         Ava     Aight           Tall  TRUE        4
    ## 6      Vihaan     Smith           Tall  TRUE        4
    ## 7    Isabella       Six           Tall  TRUE        4
    ## 8       Sarah     Seven      Attentive FALSE        2
    ## 9    Benedict      Five      Attentive FALSE        2
    ## 10      Peter     Three      Attentive FALSE        2
    ## 11       Noah  Buchholz      Attentive FALSE        2
    ## 12       Emel      Abed       Annoying  TRUE        1
    ## 13       Noah  Buchholz       Annoying  TRUE        1
    ## 14       <NA>       Lee          Smart FALSE        3
    ## 15     Sophia        Li          Smart FALSE        3
    ## 16        Ava     Aight          Smart FALSE        3
    ## 17   Isabella       Six          Smart FALSE        3
    ## 18        Zoe      Four          Smart FALSE        3
    ## 19     Sophia        Li           Nice FALSE        5
    ## 20       Emel      Abed           Nice FALSE        5
    ## 21     Vihaan     Smith           Nice FALSE        5
    ## 22   Isabella       Six           Nice FALSE        5
    ## 23      Peter     Three           Nice FALSE        5
    ## 24     Seoyun      <NA>           Tall  TRUE        4
    ## 25       <NA>      <NA>      Attentive FALSE        2
    ## 26        Zoe      <NA>       Annoying  TRUE        1
    ## 27       <NA>      <NA>          Smart FALSE        3
    ## 28       <NA>      <NA>           Nice FALSE        5

Final result
------------

``` r
join_2 %>%
  filter(is.na(`First Name`)==FALSE | is.na(`Last Name`)==FALSE) %>%
  group_by(`First Name`, `Last Name`) %>% 
  filter(Priority == min(Priority)) %>%
  select(-Characteristic) %>%
  arrange(`First Name`, `Last Name`)
```

    ## # A tibble: 14 x 4
    ## # Groups:   First Name, Last Name [14]
    ##    `First Name` `Last Name` Fail  Priority
    ##    <chr>        <chr>       <lgl>    <int>
    ##  1 Ava          Aight       FALSE        3
    ##  2 Benedict     Five        FALSE        2
    ##  3 Ellen        Macdonald   TRUE         4
    ##  4 Emel         Abed        TRUE         1
    ##  5 Isabella     Six         FALSE        3
    ##  6 Noah         Buchholz    TRUE         1
    ##  7 Peter        Three       FALSE        2
    ##  8 Sarah        Seven       FALSE        2
    ##  9 Seoyun       <NA>        TRUE         4
    ## 10 Sophia       Li          FALSE        3
    ## 11 Vihaan       Smith       TRUE         4
    ## 12 Zoe          Four        FALSE        3
    ## 13 Zoe          <NA>        TRUE         1
    ## 14 <NA>         Lee         FALSE        3

Join in python
--------------

``` python
import pandas as pd
preferences_py = r.preferences
students_long_py = r.students_long
pandas_join = pd.merge(preferences_py, students_long_py, on='Characteristic', how='outer')
print(pandas_join)
```

    ##    Characteristic   Fail  Priority     Who First Name  Last Name
    ## 0            Tall   True         4  Seoyun      Sarah      Seven
    ## 1            Tall   True         4  Seoyun      Ellen  Macdonald
    ## 2            Tall   True         4  Seoyun         NA        Lee
    ## 3            Tall   True         4  Seoyun     Sophia         Li
    ## 4            Tall   True         4  Seoyun        Ava      Aight
    ## 5            Tall   True         4  Seoyun     Vihaan      Smith
    ## 6            Tall   True         4  Seoyun   Isabella        Six
    ## 7       Attentive  False         2      NA      Sarah      Seven
    ## 8       Attentive  False         2      NA   Benedict       Five
    ## 9       Attentive  False         2      NA      Peter      Three
    ## 10      Attentive  False         2      NA       Noah   Buchholz
    ## 11       Annoying   True         1     Zoe       Emel       Abed
    ## 12       Annoying   True         1     Zoe       Noah   Buchholz
    ## 13          Smart  False         3      NA         NA        Lee
    ## 14          Smart  False         3      NA     Sophia         Li
    ## 15          Smart  False         3      NA        Ava      Aight
    ## 16          Smart  False         3      NA   Isabella        Six
    ## 17          Smart  False         3      NA        Zoe       Four
    ## 18           Nice  False         5      NA     Sophia         Li
    ## 19           Nice  False         5      NA       Emel       Abed
    ## 20           Nice  False         5      NA     Vihaan      Smith
    ## 21           Nice  False         5      NA   Isabella        Six
    ## 22           Nice  False         5      NA      Peter      Three
