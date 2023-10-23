Strings_and_factors
================
Shina Min
2023-10-22

## Strings and regex

``` r
string_vec = c("my", "name", "is", "shina")

str_detect(string_vec, "shina")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_replace(string_vec, "shina", "Shina")
```

    ## [1] "my"    "name"  "is"    "Shina"

``` r
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun",
  "it will be fun, i think"
)

str_detect(string_vec, "^i think") # ^ is the beginning of the line that I am interested 
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
str_detect(string_vec, "i think$") # $ is the end of the line
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
string_vec = c(
  "Y'all remember Pres. HW Bush?",
  "I saw a green bush",
  "BBQ and Bushwalking at Molonglo Gorge",
  "BUSH -- LIVE IN CONCERT!!"
)

str_detect(string_vec, "[Bb]ush")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )

str_detect(string_vec, "^[0-9][a-zA-Z]") # number first and a letter follows right after
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
  )

str_detect(string_vec, "7.11") # . is a special character in regular expressions that matches literally anything that might exist in a string
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
str_detect(string_vec, "7\\.11") # if I want to detect the dot specifically, I need to put two \\ in front of .
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
string_vec = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on pages [6-7]'
  )

str_detect(string_vec, "\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

## Factors

``` r
factor_vec = factor(c("male", "male", "female", "female"))

factor_vec
```

    ## [1] male   male   female female
    ## Levels: female male

``` r
as.numeric(factor_vec) # converting my level to numeric values 
```

    ## [1] 2 2 1 1

what happens if i relevel…

``` r
factor_vec = fct_relevel(factor_vec, "male")

factor_vec
```

    ## [1] male   male   female female
    ## Levels: male female

``` r
as.numeric(factor_vec)
```

    ## [1] 1 1 2 2

## NSDUH

``` r
url = "https://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

table_marj =
  read_html(url) %>%
  html_table() %>%  
  first() %>%
  slice(-1) %>% # access specific rows in a data set (removing the first row)
  as_tibble() # converting to a tibble
```

``` r
# I don't want the p value to float around
# age range (variety of ranges floating around)
# year

data_marj =
  table_marj %>%
  select(-contains("P Value")) %>% # -contains is to remove strings
  pivot_longer(
    -State,
    names_to = "age_year",
    values_to = "percent"
  ) %>%
  separate(age_year, into = c("age", "year"), sep = "\\(") %>%
  mutate(
    year = str_replace(year, "\\)", ""),
    percent = str_replace(percent, "[a-c]$",""),
    percent = as.numeric(percent)
  ) %>%
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))
```

## NSDUH – factors

``` r
data_marj %>%
  filter(age == "12-17") %>%
  mutate(State = fct_reorder(State, percent)) %>%
  ggplot(aes(x = State, y = percent, color = year)) +
  geom_point() +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

<img src="stinrgs_and_factors_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />
