lab10
================
sl
2022-11-02

## Data setup

``` r
if (!require(RSQLite)) install.packages(c("RSQLite"))
```

    ## Loading required package: RSQLite

``` r
if (!require(DBI)) install.packages(c("DBI"))
```

    ## Loading required package: DBI

``` r
# install.packages(c("RSQLite", "DBI"))

library(RSQLite)
library(DBI)
```

``` r
# Initialize a temporary in memory database
con <- dbConnect(SQLite(), ":memory:")

# Download tables
actor <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/actor.csv")
rental <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/rental.csv")
customer <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/customer.csv")
payment <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/payment_p2007_01.csv")

# Copy data.frames to database
dbWriteTable(con, "actor", actor)
dbWriteTable(con, "rental", rental)
dbWriteTable(con, "customer", customer)
dbWriteTable(con, "payment", payment)
dbListTables(con)
```

    ## [1] "actor"    "customer" "payment"  "rental"

``` sql
PRAGMA table_info(actor)
```

``` r
x1
```

    ##   cid        name    type notnull dflt_value pk
    ## 1   0    actor_id INTEGER       0         NA  0
    ## 2   1  first_name    TEXT       0         NA  0
    ## 3   2   last_name    TEXT       0         NA  0
    ## 4   3 last_update    TEXT       0         NA  0

``` sql
PRAGMA table_info(actor)
```

| cid | name        | type    | notnull | dflt_value |  pk |
|:----|:------------|:--------|--------:|:-----------|----:|
| 0   | actor_id    | INTEGER |       0 | NA         |   0 |
| 1   | first_name  | TEXT    |       0 | NA         |   0 |
| 2   | last_name   | TEXT    |       0 | NA         |   0 |
| 3   | last_update | TEXT    |       0 | NA         |   0 |

4 records

## Exercise 1

Retrieve the actor ID, first name and last name for all actors using the
actor table. Sort by last name and then by first name.

``` r
dbGetQuery(con,
  "
  SELECT actor_id, first_name, last_name
  FROM actor
  ORDER by first_name, first_name
  LIMIT 10
  ")
```

    ##    actor_id first_name   last_name
    ## 1        71       ADAM       GRANT
    ## 2       132       ADAM      HOPPER
    ## 3       165         AL     GARLAND
    ## 4       173       ALAN    DREYFUSS
    ## 5       125     ALBERT       NOLTE
    ## 6       146     ALBERT   JOHANSSON
    ## 7        29       ALEC       WAYNE
    ## 8        65     ANGELA      HUDSON
    ## 9       144     ANGELA WITHERSPOON
    ## 10       76   ANGELINA     ASTAIRE

## Use SQL directly

``` sql
SELECT actor_id, first_name, last_name
FROM actor
ORDER by first_name, first_name
LIMIT 10
```

| actor_id | first_name | last_name   |
|---------:|:-----------|:------------|
|       71 | ADAM       | GRANT       |
|      132 | ADAM       | HOPPER      |
|      165 | AL         | GARLAND     |
|      173 | ALAN       | DREYFUSS    |
|      125 | ALBERT     | NOLTE       |
|      146 | ALBERT     | JOHANSSON   |
|       29 | ALEC       | WAYNE       |
|       65 | ANGELA     | HUDSON      |
|      144 | ANGELA     | WITHERSPOON |
|       76 | ANGELINA   | ASTAIRE     |

Displaying records 1 - 10

## Exercise 2

Retrieve the actor ID, first name, and last name for actors whose last
name equals ‘WILLIAMS’ or ‘DAVIS’.

``` r
dbGetQuery(con,
  "
  SELECT actor_id, first_name, last_name
  FROM actor
  WHERE last_name IN ('WILLIAMS', 'DAVIS')
  ")
```

    ##   actor_id first_name last_name
    ## 1        4   JENNIFER     DAVIS
    ## 2       72       SEAN  WILLIAMS
    ## 3      101      SUSAN     DAVIS
    ## 4      110      SUSAN     DAVIS
    ## 5      137     MORGAN  WILLIAMS
    ## 6      172    GROUCHO  WILLIAMS

``` sql
SELECT actor_id, first_name, last_name
FROM actor
WHERE last_name IN ('WILLIAMS', 'DAVIS')
```

| actor_id | first_name | last_name |
|---------:|:-----------|:----------|
|        4 | JENNIFER   | DAVIS     |
|       72 | SEAN       | WILLIAMS  |
|      101 | SUSAN      | DAVIS     |
|      110 | SUSAN      | DAVIS     |
|      137 | MORGAN     | WILLIAMS  |
|      172 | GROUCHO    | WILLIAMS  |

6 records

## Exercise 3

Write a query against the rental table that returns the IDs of the
customers who rented a film on July 5, 2005 (use the rental.rental_date
column, and you can use the date() function to ignore the time
component). Include a single row for each distinct customer ID.

``` r
dbGetQuery(con,
  "
  SELECT DISTINCT customer_id, rental_date
  FROM rental
  WHERE date(rental_date) = '2005-07-05'
  LIMIT 10
  ")
```

    ##    customer_id         rental_date
    ## 1          565 2005-07-05 22:49:24
    ## 2          242 2005-07-05 22:51:44
    ## 3           37 2005-07-05 22:56:33
    ## 4           60 2005-07-05 22:57:34
    ## 5          594 2005-07-05 22:59:53
    ## 6            8 2005-07-05 23:01:21
    ## 7          490 2005-07-05 23:02:37
    ## 8          476 2005-07-05 23:05:17
    ## 9          322 2005-07-05 23:05:44
    ## 10         298 2005-07-05 23:08:53

``` r
#dbGetQuery(con,
#  "
#  SELECT customer_id, COUNT(*) AS N
#  FROM rental
#  WHERE date(rental_date) = '2005-07-05'
#  GROUP BY customer_id
#  ")
```

``` sql
SELECT DISTINCT customer_id, rental_date
FROM rental
WHERE date(rental_date) = '2005-07-05'
LIMIT 10
```

| customer_id | rental_date         |
|------------:|:--------------------|
|         565 | 2005-07-05 22:49:24 |
|         242 | 2005-07-05 22:51:44 |
|          37 | 2005-07-05 22:56:33 |
|          60 | 2005-07-05 22:57:34 |
|         594 | 2005-07-05 22:59:53 |
|           8 | 2005-07-05 23:01:21 |
|         490 | 2005-07-05 23:02:37 |
|         476 | 2005-07-05 23:05:17 |
|         322 | 2005-07-05 23:05:44 |
|         298 | 2005-07-05 23:08:53 |

Displaying records 1 - 10

## Exercise 4

### Exercise 4.1

Construct a query that retrieve all rows from the payment table where
the amount is either 1.99, 7.99, 9.99.

``` r
dbGetQuery(con,
  "
  SELECT  *
  FROM payment
  WHERE amount IN (1.99, 7.99, 9.99)
  LIMIT 10
  ")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16050         269        2         7   1.99 2007-01-24 21:40:19.996577
    ## 2       16056         270        1       193   1.99 2007-01-26 05:10:14.996577
    ## 3       16081         282        2        48   1.99 2007-01-25 04:49:12.996577
    ## 4       16103         294        1       595   1.99 2007-01-28 12:28:20.996577
    ## 5       16133         307        1       614   1.99 2007-01-28 14:01:54.996577
    ## 6       16158         316        1      1065   1.99 2007-01-31 07:23:22.996577
    ## 7       16160         318        1       224   9.99 2007-01-26 08:46:53.996577
    ## 8       16161         319        1        15   9.99 2007-01-24 23:07:48.996577
    ## 9       16180         330        2       967   7.99 2007-01-30 17:40:32.996577
    ## 10      16206         351        1      1137   1.99 2007-01-31 17:48:40.996577

``` sql
SELECT  *
FROM payment
WHERE amount IN (1.99, 7.99, 9.99)
LIMIT 10
```

| payment_id | customer_id | staff_id | rental_id | amount | payment_date               |
|-----------:|------------:|---------:|----------:|-------:|:---------------------------|
|      16050 |         269 |        2 |         7 |   1.99 | 2007-01-24 21:40:19.996577 |
|      16056 |         270 |        1 |       193 |   1.99 | 2007-01-26 05:10:14.996577 |
|      16081 |         282 |        2 |        48 |   1.99 | 2007-01-25 04:49:12.996577 |
|      16103 |         294 |        1 |       595 |   1.99 | 2007-01-28 12:28:20.996577 |
|      16133 |         307 |        1 |       614 |   1.99 | 2007-01-28 14:01:54.996577 |
|      16158 |         316 |        1 |      1065 |   1.99 | 2007-01-31 07:23:22.996577 |
|      16160 |         318 |        1 |       224 |   9.99 | 2007-01-26 08:46:53.996577 |
|      16161 |         319 |        1 |        15 |   9.99 | 2007-01-24 23:07:48.996577 |
|      16180 |         330 |        2 |       967 |   7.99 | 2007-01-30 17:40:32.996577 |
|      16206 |         351 |        1 |      1137 |   1.99 | 2007-01-31 17:48:40.996577 |

Displaying records 1 - 10

### Exercise 4.2

Construct a query that retrieve all rows from the payment table where
the amount is greater then 5

``` r
dbGetQuery(con,
  "
  SELECT  *
  FROM payment
  WHERE amount > 5
  LIMIT 10
  ")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16052         269        2       678   6.99 2007-01-28 21:44:14.996577
    ## 2       16058         271        1      1096   8.99 2007-01-31 11:59:15.996577
    ## 3       16060         272        1       405   6.99 2007-01-27 12:01:05.996577
    ## 4       16061         272        1      1041   6.99 2007-01-31 04:14:49.996577
    ## 5       16068         274        1       394   5.99 2007-01-27 09:54:37.996577
    ## 6       16073         276        1       860  10.99 2007-01-30 01:13:42.996577
    ## 7       16074         277        2       308   6.99 2007-01-26 20:30:05.996577
    ## 8       16082         282        2       282   6.99 2007-01-26 17:24:52.996577
    ## 9       16086         284        1      1145   6.99 2007-01-31 18:42:11.996577
    ## 10      16087         286        2        81   6.99 2007-01-25 10:43:45.996577

``` sql
SELECT  *
FROM payment
WHERE amount > 5
LIMIT 10
```

| payment_id | customer_id | staff_id | rental_id | amount | payment_date               |
|-----------:|------------:|---------:|----------:|-------:|:---------------------------|
|      16052 |         269 |        2 |       678 |   6.99 | 2007-01-28 21:44:14.996577 |
|      16058 |         271 |        1 |      1096 |   8.99 | 2007-01-31 11:59:15.996577 |
|      16060 |         272 |        1 |       405 |   6.99 | 2007-01-27 12:01:05.996577 |
|      16061 |         272 |        1 |      1041 |   6.99 | 2007-01-31 04:14:49.996577 |
|      16068 |         274 |        1 |       394 |   5.99 | 2007-01-27 09:54:37.996577 |
|      16073 |         276 |        1 |       860 |  10.99 | 2007-01-30 01:13:42.996577 |
|      16074 |         277 |        2 |       308 |   6.99 | 2007-01-26 20:30:05.996577 |
|      16082 |         282 |        2 |       282 |   6.99 | 2007-01-26 17:24:52.996577 |
|      16086 |         284 |        1 |      1145 |   6.99 | 2007-01-31 18:42:11.996577 |
|      16087 |         286 |        2 |        81 |   6.99 | 2007-01-25 10:43:45.996577 |

Displaying records 1 - 10

### Exercise 4.3

Construct a query that retrives all rows from the payment table where
the amount is greater then 5 and less then 8

``` r
dbGetQuery(con,
  "
  SELECT  *
  FROM payment
  WHERE amount > 5 AND amount < 8
  LIMIT 10
  ")
```

    ##    payment_id customer_id staff_id rental_id amount               payment_date
    ## 1       16052         269        2       678   6.99 2007-01-28 21:44:14.996577
    ## 2       16060         272        1       405   6.99 2007-01-27 12:01:05.996577
    ## 3       16061         272        1      1041   6.99 2007-01-31 04:14:49.996577
    ## 4       16068         274        1       394   5.99 2007-01-27 09:54:37.996577
    ## 5       16074         277        2       308   6.99 2007-01-26 20:30:05.996577
    ## 6       16082         282        2       282   6.99 2007-01-26 17:24:52.996577
    ## 7       16086         284        1      1145   6.99 2007-01-31 18:42:11.996577
    ## 8       16087         286        2        81   6.99 2007-01-25 10:43:45.996577
    ## 9       16092         288        2       427   6.99 2007-01-27 14:38:30.996577
    ## 10      16094         288        2       565   5.99 2007-01-28 07:54:57.996577

``` sql
SELECT  *
FROM payment
WHERE amount > 5 AND amount < 8
LIMIT 10
```

| payment_id | customer_id | staff_id | rental_id | amount | payment_date               |
|-----------:|------------:|---------:|----------:|-------:|:---------------------------|
|      16052 |         269 |        2 |       678 |   6.99 | 2007-01-28 21:44:14.996577 |
|      16060 |         272 |        1 |       405 |   6.99 | 2007-01-27 12:01:05.996577 |
|      16061 |         272 |        1 |      1041 |   6.99 | 2007-01-31 04:14:49.996577 |
|      16068 |         274 |        1 |       394 |   5.99 | 2007-01-27 09:54:37.996577 |
|      16074 |         277 |        2 |       308 |   6.99 | 2007-01-26 20:30:05.996577 |
|      16082 |         282 |        2 |       282 |   6.99 | 2007-01-26 17:24:52.996577 |
|      16086 |         284 |        1 |      1145 |   6.99 | 2007-01-31 18:42:11.996577 |
|      16087 |         286 |        2 |        81 |   6.99 | 2007-01-25 10:43:45.996577 |
|      16092 |         288 |        2 |       427 |   6.99 | 2007-01-27 14:38:30.996577 |
|      16094 |         288 |        2 |       565 |   5.99 | 2007-01-28 07:54:57.996577 |

Displaying records 1 - 10

## Exercise 5

Retrieve all the payment IDs and their amount from the customers whose
last name is ‘DAVIS’.

``` r
dbGetQuery(con,
  "
  SELECT c.customer_id, c.last_name, p.payment_id, p.amount
  FROM customer AS c INNER JOIN payment AS p
  ON c.customer_id = p.customer_id
  WHERE c.last_name = 'DAVIS'
  /*WHERE c.last_name IN ('DAVIS')*/
  ")
```

    ##   customer_id last_name payment_id amount
    ## 1           6     DAVIS      16685   4.99
    ## 2           6     DAVIS      16686   2.99
    ## 3           6     DAVIS      16687   0.99

``` sql
SELECT c.customer_id, c.last_name, p.payment_id, p.amount
FROM customer AS c INNER JOIN payment AS p
ON c.customer_id = p.customer_id
WHERE c.last_name = 'DAVIS'
```

| customer_id | last_name | payment_id | amount |
|------------:|:----------|-----------:|-------:|
|           6 | DAVIS     |      16685 |   4.99 |
|           6 | DAVIS     |      16686 |   2.99 |
|           6 | DAVIS     |      16687 |   0.99 |

3 records

## Exercise 6

### Exercise 6.1

Use COUNT(\*) to count the number of rows in rental

``` r
dbGetQuery(con,
  "
  SELECT  COUNT(*) AS N
  FROM rental
  ")
```

    ##       N
    ## 1 16044

``` sql
SELECT  COUNT(*) AS N
FROM rental
```

|     N |
|------:|
| 16044 |

1 records

### Exercise 6.2

Use COUNT(\*) and GROUP BY to count the number of rentals for each
customer_id

``` r
dbGetQuery(con,
  "
  SELECT customer_id, COUNT(*) AS N
  FROM rental
  GROUP BY customer_id
  LIMIT 10
  ")
```

    ##    customer_id  N
    ## 1            1 32
    ## 2            2 27
    ## 3            3 26
    ## 4            4 22
    ## 5            5 38
    ## 6            6 28
    ## 7            7 33
    ## 8            8 24
    ## 9            9 23
    ## 10          10 25

``` sql
SELECT customer_id, COUNT(*) AS N
FROM rental
GROUP BY customer_id
LIMIT 10
```

| customer_id |   N |
|:------------|----:|
| 1           |  32 |
| 2           |  27 |
| 3           |  26 |
| 4           |  22 |
| 5           |  38 |
| 6           |  28 |
| 7           |  33 |
| 8           |  24 |
| 9           |  23 |
| 10          |  25 |

Displaying records 1 - 10

### Exercise 6.3

Repeat the previous query and sort by the count in descending order

``` r
dbGetQuery(con,
  "
  SELECT customer_id, COUNT(*) AS N
  FROM rental
  GROUP BY customer_id
  ORDER BY N DESC
  LIMIT 10
  ")
```

    ##    customer_id  N
    ## 1          148 46
    ## 2          526 45
    ## 3          236 42
    ## 4          144 42
    ## 5           75 41
    ## 6          469 40
    ## 7          197 40
    ## 8          468 39
    ## 9          178 39
    ## 10         137 39

``` sql
SELECT customer_id, COUNT(*) AS N
FROM rental
GROUP BY customer_id
ORDER BY N DESC
LIMIT 10
```

| customer_id |   N |
|------------:|----:|
|         148 |  46 |
|         526 |  45 |
|         236 |  42 |
|         144 |  42 |
|          75 |  41 |
|         469 |  40 |
|         197 |  40 |
|         468 |  39 |
|         178 |  39 |
|         137 |  39 |

Displaying records 1 - 10

### Exercise 6.4

Repeat the previous query but use HAVING to only keep the groups with 40
or more.

``` r
dbGetQuery(con,
  "
  SELECT customer_id, COUNT(*) AS N
  FROM rental
  GROUP BY customer_id
  HAVING N >= 40
  ORDER BY N DESC
  LIMIT 10
  ")
```

    ##   customer_id  N
    ## 1         148 46
    ## 2         526 45
    ## 3         236 42
    ## 4         144 42
    ## 5          75 41
    ## 6         469 40
    ## 7         197 40

``` sql
SELECT customer_id, COUNT(*) AS N
FROM rental
GROUP BY customer_id
HAVING N >= 40
ORDER BY N DESC
LIMIT 10
```

| customer_id |   N |
|------------:|----:|
|         148 |  46 |
|         526 |  45 |
|         236 |  42 |
|         144 |  42 |
|          75 |  41 |
|         469 |  40 |
|         197 |  40 |

7 records

## Exercise 7

The following query calculates a number of summary statistics for the
payment table using MAX, MIN, AVG and SUM

``` r
dbGetQuery(con,
  "
  SELECT
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
  FROM payment
  ")
```

    ##   max_payment min_payment avg_payment sum_payment
    ## 1       11.99        0.99    4.169775     4824.43

``` sql
SELECT
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
FROM payment
```

| max_payment | min_payment | avg_payment | sum_payment |
|------------:|------------:|------------:|------------:|
|       11.99 |        0.99 |    4.169775 |     4824.43 |

1 records

### Exercise 7.1

Modify the above query to do those calculations for each customer_id

``` r
dbGetQuery(con,
  "
  SELECT customer_id,
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
  FROM payment
  GROUP BY customer_id
  LIMIT 10
  ")
```

    ##    customer_id max_payment min_payment avg_payment sum_payment
    ## 1            1        2.99        0.99    1.990000        3.98
    ## 2            2        4.99        4.99    4.990000        4.99
    ## 3            3        2.99        1.99    2.490000        4.98
    ## 4            5        6.99        0.99    3.323333        9.97
    ## 5            6        4.99        0.99    2.990000        8.97
    ## 6            7        5.99        0.99    4.190000       20.95
    ## 7            8        6.99        6.99    6.990000        6.99
    ## 8            9        4.99        0.99    3.656667       10.97
    ## 9           10        4.99        4.99    4.990000        4.99
    ## 10          11        6.99        6.99    6.990000        6.99

``` sql
SELECT customer_id,
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
FROM payment
GROUP BY customer_id
LIMIT 10
```

| customer_id | max_payment | min_payment | avg_payment | sum_payment |
|------------:|------------:|------------:|------------:|------------:|
|           1 |        2.99 |        0.99 |    1.990000 |        3.98 |
|           2 |        4.99 |        4.99 |    4.990000 |        4.99 |
|           3 |        2.99 |        1.99 |    2.490000 |        4.98 |
|           5 |        6.99 |        0.99 |    3.323333 |        9.97 |
|           6 |        4.99 |        0.99 |    2.990000 |        8.97 |
|           7 |        5.99 |        0.99 |    4.190000 |       20.95 |
|           8 |        6.99 |        6.99 |    6.990000 |        6.99 |
|           9 |        4.99 |        0.99 |    3.656667 |       10.97 |
|          10 |        4.99 |        4.99 |    4.990000 |        4.99 |
|          11 |        6.99 |        6.99 |    6.990000 |        6.99 |

Displaying records 1 - 10

### Exercise 7.2

Modify the above query to only keep the customer_ids that have more then
5 payments

``` r
dbGetQuery(con,
  "
  SELECT customer_id, COUNT(*) AS N,
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
  FROM payment
  GROUP BY customer_id
  HAVING N > 5
  LIMIT 10
  ")
```

    ##    customer_id N max_payment min_payment avg_payment sum_payment
    ## 1           19 6        9.99        0.99    4.490000       26.94
    ## 2           53 6        9.99        0.99    4.490000       26.94
    ## 3          109 7        7.99        0.99    3.990000       27.93
    ## 4          161 6        5.99        0.99    2.990000       17.94
    ## 5          197 8        3.99        0.99    2.615000       20.92
    ## 6          207 6        6.99        0.99    2.990000       17.94
    ## 7          239 6        7.99        2.99    5.656667       33.94
    ## 8          245 6        8.99        0.99    4.823333       28.94
    ## 9          251 6        4.99        1.99    3.323333       19.94
    ## 10         269 6        6.99        0.99    3.156667       18.94

``` sql
SELECT customer_id, COUNT(*) AS N,
    MAX(amount) AS max_payment,
    MIN(amount) AS min_payment,
    AVG(amount) AS avg_payment,
    SUM(amount) AS sum_payment
FROM payment
GROUP BY customer_id
HAVING N > 5
LIMIT 10
```

| customer_id |   N | max_payment | min_payment | avg_payment | sum_payment |
|------------:|----:|------------:|------------:|------------:|------------:|
|          19 |   6 |        9.99 |        0.99 |    4.490000 |       26.94 |
|          53 |   6 |        9.99 |        0.99 |    4.490000 |       26.94 |
|         109 |   7 |        7.99 |        0.99 |    3.990000 |       27.93 |
|         161 |   6 |        5.99 |        0.99 |    2.990000 |       17.94 |
|         197 |   8 |        3.99 |        0.99 |    2.615000 |       20.92 |
|         207 |   6 |        6.99 |        0.99 |    2.990000 |       17.94 |
|         239 |   6 |        7.99 |        2.99 |    5.656667 |       33.94 |
|         245 |   6 |        8.99 |        0.99 |    4.823333 |       28.94 |
|         251 |   6 |        4.99 |        1.99 |    3.323333 |       19.94 |
|         269 |   6 |        6.99 |        0.99 |    3.156667 |       18.94 |

Displaying records 1 - 10

## Cleanup

Run the following chunk to disconnect from the connection.

``` r
# clean up
dbDisconnect(con)
```
