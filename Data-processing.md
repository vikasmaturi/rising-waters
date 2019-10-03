This file takes in Excel data copied from the Army Corps of Engineers
website and turns it into cleaned .Rmd files. The data looks at openings
of the Bonnet Carre spillway and associated water flows.

\#Libraries and parameters

``` r
# loading relevant packages
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.1       ✔ purrr   0.3.2  
    ## ✔ tibble  2.1.1       ✔ dplyr   0.8.0.1
    ## ✔ tidyr   0.8.3       ✔ stringr 1.4.0  
    ## ✔ readr   1.3.1       ✔ forcats 0.4.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(readxl)
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

``` r
#loading file names from where they are saved in my local machine. If you want this code to run on your local machine, you must either: 1. create the exact following file structure under your Home, and clone the git repository to that directory, or 2. change each of these file names when running the program on your local machine.
file_2019_opening1 <- "~/Autumn-2019/COMM177b/rising-waters/2019-Opening1.xlsx"
file_2019_opening2 <- "~/Autumn-2019/COMM177b/rising-waters/2019-Opening2.xlsx"
file_2018_opening1 <- "~/Autumn-2019/COMM177b/rising-waters/2018-Opening1.xlsx"
file_2016_opening1 <- "~/Autumn-2019/COMM177b/rising-waters/2016-Opening1.xlsx"

# paths for saving the new .Rds (R data files) for easier manipulation
path_opening1_2019 <- "~/Autumn-2019/COMM177b/rising-waters/2019-Opening1.Rds"
path_opening2_2019 <- "~/Autumn-2019/COMM177b/rising-waters/2019-Opening2.Rds"
path_opening1_2018 <- "~/Autumn-2019/COMM177b/rising-waters/2018-Opening1.Rds"
path_opening1_2016 <- "~/Autumn-2019/COMM177b/rising-waters/2016-Opening1.Rds"
```

\#Load data

``` r
#this loads the raw data from their file names
raw_2019_opening1 <- read_excel(file_2019_opening1)

raw_2019_opening2 <- read_excel(file_2019_opening2)

raw_2018_opening1 <- read_excel(file_2018_opening1)

raw_2016_opening1 <- read_excel(file_2016_opening1)
```

\#Modify data tables for easier future editing

``` r
#this adjusts the 2019-opening-1 data to correct for type, unit, and grammatical errors
fixed_2019_opening1 <- 
  raw_2019_opening1 %>%
  
  # rename rows for consistency across data sets
  rename("days_open" = Day, "date" = Date, "bays_opened" = `Bays Opened`, "total_opened" = `Total Opened`, "discharge" = Discharge) %>% 
  
  #extract the first "word" from discharge column to remove units
  mutate(discharge = word(discharge, 1), year = 2019) %>% 
  
  #separate the date and recombine with the year in order to cast the date into a more usable "date" data type
  separate(date, into = c("month", "day_of_month"), sep = ". ") %>% 
  mutate(month = if_else(month == "Arp", "Apr", month)) %>% #adjust wrongly inputted data fields
  unite(col = "date", month, day_of_month, year, sep = " ") %>% 
  mutate(date = mdy(date)) %>% 
  
  #turn discharge column into a numeric value (rather than string) for easier plotting
  mutate(discharge = as.numeric(sub(",", "", discharge)))
```

``` r
##Data from 2019-opening-2 was already in the correct form
fixed_2019_opening2 <- 
  raw_2019_opening2 %>% 
  rename("days_open" = Day, "date" = Date, "bays_opened" = `Bays Opened`, "total_opened" = `Total Opened`, "discharge" = Discharge)  
```

``` r
##this adjusts the 2018-opening-1 data to correct for type, unit, and grammatical errors
fixed_2018_opening1 <- 
  raw_2018_opening1 %>%
  
  # rename rows for consistency across data sets
  rename("days_open" = Day, "date" = Date, "bays_opened" = `Bays Opened`, "total_opened" = `Total Opened`, "discharge" = Discharge) %>% 
  
  #extract the first "word" from discharge column to remove units
  mutate(discharge = word(discharge, 1), year = 2018) %>% 
  
  #separate the date and recombine with the year in order to cast the date into a more usable "date" data type
  separate(date, into = c("month", "day_of_month"), sep = ". ") %>% 
  mutate(month = if_else(month == "Arp", "Apr", month)) %>% #adjust wrongly inputted data fields
  unite(col = "date", month, day_of_month, year, sep = " ") %>% 
  mutate(date = mdy(date)) %>% 
  
  #turn discharge column into a numeric value (rather than string) for easier plotting
  mutate(discharge = as.numeric(sub(",", "", discharge)))
```

``` r
##this adjusts the 2016-opening-1 data to correct for type, unit, and grammatical errors
fixed_2016_opening1 <- 
  raw_2016_opening1 %>%
  
  # rename rows for consistency across data sets
  rename("days_open" = Day, "date" = Date, "bays_opened" = `Bays Opened`, "total_opened" = `Total Opened`, "discharge" = CFS) %>%
  
  #extract the first "word" from discharge column to remove units
  mutate(discharge = word(discharge, 1), year = 2016) %>% 
  
  #separate the date and recombine with the year in order to cast the date into a more usable "date" data type
  separate(date, into = c("month", "day_of_month"), sep = ". ") %>% 
  mutate(month = if_else(month == "Arp", "Apr", month)) %>% 
  unite(col = "date", month, day_of_month, year, sep = " ") %>% 
  mutate(date = mdy(date)) %>% 
  
  #turn discharge column into a numeric value (rather than string) for easier plotting
  mutate(discharge = as.numeric(sub(",", "", discharge)))
```

\#Put data in .Rds format for future manipulations

``` r
#Write each fixed data frame to an R data file (paths are defined in parameters above)
write_rds(fixed_2019_opening1, path = path_opening1_2019)
write_rds(fixed_2019_opening2, path = path_opening2_2019)
write_rds(fixed_2018_opening1, path = path_opening1_2018)
write_rds(fixed_2016_opening1, path = path_opening1_2016)
```
