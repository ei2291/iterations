writing_functions
================

## My first function

``` r
x_vec = rnorm(25, mean = 5, sd = 3)

(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1]  0.9545482  0.6878265 -0.3754197  0.8585115 -0.3707482 -0.8216972
    ##  [7] -1.1049113  0.2469888 -0.6811442 -0.5467845  1.2728682 -1.0855859
    ## [13] -0.6272320  1.8389692 -1.9276192  0.6026506 -1.3433453  0.4724087
    ## [19]  0.6462734 -0.2412594 -0.1642855 -0.3742145  1.0949958 -0.8968654
    ## [25]  1.8850713

``` r
z_scores = function(x) {
  z = (x-mean(x))/sd(x)
  z
}
z_scores(x_vec)
```

    ##  [1]  0.9545482  0.6878265 -0.3754197  0.8585115 -0.3707482 -0.8216972
    ##  [7] -1.1049113  0.2469888 -0.6811442 -0.5467845  1.2728682 -1.0855859
    ## [13] -0.6272320  1.8389692 -1.9276192  0.6026506 -1.3433453  0.4724087
    ## [19]  0.6462734 -0.2412594 -0.1642855 -0.3742145  1.0949958 -0.8968654
    ## [25]  1.8850713

Try my function on some other things.These should give errors.

``` r
z_scores(3)
```

    ## [1] NA

``` r
z_scores("my name is eman")
```

    ## Warning in mean.default(x): argument is not numeric or logical: returning NA

    ## Error in x - mean(x): non-numeric argument to binary operator

``` r
z_scores(mt_cars)
```

    ## Error: object 'mt_cars' not found

``` r
z_scores(c(TRUE, TRUE, FALSE, TRUE))
```

    ## [1]  0.5  0.5 -1.5  0.5

Making your function safe

``` r
z_scores = function(x) {
  
  if(!is.numeric(x)){
    stop ("Input must be numeric")
  }
  
  if(length(x) ==1){
    stop("Z scores cannot be computed for length 1 vectors")
  }
  
  z = (x-mean(x)) / sd(x)
  z
}
```

## Multiple Outputs

Return a list

``` r
mean_and_sd = function(x){
  if(!is.numeric(x)) stop ("Argument x should be numeric")
  if(length(x) ==1) stop ("Cannot be computed for length 1 vectors")
  
  list(mean = mean(x), sd = sd(x))
}
mean_and_sd(x_vec)
```

    ## $mean
    ## [1] 4.869879
    ## 
    ## $sd
    ## [1] 3.321977

Return a tibble (a 1-row table)

``` r
mean_and_sd = function(x){
  if(!is.numeric(x)) stop ("Argument x should be numeric")
  if(length(x) ==1) stop ("Cannot be computed for length 1 vectors")
  
  tibble(mean=mean(x), sd = sd(x))
}
mean_and_sd(x_vec)
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.87  3.32

## Multiple inputs + simulation

Goal: Make a function that creates data and summarizes it.

Try it once (no function)

``` r
sim_data = tibble(x=rnorm(30, mean = 2, sd = 3))

sim_data |> 
  summarize(mu_hat = mean(x), sigma_hat = sd(x))
```

    ## # A tibble: 1 × 2
    ##   mu_hat sigma_hat
    ##    <dbl>     <dbl>
    ## 1   2.21      2.59

Wrap into a function argument

``` r
sim_mean_sd = function(n, mu=2, sigma = 3){
  sim_data = tibble(x=rnorm(n, mean = mu, sd = sigma))

sim_data |> 
  summarize(mu_hat = mean(x), sigma_hat = sd(x))
}
sim_mean_sd(30)
```

    ## # A tibble: 1 × 2
    ##   mu_hat sigma_hat
    ##    <dbl>     <dbl>
    ## 1   2.34      2.86

LoTR Excel example. The “old” way

``` r
fellowship_ring = readxl::read_excel("LotR_Words.xlsx", range="B3:D6") |> mutate(movie="fellowship_ring")
two_towers      = readxl::read_excel("LotR_Words.xlsx", range="F3:H6") |> mutate(movie="two_towers")
return_king     = readxl::read_excel("LotR_Words.xlsx", range="J3:L6") |> mutate(movie="return_king")

lotr_tidy = bind_rows(fellowship_ring, two_towers, return_king) |>
  janitor::clean_names() |>
  pivot_longer(female:male, names_to="sex", 
values_to="words") |>
  mutate(race = str_to_lower(race)) |>
  select(movie, everything())
```

## Learning Assessment

The “better” way.

``` r
lotr_load_and_tidy = function(path, range, movie_name) {
  readxl::read_excel(path, range = range) |>
    janitor::clean_names() |>
    pivot_longer(female:male, names_to = "sex", values_to = "words") |>
    mutate(race = str_to_lower(race), movie = movie_name) |>
    select(movie, everything())
}

lotr_tidy =
  bind_rows(
    lotr_load_and_tidy("LotR_Words.xlsx", "B3:D6", "fellowship_ring"),
    lotr_load_and_tidy("LotR_Words.xlsx", "F3:H6", "two_towers"),
    lotr_load_and_tidy("LotR_Words.xlsx", "J3:L6", "return_king")
  )
```

## Web scraping example (NSSDUH data)

1.  Get the webpage

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)
```

2.  Clean one table (no function yet)

``` r
data_marj =
  nsduh_html |>          
  html_table() |>        
  nth(1) |>              
  slice(-1) |>           
  select(-contains("P Value")) |>  
  pivot_longer(-State, names_to="age_year", values_to="percent") |>
  separate(age_year, into=c("age","year"), sep="\\(") |>
  mutate(
    year    = str_replace(year, "\\)", ""),
    percent = str_replace(percent, "[a-c]$", ""), 
    percent = as.numeric(percent)
  ) |>
  filter(!(State %in% c("Total U.S.","Northeast","Midwest","South","West")))
```

3.  Wrap it into a function so you can reuse it

``` r
nsduh_table = function(html, table_num, table_name) {
  html |>
    html_table() |>
    nth(table_num) |>
    slice(-1) |>
    select(-contains("P Value")) |>
    pivot_longer(-State, names_to="age_year", values_to="percent") |>
    separate(age_year, into=c("age","year"), sep="\\(") |>
    mutate(
      year    = str_replace(year, "\\)", ""),
      percent = str_replace(percent, "[a-c]$", ""),
      percent = as.numeric(percent),
      name    = table_name
    ) |>
    filter(!(State %in% c("Total U.S.","Northeast","Midwest","South","West")))
}
```

4.  Use it for multiple tables easily

``` r
nsduh_results =
  bind_rows(
    nsduh_table(nsduh_html, 1, "marj_one_year"),
    nsduh_table(nsduh_html, 4, "cocaine_one_year"),
    nsduh_table(nsduh_html, 5, "heroin_one_year")
  )
```

## Passing a function into a function

``` r
my_summary = function(x, summ_func){
  summ_func(x)
}
x_vec=rnorm(25, 0, 1)
my_summary(x_vec, mean)
```

    ## [1] -0.4085421

``` r
my_summary(x_vec, sd)
```

    ## [1] 0.9851939

``` r
my_summary(x_vec, IQR)
```

    ## [1] 1.146718

``` r
my_summary(x_vec, var)
```

    ## [1] 0.9706071

## Scoping (how R finds variable names)

``` r
f = function(x){
  z = x+y
  z
}
x=1
y=2
f(x=y)
```

    ## [1] 4

Let’s define x

``` r
f = function(x){
  z = x+y
  z
}
x=1
y=2
f(x)
```

    ## [1] 3
