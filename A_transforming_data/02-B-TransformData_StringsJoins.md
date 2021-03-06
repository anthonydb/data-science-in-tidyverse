Transforming Data, Part B - NICAR 2019
================
Aaron Kessler
(March 9, 2019 - NICAR Conference, Newport Beach, CA)

load the packages we’ll
    need

``` r
library(tidyverse) # we'll use the stringr package from tidyverse
```

    ## -- Attaching packages ------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0       v purrr   0.3.0  
    ## v tibble  2.0.1       v dplyr   0.8.0.1
    ## v tidyr   0.8.2       v stringr 1.4.0  
    ## v readr   1.3.1       v forcats 0.4.0

    ## -- Conflicts ---------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

``` r
library(janitor)
```

load in previous data of prez candidate campaign trips - we’ll get back
to this in a minute

``` r
events <- readRDS("events_saved.rds")
```

### String Functions - Using the `stringr` package

Each function starts with `str_`

  - `str_length()` figure out length of string
  - `str_c()` combine strings
  - `str_sub()` substitute string
  - `str_detect()` detect string in string
  - `str_match()` does string match
  - `str_count()` count strings
  - `str_split()` split strings
  - `str_to_upper()` convert string to upper case
  - `str_to_lower()` convert string to lower case
  - `str_to_title()` convert the first letter of each word to upper case
  - `str_trim()` eliminate trailing white space \#\# Let’s load this
    data in

<!-- end list -->

``` r
messy <- tibble(name=c("Bill Smith", "jane doe", "John Forest-William"),
      email=c("bsmith@themail.com", "jdoe@themail.com", "jfwilliams$geemail.com"),
      income=c("$90,000", "$140,000", "E8500"),
      phone=c("(203) 847-334", "207-999-1122", "2128487345"),
      activites=c("fishing, sailing, planting flowers", "reading, raising flowers, biking", "hiking, fishing"))

messy
```

    ## # A tibble: 3 x 5
    ##   name           email            income  phone     activites              
    ##   <chr>          <chr>            <chr>   <chr>     <chr>                  
    ## 1 Bill Smith     bsmith@themail.~ $90,000 (203) 84~ fishing, sailing, plan~
    ## 2 jane doe       jdoe@themail.com $140,0~ 207-999-~ reading, raising flowe~
    ## 3 John Forest-W~ jfwilliams$geem~ E8500   21284873~ hiking, fishing

What problems do you see?  
*Tasks*

1.  Split name into First name and Last name
2.  Convert names to title case
3.  Create a new variable identifying bad email addresses
4.  Convert income to a new number in US dollars
5.  Create a new variable containing area code
6.  Creating a new variable counting how many activities each person is
    engaged in
7.  Break activities into a set of useful dummy codes

**String length**  
`str_length(string)` counts the number of characters in each element of
a string or character vector.

``` r
x <- c("Bill", "Bob", "William")
str_length(x)
```

    ## [1] 4 3 7

**Combine strings**  
`str_c(strings, sep="")`  
It’s like the equivalent of =concatenate in Excel.  
But there are a couple of quirks

``` r
data <- tibble(place=c("HQ", "HQ", "HQ"),
              id=c("A", "B", "C"),
              number=c("001", "002", "003"))

data
```

    ## # A tibble: 3 x 3
    ##   place id    number
    ##   <chr> <chr> <chr> 
    ## 1 HQ    A     001   
    ## 2 HQ    B     002   
    ## 3 HQ    C     003

We can add a string to each value in the *number* column this way:

``` r
data %>% 
  mutate(combined=str_c("Num: ", number))
```

    ## # A tibble: 3 x 4
    ##   place id    number combined
    ##   <chr> <chr> <chr>  <chr>   
    ## 1 HQ    A     001    Num: 001
    ## 2 HQ    B     002    Num: 002
    ## 3 HQ    C     003    Num: 003

You can pass the variable `collapse` to `str_c()` if you’re turning an
array of strings into one.

``` r
data %>% 
    group_by(place) %>% 
    summarize(ids_combined=str_c(number, collapse="-"))
```

    ## # A tibble: 1 x 2
    ##   place ids_combined
    ##   <chr> <chr>       
    ## 1 HQ    001-002-003

**subset strings**  
`str_sub(strings, start, end)` extracts and replaces substrings

``` r
x <- "Dr. James"

str_sub(x, 1, 3)
```

    ## [1] "Dr."

``` r
str_sub(x, 1, 3) <- "Mr."
x
```

    ## [1] "Mr. James"

Negative numbers count from the right.

``` r
x <- "baby"
str_sub(x, -3, -1)
```

    ## [1] "aby"

``` r
str_sub(x, -1, -1) <- "ies"
x
```

    ## [1] "babies"

**change case**

  - `str_to_upper(strings)` is upper case
  - `str_to_lower(strings)` is lower case
  - `str_to_title(strings)` is title case

<!-- end list -->

``` r
x <- c("john smith", "Mary Todd", "BILL HOLLIS")

str_to_upper(x)
```

    ## [1] "JOHN SMITH"  "MARY TODD"   "BILL HOLLIS"

``` r
str_to_lower(x)
```

    ## [1] "john smith"  "mary todd"   "bill hollis"

``` r
str_to_title(x)
```

    ## [1] "John Smith"  "Mary Todd"   "Bill Hollis"

**trim strings**  
`str_trim(strings)` remove white space at the beginning and end of
string

``` r
x <- c(" Assault", "Burglary ", " Kidnapping ")

str_trim(x)
```

    ## [1] "Assault"    "Burglary"   "Kidnapping"

**detect matches**  
`str_detect(strings, pattern)` returns T/F

``` r
x <- c("Bill", "Bob", "David.Williams")
x
```

    ## [1] "Bill"           "Bob"            "David.Williams"

``` r
str_detect(x, "il")
```

    ## [1]  TRUE FALSE  TRUE

**count matches**  
`str_count(strings, pattern)` count number of matches in a string

``` r
x <- c("Assault/Robbery/Kidnapping")
x
```

    ## [1] "Assault/Robbery/Kidnapping"

``` r
str_count(x, "/")
```

    ## [1] 2

How many offenses

``` r
str_count(x, "/") + 1
```

    ## [1] 3

**extract matches** using regular
expressions

``` r
x <- c("bsmith@microsoft.com", "jdoe@google.com", "jfwilliams@google.com")

str_extract(x, "@.+\\.com$")
```

    ## [1] "@microsoft.com" "@google.com"    "@google.com"

**split strings**  
`str_split(string, pattern)` split a string into pieces

``` r
x <- c("john smith", "mary todd", "bill holis")

str_split(x, " ", simplify=TRUE)
```

    ##      [,1]   [,2]   
    ## [1,] "john" "smith"
    ## [2,] "mary" "todd" 
    ## [3,] "bill" "holis"

``` r
first <- str_split(x, " ", simplify=TRUE)[,1]
last  <- str_split(x, " ", simplify=TRUE)[,2]
```

**replace a pattern**  
`str_replace(strings, pattern, replacement)`  
replace a pattern in a string with another string

``` r
x <- c("john smith", "mary todd", "bill holis")

str_replace(x, "[aeiou]", "-")
```

    ## [1] "j-hn smith" "m-ry todd"  "b-ll holis"

``` r
str_replace_all(x, "[aeiou]", "-")
```

    ## [1] "j-hn sm-th" "m-ry t-dd"  "b-ll h-l-s"

### Back to our campaign data

``` r
events
```

    ## # A tibble: 88 x 7
    ##    date       cand_restname cand_lastname city  state event_type
    ##    <date>     <chr>         <chr>         <chr> <chr> <chr>     
    ##  1 2010-02-06 John          Delaney       Salt~ UT    event spe~
    ##  2 2019-01-31 Sherrod       Brown         Perry IA    meet and ~
    ##  3 2019-01-31 Marianne      Williamson    Des ~ IA    campaign ~
    ##  4 2019-01-31 Eric          Swalwell      Exet~ NH    meet and ~
    ##  5 2019-01-31 John          Hickenlooper  Wash~ DC    event spe~
    ##  6 2019-01-30 John          Delaney       Coun~ IA    campaign ~
    ##  7 2019-01-30 Sherrod       Brown         Clev~ OH    rally     
    ##  8 2019-01-30 Howard        Schultz       Tempe AZ    event spe~
    ##  9 2019-01-29 Beto          O'Rourke      El P~ TX    meet and ~
    ## 10 2019-01-29 Michael       Bloomberg     Manc~ NH    event spe~
    ## # ... with 78 more rows, and 1 more variable: description <chr>

now let’s use string functions to standardize a few event types

``` r
events %>%
  select(event_type) %>% 
  mutate(new_type = case_when(
    str_detect(event_type, "speech") ~ "speech")
  ) 
```

    ## # A tibble: 88 x 2
    ##    event_type                   new_type
    ##    <chr>                        <chr>   
    ##  1 event speech                 speech  
    ##  2 meet and greet               <NA>    
    ##  3 campaign announcement        <NA>    
    ##  4 meet and greet               <NA>    
    ##  5 event speech                 speech  
    ##  6 campaign events              <NA>    
    ##  7 rally                        <NA>    
    ##  8 event speech                 speech  
    ##  9 meet and greet               <NA>    
    ## 10 event speech, meet and greet speech  
    ## # ... with 78 more rows

could there be a problem here?  
multiples?

``` r
events %>%
  select(event_type) %>% 
  mutate(new_type = case_when(
    str_detect(event_type, ",") ~ "multiple",
    str_detect(event_type, "speech") ~ "speech",
    str_detect(event_type, "event") ~ "unspecified event",
    str_detect(event_type, "forum") ~ "town hall",
    str_detect(event_type, "town hall") ~ "town hall"
    )
  ) 
```

    ## # A tibble: 88 x 2
    ##    event_type                   new_type         
    ##    <chr>                        <chr>            
    ##  1 event speech                 speech           
    ##  2 meet and greet               <NA>             
    ##  3 campaign announcement        <NA>             
    ##  4 meet and greet               <NA>             
    ##  5 event speech                 speech           
    ##  6 campaign events              unspecified event
    ##  7 rally                        <NA>             
    ##  8 event speech                 speech           
    ##  9 meet and greet               <NA>             
    ## 10 event speech, meet and greet multiple         
    ## # ... with 78 more rows

Notice that in the example above, the search for comma comes first, not
last

We can also use our string functions for filtering  
Let’s see what that might look like

``` r
events %>% 
  filter(str_detect(event_type, "event"))
```

    ## # A tibble: 50 x 7
    ##    date       cand_restname cand_lastname city  state event_type
    ##    <date>     <chr>         <chr>         <chr> <chr> <chr>     
    ##  1 2010-02-06 John          Delaney       Salt~ UT    event spe~
    ##  2 2019-01-31 John          Hickenlooper  Wash~ DC    event spe~
    ##  3 2019-01-30 John          Delaney       Coun~ IA    campaign ~
    ##  4 2019-01-30 Howard        Schultz       Tempe AZ    event spe~
    ##  5 2019-01-29 Michael       Bloomberg     Manc~ NH    event spe~
    ##  6 2019-01-25 Kamala        Harris        Colu~ SC    event spe~
    ##  7 2019-01-25 Michael       Bloomberg     Wash~ DC    event spe~
    ##  8 2019-01-25 John          Delaney       Des ~ IA    event spe~
    ##  9 2019-01-24 Joe           Biden         Wash~ DC    event spe~
    ## 10 2019-01-24 Eric          Garcetti      Wash~ DC    event spe~
    ## # ... with 40 more rows, and 1 more variable: description <chr>

That’s *kinda* helpful, but is there a column this could be even more
useful for?

Examine the descriptions

``` r
events %>% 
  select(cand_restname, description) 
```

    ## # A tibble: 88 x 2
    ##    cand_restname description                                               
    ##    <chr>         <chr>                                                     
    ##  1 John          Delaney will speak at the Sorenson Winter Innovation Summ~
    ##  2 Sherrod       Roundtable with local farmers in Perry, IA and meet and g~
    ##  3 Marianne      Campaign announcement                                     
    ##  4 Eric          Meet and greet with Rockingham County Democrats           
    ##  5 John          Brookings institution Hamilton Project                    
    ##  6 John          Meet & greets and campaign office openings                
    ##  7 Sherrod       Dignity of Work tour                                      
    ##  8 Howard        Discussion with ASU students                              
    ##  9 Beto          meet and greet with UTEP students                         
    ## 10 Michael       Saint Anselm Institute of Politics, factory tour and hous~
    ## # ... with 78 more rows

What we we want to find descriptions mentioning students

``` r
events %>% 
  select(cand_restname, description) %>% 
  filter(str_detect(description, "student"))
```

    ## # A tibble: 6 x 2
    ##   cand_restname description                                                
    ##   <chr>         <chr>                                                      
    ## 1 Howard        Discussion with ASU students                               
    ## 2 Beto          meet and greet with UTEP students                          
    ## 3 Jay           League of Conservation Voters, speech to Dartmouth and Sai~
    ## 4 Bernie        SC NAACP MLK Day program, Conversation with students at Be~
    ## 5 Cory          InspireNOLA and Rep. Cedric Richmond’s “Project LIVE & Ach~
    ## 6 Beto          meet and greet with Pueblo Community College students

How about anything referencing the NAACP

``` r
events %>% 
  select(cand_restname, description) %>% 
  filter(str_detect(description, "NAACP"))
```

    ## # A tibble: 3 x 2
    ##   cand_restname description                                                
    ##   <chr>         <chr>                                                      
    ## 1 Bernie        SC NAACP MLK Day program, Conversation with students at Be~
    ## 2 Cory          SC NAACP MLK Day program                                   
    ## 3 Kamala        NAACP Women in Power town hall

Remember: R is *case-sensitive*.  
Could an acronym like that possibly cause us trouble?

If so, how might we solve the issue of case sensitivity?

``` r
events %>% 
  select(cand_restname, description) %>% 
  filter(str_detect(str_to_lower(description), "naacp"))
```

    ## # A tibble: 3 x 2
    ##   cand_restname description                                                
    ##   <chr>         <chr>                                                      
    ## 1 Bernie        SC NAACP MLK Day program, Conversation with students at Be~
    ## 2 Cory          SC NAACP MLK Day program                                   
    ## 3 Kamala        NAACP Women in Power town hall

This method is a good strategy to use almost anytime you’re searching in
this way

Even when you don’t think you’ll need it, you never know.

Let’s look at an example

``` r
events %>% 
  filter(str_detect(description, "border"))
```

    ## # A tibble: 0 x 7
    ## # ... with 7 variables: date <date>, cand_restname <chr>,
    ## #   cand_lastname <chr>, city <chr>, state <chr>, event_type <chr>,
    ## #   description <chr>

No results. Or are there?

``` r
events %>% 
  filter(str_detect(str_to_lower(description), "border"))
```

    ## # A tibble: 3 x 7
    ##   date       cand_restname cand_lastname city  state event_type description
    ##   <date>     <chr>         <chr>         <chr> <chr> <chr>      <chr>      
    ## 1 2018-12-23 Beto          O'Rourke      Torn~ TX    tour       Border vis~
    ## 2 2018-12-15 Jeff          Merkley       Torn~ TX    tour       Border vis~
    ## 3 2018-12-15 Beto          O'Rourke      Torn~ TX    tour       Border vis~

You can also do the reverse for the case, with the same goal.

``` r
events %>% 
  filter(str_detect(str_to_upper(description), "BORDER"))
```

    ## # A tibble: 3 x 7
    ##   date       cand_restname cand_lastname city  state event_type description
    ##   <date>     <chr>         <chr>         <chr> <chr> <chr>      <chr>      
    ## 1 2018-12-23 Beto          O'Rourke      Torn~ TX    tour       Border vis~
    ## 2 2018-12-15 Jeff          Merkley       Torn~ TX    tour       Border vis~
    ## 3 2018-12-15 Beto          O'Rourke      Torn~ TX    tour       Border vis~

### Joining Tables

One of the most powerful things about relational data being able to join
tables together.

Let’s take a look at how to do that with dpylr and the tidyverse.

First, let’s bring in some new data:

``` r
key_house_results <- readRDS("key_house_results.rds") 
key_house_historical <- readRDS("key_house_historical.rds") 
```

What do we have here? Let’s take a look and discuss.

``` r
key_house_results
```

    ## # A tibble: 104 x 7
    ##    house_dist keyrace_rating  flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>           <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 AZ-01      lean democratic N             53.8         46.2 D         7.6
    ##  2 AZ-02      likely democra~ Y             54.7         45.3 D         9.4
    ##  3 AZ-06      likely republi~ N             44.8         55.2 R        10.4
    ##  4 AZ-08      likely republi~ N             44.5         55.5 R        11  
    ##  5 AZ-09      likely democra~ N             61.1         38.9 D        22.2
    ##  6 AR-02      lean republican N             45.8         52.1 R         6.3
    ##  7 CA-04      likely republi~ N             45.9         54.1 R         8.2
    ##  8 CA-10      tossup          Y             52.3         47.7 D         4.6
    ##  9 CA-21      likely republi~ Y             50.4         49.6 D         0.8
    ## 10 CA-25      tossup          Y             54.4         45.6 D         8.8
    ## # ... with 94 more rows

``` r
key_house_historical
```

    ## # A tibble: 104 x 9
    ##    house_dist former_party pct_college pct_college_abo~ median_income
    ##    <chr>      <chr>              <dbl> <chr>                    <dbl>
    ##  1 AZ-01      D                  23.6  BELOW                    48583
    ##  2 AZ-02      R                  33.0  ABOVE                    48125
    ##  3 AZ-06      R                  42.9  ABOVE                    64223
    ##  4 AZ-08      R                  28.3  BELOW                    60342
    ##  5 AZ-09      D                  36.7  ABOVE                    52245
    ##  6 AR-02      R                  28.4  BELOW                    48103
    ##  7 CA-04      R                  32.2  ABOVE                    69051
    ##  8 CA-10      R                  17.2  BELOW                    56256
    ##  9 CA-21      R                   8.13 BELOW                    38462
    ## 10 CA-25      R                  27.3  BELOW                    73819
    ## # ... with 94 more rows, and 4 more variables:
    ## #   median_income_abovebelow_natl <chr>, prez_winner_2016 <chr>,
    ## #   trump_vote_pct <dbl>, clinton_vote_pct <dbl>

``` r
#` This is a common thing to see - ables designed to be joined together based on a common key.
```

In this case, we have the house district itself as the common key
between the two tables.

We’ll use dplyr’s `inner_join()` function to match the tables based on
that column.  
Let’s see how that works

``` r
inner_join(key_house_results, key_house_historical)
```

    ## Joining, by = "house_dist"

    ## # A tibble: 104 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 AZ-01      lean democrat~ N             53.8         46.2 D         7.6
    ##  2 AZ-02      likely democr~ Y             54.7         45.3 D         9.4
    ##  3 AZ-06      likely republ~ N             44.8         55.2 R        10.4
    ##  4 AZ-08      likely republ~ N             44.5         55.5 R        11  
    ##  5 AZ-09      likely democr~ N             61.1         38.9 D        22.2
    ##  6 AR-02      lean republic~ N             45.8         52.1 R         6.3
    ##  7 CA-04      likely republ~ N             45.9         54.1 R         8.2
    ##  8 CA-10      tossup         Y             52.3         47.7 D         4.6
    ##  9 CA-21      likely republ~ Y             50.4         49.6 D         0.8
    ## 10 CA-25      tossup         Y             54.4         45.6 D         8.8
    ## # ... with 94 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

Wait, that’s it? We haven’t even told it what to join on.  
That’s because if the two tables share columns with the same name, it
defaults to use them for the join.

If you need to specific which columns in each table to match together,
you do it like
this:

``` r
# inner_join(table1, table2, by = c("table1_columnname" = "table2_columnname"))
```

We can also use the pipe to write out a join. It depends on your
preference.

``` r
key_house_results %>% 
  inner_join(key_house_historical)
```

    ## Joining, by = "house_dist"

    ## # A tibble: 104 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 AZ-01      lean democrat~ N             53.8         46.2 D         7.6
    ##  2 AZ-02      likely democr~ Y             54.7         45.3 D         9.4
    ##  3 AZ-06      likely republ~ N             44.8         55.2 R        10.4
    ##  4 AZ-08      likely republ~ N             44.5         55.5 R        11  
    ##  5 AZ-09      likely democr~ N             61.1         38.9 D        22.2
    ##  6 AR-02      lean republic~ N             45.8         52.1 R         6.3
    ##  7 CA-04      likely republ~ N             45.9         54.1 R         8.2
    ##  8 CA-10      tossup         Y             52.3         47.7 D         4.6
    ##  9 CA-21      likely republ~ Y             50.4         49.6 D         0.8
    ## 10 CA-25      tossup         Y             54.4         45.6 D         8.8
    ## # ... with 94 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

Now with an explicit mentioning of the column to join

``` r
key_house_results %>% 
  inner_join(key_house_historical, by = "house_dist")
```

    ## # A tibble: 104 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 AZ-01      lean democrat~ N             53.8         46.2 D         7.6
    ##  2 AZ-02      likely democr~ Y             54.7         45.3 D         9.4
    ##  3 AZ-06      likely republ~ N             44.8         55.2 R        10.4
    ##  4 AZ-08      likely republ~ N             44.5         55.5 R        11  
    ##  5 AZ-09      likely democr~ N             61.1         38.9 D        22.2
    ##  6 AR-02      lean republic~ N             45.8         52.1 R         6.3
    ##  7 CA-04      likely republ~ N             45.9         54.1 R         8.2
    ##  8 CA-10      tossup         Y             52.3         47.7 D         4.6
    ##  9 CA-21      likely republ~ Y             50.4         49.6 D         0.8
    ## 10 CA-25      tossup         Y             54.4         45.6 D         8.8
    ## # ... with 94 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

Remember, if we want to save the results, we need to create a new object

``` r
joined <- key_house_results %>% 
  inner_join(key_house_historical, by = "house_dist")
```

Let’s explore our new joined table using what we’ve learned so far

``` r
glimpse(joined)
```

    ## Observations: 104
    ## Variables: 15
    ## $ house_dist                    <chr> "AZ-01", "AZ-02", "AZ-06", "AZ-0...
    ## $ keyrace_rating                <chr> "lean democratic", "likely democ...
    ## $ flips                         <chr> "N", "Y", "N", "N", "N", "N", "N...
    ## $ dem_vote_pct                  <dbl> 53.8, 54.7, 44.8, 44.5, 61.1, 45...
    ## $ gop_vote_pct                  <dbl> 46.2, 45.3, 55.2, 55.5, 38.9, 52...
    ## $ winner                        <chr> "D", "D", "R", "R", "D", "R", "R...
    ## $ margin                        <dbl> 7.6, 9.4, 10.4, 11.0, 22.2, 6.3,...
    ## $ former_party                  <chr> "D", "R", "R", "R", "D", "R", "R...
    ## $ pct_college                   <dbl> 23.640, 33.024, 42.851, 28.346, ...
    ## $ pct_college_abovebelow_natl   <chr> "BELOW", "ABOVE", "ABOVE", "BELO...
    ## $ median_income                 <dbl> 48583, 48125, 64223, 60342, 5224...
    ## $ median_income_abovebelow_natl <chr> "BELOW", "BELOW", "ABOVE", "ABOV...
    ## $ prez_winner_2016              <chr> "R", "D", "R", "R", "D", "R", "R...
    ## $ trump_vote_pct                <dbl> 47.0, 43.9, 51.6, 57.1, 37.6, 52...
    ## $ clinton_vote_pct              <dbl> 46.0, 48.7, 41.7, 36.4, 53.5, 41...

What kinds of questions can we ask, using our dplyr functions? Lots of
choices\!

Let’s start out by getting some aggregate counts  
How many key races were there?

``` r
joined %>% 
  count(keyrace_rating)
```

    ## # A tibble: 6 x 2
    ##   keyrace_rating        n
    ##   <chr>             <int>
    ## 1 lean democratic      15
    ## 2 lean republican      22
    ## 3 likely democratic     6
    ## 4 likely republican    22
    ## 5 NA                    8
    ## 6 tossup               31

How many did each party win?

``` r
joined %>% 
  count(keyrace_rating, winner)
```

    ## # A tibble: 10 x 3
    ##    keyrace_rating    winner     n
    ##    <chr>             <chr>  <int>
    ##  1 lean democratic   D         15
    ##  2 lean republican   D          4
    ##  3 lean republican   R         18
    ##  4 likely democratic D          6
    ##  5 likely republican D          1
    ##  6 likely republican R         21
    ##  7 NA                D          4
    ##  8 NA                R          4
    ##  9 tossup            D         22
    ## 10 tossup            R          9

How many of those wins were flips?

``` r
joined %>% 
  filter(!is.na(keyrace_rating)) %>% 
  count(winner, flips)
```

    ## # A tibble: 4 x 3
    ##   winner flips     n
    ##   <chr>  <chr> <int>
    ## 1 D      N        10
    ## 2 D      Y        42
    ## 3 R      N        50
    ## 4 R      Y         2

Wait a sec, what was that with the `!is.na()`?  
You can reverse certain functions like `is.na()` - returning only NA
rows - by adding a `!` before it.  
Just like with `!=`

Now let’s examine just the flipped districts

``` r
flipped <- joined %>% 
  filter(flips == "Y") 

flipped
```

    ## # A tibble: 44 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 AZ-02      likely democr~ Y             54.7         45.3 D         9.4
    ##  2 CA-10      tossup         Y             52.3         47.7 D         4.6
    ##  3 CA-21      likely republ~ Y             50.4         49.6 D         0.8
    ##  4 CA-25      tossup         Y             54.4         45.6 D         8.8
    ##  5 CA-39      tossup         Y             51.6         48.4 D         3.2
    ##  6 CA-45      tossup         Y             52.1         47.9 D         4.2
    ##  7 CA-48      tossup         Y             53.6         46.4 D         7.2
    ##  8 CA-49      lean democrat~ Y             56.4         43.6 D        12.8
    ##  9 CO-06      lean democrat~ Y             54.1         42.9 D        11.2
    ## 10 FL-26      tossup         Y             50.9         49.1 D         1.8
    ## # ... with 34 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

*Note: this data is for training purposes only. A few actual results
affecting flips aren’t reflected here.*  
Now we can start asking some questions about the nature of the flipped
districts:

``` r
flipped %>% 
  count(winner)
```

    ## # A tibble: 2 x 2
    ##   winner     n
    ##   <chr>  <int>
    ## 1 D         42
    ## 2 R          2

Quite a lopsided result in favor of the Dems.  
How many flipped districts were above vs. below the national average pct
of college grads

``` r
flipped %>% 
  count(winner, pct_college_abovebelow_natl)
```

    ## # A tibble: 3 x 3
    ##   winner pct_college_abovebelow_natl     n
    ##   <chr>  <chr>                       <int>
    ## 1 D      ABOVE                          31
    ## 2 D      BELOW                          11
    ## 3 R      BELOW                           2

How many flipped districts were above vs. below the the national median
income figure

``` r
flipped %>% 
  count(winner, median_income_abovebelow_natl)
```

    ## # A tibble: 4 x 3
    ##   winner median_income_abovebelow_natl     n
    ##   <chr>  <chr>                         <int>
    ## 1 D      ABOVE                            35
    ## 2 D      BELOW                             7
    ## 3 R      ABOVE                             1
    ## 4 R      BELOW                             1

Interesting\!

Let’s do some calculating.  
What was the *average margin of victory* for Dems in flipped districts?

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise(mean(margin))
```

    ## # A tibble: 2 x 2
    ##   winner `mean(margin)`
    ##   <chr>           <dbl>
    ## 1 D                6.62
    ## 2 R                2.95

Maybe there are some other variables of which we might want to see
averages

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise(mean(pct_college))
```

    ## # A tibble: 2 x 2
    ##   winner `mean(pct_college)`
    ##   <chr>                <dbl>
    ## 1 D                     36.8
    ## 2 R                     25.0

Could we do both of them at the same time? We can, like this:

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise(mean(margin), mean(pct_college))
```

    ## # A tibble: 2 x 3
    ##   winner `mean(margin)` `mean(pct_college)`
    ##   <chr>           <dbl>               <dbl>
    ## 1 D                6.62                36.8
    ## 2 R                2.95                25.0

Hmm, this isn’t bad but what if we had five columns, or ten?  
Is there an easier way?

Yes, let’s talk about *scoped functions*.

### Scoped dplyr functions

The idea behind scoped functions: variations on the dplyr commands we’ve
used, but designed to apply to multiple variables.

They generally end with `'_all`, `_at`, and `_if` … e.g.
`summarise_if()`

Let’s take a look back at our election data. We could do something like
this:

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise(mean(margin), 
            mean(pct_college),
            mean(median_income))
```

    ## # A tibble: 2 x 4
    ##   winner `mean(margin)` `mean(pct_college)` `mean(median_income)`
    ##   <chr>           <dbl>               <dbl>                 <dbl>
    ## 1 D                6.62                36.8                70130.
    ## 2 R                2.95                25.0                54732

Or, we could use a scoped function. Here, we’ll use `summarise_at()` -
designed for when you know specific columns you want.

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise_at(vars(margin, pct_college, median_income), mean)
```

    ## # A tibble: 2 x 4
    ##   winner margin pct_college median_income
    ##   <chr>   <dbl>       <dbl>         <dbl>
    ## 1 D        6.62        36.8        70130.
    ## 2 R        2.95        25.0        54732

Sweet, right? That was a lot easier.  
Notice the use of `vars()` above - this is needed when specifying
multiple variables.  
The columns/variables you want go in `vars()`, followed by the function
to apply to them.

We can even apply *more than one* function at a time:

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise_at(vars(margin, pct_college, median_income), c(avg = mean, med = median))
```

    ## # A tibble: 2 x 7
    ##   winner margin_avg pct_college_avg median_income_a~ margin_med
    ##   <chr>       <dbl>           <dbl>            <dbl>      <dbl>
    ## 1 D            6.62            36.8           70130.       5.15
    ## 2 R            2.95            25.0           54732        2.95
    ## # ... with 2 more variables: pct_college_med <dbl>,
    ## #   median_income_med <dbl>

We can also group by more than one variable, like below where we look at
the entire set of races not just flips.

``` r
joined %>% 
  group_by(flips, winner) %>% 
  summarise_at(vars(margin, pct_college, median_income), mean)
```

    ## # A tibble: 4 x 5
    ## # Groups:   flips [2]
    ##   flips winner margin pct_college median_income
    ##   <chr> <chr>   <dbl>       <dbl>         <dbl>
    ## 1 N     D       NA           29.2        57498.
    ## 2 N     R       NA           29.0        55807.
    ## 3 Y     D        6.62        36.8        70130.
    ## 4 Y     R        2.95        25.0        54732

Notice something a little odd with the results? We’re getting some
NAs.  
Since `mean()` breaks when there are NA values, we need to fix that.

``` r
joined %>% 
  group_by(flips, winner) %>% 
  summarise_at(vars(margin, pct_college, median_income), mean, na.rm = TRUE)
```

    ## # A tibble: 4 x 5
    ## # Groups:   flips [2]
    ##   flips winner margin pct_college median_income
    ##   <chr> <chr>   <dbl>       <dbl>         <dbl>
    ## 1 N     D       10.8         29.2        57498.
    ## 2 N     R        6.53        29.0        55807.
    ## 3 Y     D        6.62        36.8        70130.
    ## 4 Y     R        2.95        25.0        54732

We can even create our own custom functions (won’t get into that in this
session, though).

Now what if we wanted to apply our mean to every column in the data?

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise_all(mean)
```

    ## Warning in mean.default(house_dist): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(house_dist): argument is not numeric or logical:
    ## returning NA

    ## Warning in mean.default(keyrace_rating): argument is not numeric or
    ## logical: returning NA
    
    ## Warning in mean.default(keyrace_rating): argument is not numeric or
    ## logical: returning NA

    ## Warning in mean.default(flips): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(flips): argument is not numeric or logical:
    ## returning NA

    ## Warning in mean.default(former_party): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(former_party): argument is not numeric or logical:
    ## returning NA

    ## Warning in mean.default(pct_college_abovebelow_natl): argument is not
    ## numeric or logical: returning NA
    
    ## Warning in mean.default(pct_college_abovebelow_natl): argument is not
    ## numeric or logical: returning NA

    ## Warning in mean.default(median_income_abovebelow_natl): argument is not
    ## numeric or logical: returning NA
    
    ## Warning in mean.default(median_income_abovebelow_natl): argument is not
    ## numeric or logical: returning NA

    ## Warning in mean.default(prez_winner_2016): argument is not numeric or
    ## logical: returning NA
    
    ## Warning in mean.default(prez_winner_2016): argument is not numeric or
    ## logical: returning NA

    ## # A tibble: 2 x 15
    ##   winner house_dist keyrace_rating flips dem_vote_pct gop_vote_pct margin
    ##   <chr>       <dbl>          <dbl> <dbl>        <dbl>        <dbl>  <dbl>
    ## 1 D              NA             NA    NA         52.9         46.3   6.62
    ## 2 R              NA             NA    NA         47.5         50.4   2.95
    ## # ... with 8 more variables: former_party <dbl>, pct_college <dbl>,
    ## #   pct_college_abovebelow_natl <dbl>, median_income <dbl>,
    ## #   median_income_abovebelow_natl <dbl>, prez_winner_2016 <dbl>,
    ## #   trump_vote_pct <dbl>, clinton_vote_pct <dbl>

We got a lot of warnings there. What happened?  
You can’t calculate a mean on a text column, only a numeric one.

Enter `summarise_if()`.

``` r
flipped %>% 
  group_by(winner) %>% 
  summarise_if(is.numeric, mean)
```

    ## # A tibble: 2 x 8
    ##   winner dem_vote_pct gop_vote_pct margin pct_college median_income
    ##   <chr>         <dbl>        <dbl>  <dbl>       <dbl>         <dbl>
    ## 1 D              52.9         46.3   6.62        36.8        70130.
    ## 2 R              47.5         50.4   2.95        25.0        54732 
    ## # ... with 2 more variables: trump_vote_pct <dbl>, clinton_vote_pct <dbl>

Now we’re talking.

Though if we look closely, there are some columns we may decide aren’t
we want.  
Let’s say we don’t want to averages of vote percentages for our
analysis.  
We could see if there’s a pattern to their names? There is, so we can
use a \`select()\`\` helper function.

``` r
flipped %>% 
  select(-ends_with("vote_pct")) %>% 
  group_by(winner) %>% 
  summarise_if(is.numeric, mean)
```

    ## # A tibble: 2 x 4
    ##   winner margin pct_college median_income
    ##   <chr>   <dbl>       <dbl>         <dbl>
    ## 1 D        6.62        36.8        70130.
    ## 2 R        2.95        25.0        54732

Perfect.

We’re not going to get into all the select helper functions, but they
are very useful,  
You can read more about them at
<https://www.rdocumentation.org/packages/dplyr/versions/0.7.2/topics/select_helpers>

Such scoped versions also exist for other dplyr functions.  
Let’s take a quick look at `mutate_at()`.

``` r
# mutate_at(vars(x, y, z), .funs, ...)
```

Perhaps we want to format the percentages as text because a web app
using the data is having trouble displaying numerics (this generally can
be handled by app code but let’s say it can’t for whatever reason).

``` r
joined %>% 
  select(dem_vote_pct, 
         gop_vote_pct,
         pct_college,
         trump_vote_pct,
         clinton_vote_pct) %>% 
  mutate_at(vars(dem_vote_pct, 
                 gop_vote_pct,
                 pct_college,
                 trump_vote_pct,
                 clinton_vote_pct), 
            as.character)
```

    ## # A tibble: 104 x 5
    ##    dem_vote_pct gop_vote_pct pct_college trump_vote_pct clinton_vote_pct
    ##    <chr>        <chr>        <chr>       <chr>          <chr>           
    ##  1 53.8         46.2         23.64       47             46              
    ##  2 54.7         45.3         33.024      43.9           48.7            
    ##  3 44.8         55.2         42.851      51.6           41.7            
    ##  4 44.5         55.5         28.346      57.1           36.4            
    ##  5 61.1         38.9         36.734      37.6           53.5            
    ##  6 45.8         52.1         28.403      52.4           41.7            
    ##  7 45.9         54.1         32.2        53             38.5            
    ##  8 52.3         47.7         17.182      44.9           47.9            
    ##  9 50.4         49.6         8.127       39.3           54.7            
    ## 10 54.4         45.6         27.332      43.4           50.1            
    ## # ... with 94 more rows

You can also create new columns just as you do with mutate

``` r
joined %>% 
  select(dem_vote_pct, 
         gop_vote_pct) %>% 
  mutate_at(vars(dem_vote_char = dem_vote_pct, 
                 gop_vote_char = gop_vote_pct), 
            as.character)
```

    ## # A tibble: 104 x 4
    ##    dem_vote_pct gop_vote_pct dem_vote_char gop_vote_char
    ##           <dbl>        <dbl> <chr>         <chr>        
    ##  1         53.8         46.2 53.8          46.2         
    ##  2         54.7         45.3 54.7          45.3         
    ##  3         44.8         55.2 44.8          55.2         
    ##  4         44.5         55.5 44.5          55.5         
    ##  5         61.1         38.9 61.1          38.9         
    ##  6         45.8         52.1 45.8          52.1         
    ##  7         45.9         54.1 45.9          54.1         
    ##  8         52.3         47.7 52.3          47.7         
    ##  9         50.4         49.6 50.4          49.6         
    ## 10         54.4         45.6 54.4          45.6         
    ## # ... with 94 more rows

To mutate certain columns based on criteria, we use `mutate_if()`

``` r
joined %>% 
  mutate_if(is.character, str_to_lower)
```

    ## # A tibble: 104 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 az-01      lean democrat~ n             53.8         46.2 d         7.6
    ##  2 az-02      likely democr~ y             54.7         45.3 d         9.4
    ##  3 az-06      likely republ~ n             44.8         55.2 r        10.4
    ##  4 az-08      likely republ~ n             44.5         55.5 r        11  
    ##  5 az-09      likely democr~ n             61.1         38.9 d        22.2
    ##  6 ar-02      lean republic~ n             45.8         52.1 r         6.3
    ##  7 ca-04      likely republ~ n             45.9         54.1 r         8.2
    ##  8 ca-10      tossup         y             52.3         47.7 d         4.6
    ##  9 ca-21      likely republ~ y             50.4         49.6 d         0.8
    ## 10 ca-25      tossup         y             54.4         45.6 d         8.8
    ## # ... with 94 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

``` r
joined %>% 
  mutate_if(is.character, str_to_title)
```

    ## # A tibble: 104 x 15
    ##    house_dist keyrace_rating flips dem_vote_pct gop_vote_pct winner margin
    ##    <chr>      <chr>          <chr>        <dbl>        <dbl> <chr>   <dbl>
    ##  1 Az-01      Lean Democrat~ N             53.8         46.2 D         7.6
    ##  2 Az-02      Likely Democr~ Y             54.7         45.3 D         9.4
    ##  3 Az-06      Likely Republ~ N             44.8         55.2 R        10.4
    ##  4 Az-08      Likely Republ~ N             44.5         55.5 R        11  
    ##  5 Az-09      Likely Democr~ N             61.1         38.9 D        22.2
    ##  6 Ar-02      Lean Republic~ N             45.8         52.1 R         6.3
    ##  7 Ca-04      Likely Republ~ N             45.9         54.1 R         8.2
    ##  8 Ca-10      Tossup         Y             52.3         47.7 D         4.6
    ##  9 Ca-21      Likely Republ~ Y             50.4         49.6 D         0.8
    ## 10 Ca-25      Tossup         Y             54.4         45.6 D         8.8
    ## # ... with 94 more rows, and 8 more variables: former_party <chr>,
    ## #   pct_college <dbl>, pct_college_abovebelow_natl <chr>,
    ## #   median_income <dbl>, median_income_abovebelow_natl <chr>,
    ## #   prez_winner_2016 <chr>, trump_vote_pct <dbl>, clinton_vote_pct <dbl>

Finally, hot of the presses: just released in the **new version** of
dplyr, 0.8. Scoped version of `group_by()`\!

More details at
<https://dplyr.tidyverse.org/reference/group_by_all.html>

We’re not going to go into it for this session - mainly because I
haven’t even explored it myself yet. But look forward to\!
