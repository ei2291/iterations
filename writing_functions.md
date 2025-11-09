writing_functions
================

## My first function

``` r
x_vec = rnorm(25, mean = 5, sd = 3)

(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1] -0.1477611  0.3246929  0.9248758 -0.2738923  0.1917571  0.1852179
    ##  [7]  1.6695736 -0.4538247 -1.7881544 -0.6137814 -1.7465250  1.6946147
    ## [13] -0.9934932  0.5114299  0.2878873  0.3639681  0.9331965 -0.6079398
    ## [19] -0.1593768 -1.0188287  0.3768894 -1.2314289  2.1713353 -0.1265673
    ## [25] -0.4738647

``` r
z_scores = function(x) {
  
  if(!is.numeric(x)){
    stop ("Input must be numeric")
  }
  
  if(length(x) <3){
    stop("Input must have at least three numbers")
  }
  
  z = (x-mean(x)) / sd(x)
  z
}
z_scores(x_vec)
```

    ##  [1] -0.1477611  0.3246929  0.9248758 -0.2738923  0.1917571  0.1852179
    ##  [7]  1.6695736 -0.4538247 -1.7881544 -0.6137814 -1.7465250  1.6946147
    ## [13] -0.9934932  0.5114299  0.2878873  0.3639681  0.9331965 -0.6079398
    ## [19] -0.1593768 -1.0188287  0.3768894 -1.2314289  2.1713353 -0.1265673
    ## [25] -0.4738647

Try my function on some other things.These should give errors.

``` r
z_scores(3)
```

    ## Error in z_scores(3): Input must have at least three numbers

``` r
z_scores("my name is eman")
```

    ## Error in z_scores("my name is eman"): Input must be numeric

``` r
z_scores(mt_cars)
```

    ## Error: object 'mt_cars' not found

``` r
z_scores(c(TRUE, TRUE, FALSE, TRUE))
```

    ## Error in z_scores(c(TRUE, TRUE, FALSE, TRUE)): Input must be numeric
