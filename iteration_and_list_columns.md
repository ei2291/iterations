iteration and list columns
================

``` r
set.seed(1)
```

## Vectors

Vector can only hold one type of thing. all numbers, all texts, or
true/false values.

``` r
ver_numeric = 5:8
ver_char = c("My", "name", "is", "eman")
vec_logical = c(TRUE, TRUE, TRUE, FALSE)
```

## Lists

Lists can hold anything. fixing the above problem.

``` r
l = list(
  vec_numeric = 5:8,
  mat = matrix (1:8, 2, 4),
  vec_logical = c(TRUE, FALSE),
  summary = summary(rnorm(30))
)
```

You can pull things out of a list using: access by name, position, or by
what’s inside, seen below respectovely.

``` r
l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
l[[1]]
```

    ## [1] 5 6 7 8

``` r
l[[1]][1:3]
```

    ## [1] 5 6 7

## for Loops - repeating a task

we now make a list of random numbers (4 sets, called a, b, c, d). each
element (a,b,c, d) is a vector of 20 random numbers with different means
and sd.

``` r
list_norms = list(
  a = rnorm(20, 3, 1),
  b = rnorm(20, 0, 5),
  c = rnorm(20, 10, .2),
  d = rnorm(20, -3, 1)
)
```

## Function to summarize one vector

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  
  tibble(
    mean = mean_x,
    sd = sd_x
  )
}
```

The above does this: - checks if input is numeric; if not, it stops and
shows an error. - checks that the input has more than one number. -
calculates the mean and sd. - returns a small 1-row table (`tibble`)
with those values.

# Apply the function manually to each element

``` r
mean_and_sd(list_norms[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.13 0.691

``` r
mean_and_sd(list_norms[[2]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.49  5.70

``` r
mean_and_sd(list_norms[[3]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.99 0.145

``` r
mean_and_sd(list_norms[[4]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.77 0.978

## Use a for loop to automate it.

We’ll create an empty list first, then fill it up inside a loop.

``` r
output = vector ("list",length = 4)

for (i in 1:4){
  output[[i]] = mean_and_sd(list_norms[[i]])
}
output
```

    ## [[1]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.13 0.691
    ## 
    ## [[2]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.49  5.70
    ## 
    ## [[3]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.99 0.145
    ## 
    ## [[4]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.77 0.978

the `output` is a list with 4 small tables, one for each example

# The same thing using `map()` (cleaner!)

``` r
output = map(list_norms, mean_and_sd)
output
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.13 0.691
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.49  5.70
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.99 0.145
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.77 0.978

^ this single line apply the function `mean_and_sd` to each element of
the list `list_norms`.

try using median function:

``` r
output = map(list_norms, median)
output
```

    ## $a
    ## [1] 2.943441
    ## 
    ## $b
    ## [1] 0.8551141
    ## 
    ## $c
    ## [1] 10.00754
    ## 
    ## $d
    ## [1] -2.728893

## Use a map variant to get specific output types

``` r
output = map_dbl(list_norms, median, .id="input")
output
```

    ##          a          b          c          d 
    ##  2.9434408  0.8551141 10.0075447 -2.7288929

``` r
output = map_dfr(list_norms, median, .id="input")
output
```

    ## # A tibble: 1 × 4
    ##       a     b     c     d
    ##   <dbl> <dbl> <dbl> <dbl>
    ## 1  2.94 0.855  10.0 -2.73

`map_dbl()` apply the function to each list element and return a numeric
vector. `map_dfr()` combines all results into 1 df (rows).

## List column

# Create a df that contains a list column

``` r
listcol_df = tibble(
  name = c("a", "b", "c", "d"),
  samp = list_norms
)
```

^ `name` is a text column, and `samp` is a list column.

check:

``` r
listcol_df |> pull(name)
```

    ## [1] "a" "b" "c" "d"

``` r
listcol_df |> pull(samp) 
```

    ## $a
    ##  [1] 4.358680 2.897212 3.387672 2.946195 1.622940 2.585005 2.605710 2.940687
    ##  [9] 4.100025 3.763176 2.835476 2.746638 3.696963 3.556663 2.311244 2.292505
    ## [17] 3.364582 3.768533 2.887654 3.881108
    ## 
    ## $b
    ##  [1]  1.9905294 -3.0601320  1.7055985 -5.6468155  7.1651185  9.9019995
    ##  [7] -1.8361074 -5.2206731  2.8485981 -0.6752730 12.0080888 -0.1962000
    ## [13]  3.4486968  0.1400108 -3.7163660  0.9439615 -9.0247931  7.3277743
    ## [19]  0.7662667 10.8630584
    ## 
    ## $c
    ##  [1] 10.095102  9.858011 10.122145  9.813180  9.749273 10.058289  9.911342
    ##  [8] 10.000221 10.014868  9.882096  9.886266  9.972964 10.235617  9.695287
    ## [15] 10.118789 10.066590 10.212620  9.939163 10.074004 10.053420
    ## 
    ## $d
    ##  [1] -3.542520 -1.792132 -1.839597 -2.299786 -1.413167 -2.441514 -4.276592
    ##  [8] -3.573265 -4.224613 -3.473401 -3.620367 -2.957884 -3.910922 -2.841971
    ## [15] -3.654585 -1.232713 -2.283293 -2.089826 -2.615815 -1.317824

``` r
pull(listcol_df, samp)[[1]]
```

    ##  [1] 4.358680 2.897212 3.387672 2.946195 1.622940 2.585005 2.605710 2.940687
    ##  [9] 4.100025 3.763176 2.835476 2.746638 3.696963 3.556663 2.311244 2.292505
    ## [17] 3.364582 3.768533 2.887654 3.881108

# Apply `mean_and_sd` to the first list manually

``` r
mean_and_sd(pull(listcol_df, samp)[[1]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.13 0.691

# Apply it to ALL list entries using `map()`

``` r
map(pull(listcol_df, samp), mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.13 0.691
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.49  5.70
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  9.99 0.145
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.77 0.978

``` r
listcol_df=
  listcol_df |> 
  mutate(summary = map(samp, mean_and_sd))

listcol_df
```

    ## # A tibble: 4 × 3
    ##   name  samp         summary         
    ##   <chr> <named list> <named list>    
    ## 1 a     <dbl [20]>   <tibble [1 × 2]>
    ## 2 b     <dbl [20]>   <tibble [1 × 2]>
    ## 3 c     <dbl [20]>   <tibble [1 × 2]>
    ## 4 d     <dbl [20]>   <tibble [1 × 2]>

## Nested data - grouping larger datasets

``` r
library(p8105.datasets)
data("weather_df")
```

nest it:

``` r
weather_nest = nest(weather_df, data = date:tmin)
weather_nest
```

    ## # A tibble: 3 × 3
    ##   name           id          data              
    ##   <chr>          <chr>       <list>            
    ## 1 CentralPark_NY USW00094728 <tibble [730 × 4]>
    ## 2 Molokai_HI     USW00022534 <tibble [730 × 4]>
    ## 3 Waterhole_WA   USS0023B17S <tibble [730 × 4]>

unnest (return to full form):

``` r
unnest(weather_nest, cols = data)
```

    ## # A tibble: 2,190 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1  
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6
    ## # ℹ 2,180 more rows

# fix models (tmax~tmin) for each station

step 1: define a model-fitting function

``` r
weather_lm = function(df){
  lm(tmax~tmin, data=df)
}
```

step 2: test it on one station’s data

``` r
weather_lm(pull(weather_nest, data)[[1]])
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.514        1.034

this fits a linear regression prediciting max temperature from min temp.

step 3: apply it to all stations using `map()`

``` r
map(pull(weather_nest, data), weather_lm)
```

    ## [[1]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.514        1.034  
    ## 
    ## 
    ## [[2]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##     21.7547       0.3222  
    ## 
    ## 
    ## [[3]]
    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.532        1.137

step 4: store those models in the df

``` r
weather_nest=
  weather_nest |> 
  mutate(models=map(data, weather_lm))

weather_nest
```

    ## # A tibble: 3 × 4
    ##   name           id          data               models
    ##   <chr>          <chr>       <list>             <list>
    ## 1 CentralPark_NY USW00094728 <tibble [730 × 4]> <lm>  
    ## 2 Molokai_HI     USW00022534 <tibble [730 × 4]> <lm>  
    ## 3 Waterhole_WA   USS0023B17S <tibble [730 × 4]> <lm>
