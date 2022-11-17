assignment04
================
sl
2022-11-17

# HPC

## Problem 1: Make sure your code is nice

Rewrite the following R functions to make them faster. It is OK (and
recommended) to take a look at Stackoverflow and Google

``` r
mat <- matrix(1:16, ncol = 4)
mat
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    5    9   13
    ## [2,]    2    6   10   14
    ## [3,]    3    7   11   15
    ## [4,]    4    8   12   16

``` r
# Total row sums
fun1 <- function(mat) {
  n <- nrow(mat)
  ans <- double(n) 
  for (i in 1:n) {
    ans[i] <- sum(mat[i, ])
  }
  ans
}

fun1(mat)
```

    ## [1] 28 32 36 40

``` r
fun1alt <- function(mat) {
  # YOUR CODE HERE
  n <- nrow(mat)
  ans <- double(n)
  ans <- rowSums(mat)
  
  return(ans)
}

fun1alt(mat)
```

    ## [1] 28 32 36 40

``` r
# Cumulative sum by row
fun2 <- function(mat) {
  n <- nrow(mat)
  k <- ncol(mat)
  ans <- mat
  for (i in 1:n) {
    for (j in 2:k) {
      ans[i,j] <- mat[i, j] + ans[i, j - 1]
    }
  }
  ans
}

fun2(mat)
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    6   15   28
    ## [2,]    2    8   18   32
    ## [3,]    3   10   21   36
    ## [4,]    4   12   24   40

``` r
library(data.table)
fun2alt <- function(mat) {
  # YOUR CODE HERE
  ans <- t(apply(mat, 1, cumsum))
  
  return(ans)
}

fun2alt(mat)
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    6   15   28
    ## [2,]    2    8   18   32
    ## [3,]    3   10   21   36
    ## [4,]    4   12   24   40

``` r
# Use the data with this code
set.seed(2315)
dat <- matrix(rnorm(200 * 100), nrow = 200)

# Test for the first
microbenchmark::microbenchmark(
  fun1(dat),
  fun1alt(dat), check = "equivalent"
)
```

    ## Unit: microseconds
    ##          expr     min       lq      mean   median      uq      max neval
    ##     fun1(dat) 459.603 555.8705 656.15497 593.2965 710.753 1516.960   100
    ##  fun1alt(dat)  50.573  52.8270  61.14281  58.1400  66.854  109.563   100

``` r
# Test for the second
microbenchmark::microbenchmark(
  fun2(dat),
  fun2alt(dat), check = "equivalent"
)
```

    ## Unit: microseconds
    ##          expr      min       lq     mean   median       uq       max neval
    ##     fun2(dat) 2826.813 2930.907 3604.901 3087.062 3859.120  8043.586   100
    ##  fun2alt(dat)  697.358 1124.958 2139.192 1237.115 2350.645 23749.656   100

I have to remove unit = “relative” to avoid an error.

## Problem 2: Make things run faster with parallel computing

The following function allows simulating PI

``` r
sim_pi <- function(n = 1000, i = NULL) {
  p <- matrix(runif(n*2), ncol = 2)
  mean(rowSums(p^2) < 1) * 4
}

# Here is an example of the run
set.seed(156)
sim_pi(1000) # 3.132
```

    ## [1] 3.132

In order to get accurate estimates, we can run this function multiple
times, with the following code:

``` r
# This runs the simulation a 4,000 times, each with 10,000 points
set.seed(1231)
system.time({
  ans <- unlist(lapply(1:4000, sim_pi, n = 10000))
  print(mean(ans))
})
```

    ## [1] 3.14124

    ##    user  system elapsed 
    ##   4.273   1.189   5.519

Rewrite the previous code using parLapply() to make it run faster. Make
sure you set the seed using clusterSetRNGStream():

``` r
# YOUR CODE HERE
library(parallel)

# Setup
cl <- makePSOCKcluster(2)  
clusterSetRNGStream(cl, 1231)

# Number of simulations we want each time to run
nsim <- 10000

system.time({
  # YOUR CODE HERE
  ans <- unlist(parLapply(cl, 1:4000, sim_pi, nsim ))
  print(mean(ans))
  # YOUR CODE HERE
})
```

    ## [1] 3.141607

    ##    user  system elapsed 
    ##   0.005   0.000   0.940

``` r
stopCluster(cl)
```

# SQL

Setup a temporary database by running the following chunk

``` r
if (!require(RSQLite)) install.packages(c("RSQLite"))
```

    ## Loading required package: RSQLite

``` r
if (!require(DBI)) install.packages(c("DBI"))
```

    ## Loading required package: DBI

``` r
library(RSQLite)
library(DBI)

# Initialize a temporary in memory database
con <- dbConnect(SQLite(), ":memory:")

# Download tables
film <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/film.csv")
film_category <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/film_category.csv")
category <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/category.csv")

# Copy data.frames to database
dbWriteTable(con, "film", film)
dbWriteTable(con, "film_category", film_category)
dbWriteTable(con, "category", category)
```

## Question 1

How many movies is there available in each rating category.

``` sql
SELECT rating, COUNT(*) AS N
FROM film
GROUP BY rating
ORDER BY N DESC
```

| rating |   N |
|:-------|----:|
| PG-13  | 223 |
| NC-17  | 210 |
| R      | 195 |
| PG     | 194 |
| G      | 180 |

5 records

## Question 2

What is the average replacement cost and rental rate for each rating
category.

``` sql
SELECT rating, 
        AVG(replacement_cost) AS avg_replacement_cost,
        AVG(rental_rate) AS avg_rental_rate
FROM film
GROUP BY rating
ORDER BY avg_replacement_cost DESC
```

| rating | avg_replacement_cost | avg_rental_rate |
|:-------|---------------------:|----------------:|
| PG-13  |             20.40256 |        3.034843 |
| R      |             20.23103 |        2.938718 |
| NC-17  |             20.13762 |        2.970952 |
| G      |             20.12333 |        2.912222 |
| PG     |             18.95907 |        3.051856 |

5 records

## Question 3

Use table film_category together with film to find the how many films
there are within each category ID

``` sql
SELECT category_id, COUNT(*) AS N
FROM film_category AS fc INNER JOIN film AS f
ON fc.film_id = f.film_id
GROUP BY category_id
ORDER BY category_id ASC
```

| category_id |   N |
|:------------|----:|
| 1           |  64 |
| 2           |  66 |
| 3           |  60 |
| 4           |  57 |
| 5           |  58 |
| 6           |  68 |
| 7           |  62 |
| 8           |  69 |
| 9           |  73 |
| 10          |  61 |

Displaying records 1 - 10

## Question 4

Incorporate table category into the answer to the previous question to
find the name of the most popular category.

``` sql
SELECT c.name, COUNT(*) AS N
FROM ((film_category AS fc
INNER JOIN film AS f ON fc.film_id = f.film_id)
INNER JOIN category AS c ON fc.category_id = c.category_id)
GROUP BY c.name
ORDER BY N DESC
```

| name        |   N |
|:------------|----:|
| Sports      |  74 |
| Foreign     |  73 |
| Family      |  69 |
| Documentary |  68 |
| Animation   |  66 |
| Action      |  64 |
| New         |  63 |
| Drama       |  62 |
| Sci-Fi      |  61 |
| Games       |  61 |

Displaying records 1 - 10

## Cleanup

Run the following chunk to disconnect from the connection.

``` r
# clean up
dbDisconnect(con)
```
