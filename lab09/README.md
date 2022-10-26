lab09
================
sl
2022-10-26

## Problem 2.

Create a n x k dataset with all its entries distributed poission with
mean lambda

``` r
set.seed(1235)
fun1 <- function(n = 100, k = 4, lambda = 4) {
  x <- NULL
  
  for (i in 1:n)
    x <- rbind(x, rpois(k, lambda))
  
  return(x)
}
f1 <- fun1(1000,4)
mean(f1)
```

    ## [1] 4.03725

``` r
#f1 <- fun1(10000,4)
#f1 <- fun1(50000,4)

fun1alt <- function(n = 100, k = 4, lambda = 4) {
  # YOUR CODE HERE
  
  x <- matrix( rpois(n*k, lambda), ncol = 4)
  
  return(x)
}
f1 <- fun1alt(50000,4)


# Benchmarking
microbenchmark::microbenchmark(
  fun1(),
  fun1alt()
)
```

    ## Unit: microseconds
    ##       expr     min      lq      mean    median        uq      max neval
    ##     fun1() 713.315 832.535 1215.4279 1101.1005 1210.5330 5992.219   100
    ##  fun1alt()  34.609  39.068  109.9477   42.7305   52.0415 4220.418   100

``` r
d <- matrix(1:16, ncol = 4)
d
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    5    9   13
    ## [2,]    2    6   10   14
    ## [3,]    3    7   11   15
    ## [4,]    4    8   12   16
