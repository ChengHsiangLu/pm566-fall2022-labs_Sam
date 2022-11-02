lab10
================
sl
2022-11-02

## Data setup

``` r
# install.packages(c("RSQLite", "DBI"))

library(RSQLite)
library(DBI)

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
  ")
```

    ##     actor_id  first_name    last_name
    ## 1         71        ADAM        GRANT
    ## 2        132        ADAM       HOPPER
    ## 3        165          AL      GARLAND
    ## 4        173        ALAN     DREYFUSS
    ## 5        125      ALBERT        NOLTE
    ## 6        146      ALBERT    JOHANSSON
    ## 7         29        ALEC        WAYNE
    ## 8         65      ANGELA       HUDSON
    ## 9        144      ANGELA  WITHERSPOON
    ## 10        76    ANGELINA      ASTAIRE
    ## 11        49        ANNE       CRONYN
    ## 12        34      AUDREY      OLIVIER
    ## 13       190      AUDREY       BAILEY
    ## 14       196        BELA       WALKEN
    ## 15        83         BEN       WILLIS
    ## 16       152         BEN       HARRIS
    ## 17         6       BETTE    NICHOLSON
    ## 18        19         BOB      FAWCETT
    ## 19        36        BURT      DUKAKIS
    ## 20        75        BURT        POSEY
    ## 21       193        BURT       TEMPLE
    ## 22        24     CAMERON       STREEP
    ## 23        63     CAMERON         WRAY
    ## 24       111     CAMERON    ZELLWEGER
    ## 25        52      CARMEN         HUNT
    ## 26        77        CARY  MCCONAUGHEY
    ## 27       128        CATE      MCQUEEN
    ## 28       141        CATE       HARRIS
    ## 29        89    CHARLIZE        DENCH
    ## 30        98       CHRIS      BRIDGES
    ## 31       160       CHRIS         DEPP
    ## 32        10   CHRISTIAN        GABLE
    ## 33        58   CHRISTIAN       AKROYD
    ## 34        61   CHRISTIAN       NEESON
    ## 35        91 CHRISTOPHER        BERRY
    ## 36       163 CHRISTOPHER         WEST
    ## 37        15        CUBA      OLIVIER
    ## 38       118        CUBA        ALLEN
    ## 39       189        CUBA        BIRCH
    ## 40        18         DAN         TORN
    ## 41        56         DAN       HARRIS
    ## 42       116         DAN       STREEP
    ## 43        95       DARYL     WAHLBERG
    ## 44       129       DARYL     CRAWFORD
    ## 45       182      DEBBIE       AKROYD
    ## 46        59      DUSTIN       TAUTOU
    ## 47         3          ED        CHASE
    ## 48       136          ED    MANSFIELD
    ## 49       179          ED      GUINESS
    ## 50        93       ELLEN      PRESLEY
    ## 51        22       ELVIS         MARX
    ## 52       148       EMILY          DEE
    ## 53       139        EWAN      GOODING
    ## 54        55         FAY       KILMER
    ## 55       147         FAY      WINSLET
    ## 56       156         FAY         WOOD
    ## 57        48     FRANCES    DAY-LEWIS
    ## 58       126     FRANCES        TOMEI
    ## 59        16        FRED      COSTNER
    ## 60        51        GARY      PHOENIX
    ## 61        73        GARY         PENN
    ## 62        96        GENE       WILLIS
    ## 63       134        GENE      HOPKINS
    ## 64       177        GENE     MCKELLEN
    ## 65       151    GEOFFREY       HESTON
    ## 66       107        GINA    DEGENERES
    ## 67        39      GOLDIE        BRODY
    ## 68         7       GRACE       MOSTEL
    ## 69        86        GREG      CHAPLIN
    ## 70       191     GREGORY      GOODING
    ## 71       130       GRETA       KEITEL
    ## 72       157       GRETA       MALDEN
    ## 73        78     GROUCHO      SINATRA
    ## 74       106     GROUCHO        DUNST
    ## 75       172     GROUCHO     WILLIAMS
    ## 76       115    HARRISON         BALE
    ## 77       161      HARVEY         HOPE
    ## 78        17       HELEN       VOIGHT
    ## 79        60       HENRY        BERRY
    ## 80       164    HUMPHREY       WILLIS
    ## 81       184    HUMPHREY      GARLAND
    ## 82       155         IAN        TANDY
    ## 83       142        JADA        RYDER
    ## 84        84       JAMES         PITT
    ## 85       131        JANE      JACKMAN
    ## 86        62       JAYNE       NEESON
    ## 87       150       JAYNE        NOLTE
    ## 88       195       JAYNE  SILVERSTONE
    ## 89       180        JEFF  SILVERSTONE
    ## 90         4    JENNIFER        DAVIS
    ## 91        67     JESSICA       BAILEY
    ## 92        99         JIM       MOSTEL
    ## 93        41       JODIE    DEGENERES
    ## 94         9         JOE        SWANK
    ## 95       192        JOHN       SUVARI
    ## 96         5      JOHNNY LOLLOBRIGIDA
    ## 97        40      JOHNNY         CAGE
    ## 98       176         JON        CHASE
    ## 99        57        JUDE       CRUISE
    ## 100       35        JUDY         DEAN
    ## 101       27       JULIA      MCQUEEN
    ## 102       47       JULIA    BARRYMORE
    ## 103      186       JULIA    ZELLWEGER
    ## 104      199       JULIA      FAWCETT
    ## 105      123    JULIANNE        DENCH
    ## 106       12        KARL        BERRY
    ## 107       69     KENNETH      PALTROW
    ## 108       88     KENNETH        PESCI
    ## 109       94     KENNETH         TORN
    ## 110      169     KENNETH      HOFFMAN
    ## 111       25       KEVIN        BLOOM
    ## 112      127       KEVIN      GARLAND
    ## 113      145         KIM        ALLEN
    ## 114       43        KIRK     JOVOVICH
    ## 115       21     KIRSTEN      PALTROW
    ## 116       92     KIRSTEN       AKROYD
    ## 117      159       LAURA        BRODY
    ## 118      167    LAURENCE      BULLOCK
    ## 119      178        LISA       MONROE
    ## 120      121        LIZA      BERGMAN
    ## 121       20     LUCILLE        TRACY
    ## 122      138     LUCILLE          DEE
    ## 123       79         MAE      HOFFMAN
    ## 124       66        MARY        TANDY
    ## 125      198        MARY       KEITEL
    ## 126        8     MATTHEW    JOHANSSON
    ## 127      103     MATTHEW        LEIGH
    ## 128      181     MATTHEW       CARREY
    ## 129       97         MEG        HAWKE
    ## 130       53        MENA       TEMPLE
    ## 131      170        MENA       HOPPER
    ## 132      154       MERYL       GIBSON
    ## 133      194       MERYL        ALLEN
    ## 134      174     MICHAEL       BENING
    ## 135      185     MICHAEL       BOLGER
    ## 136       70    MICHELLE  MCCONAUGHEY
    ## 137       33       MILLA         PECK
    ## 138       74       MILLA       KEITEL
    ## 139       85      MINNIE    ZELLWEGER
    ## 140      153      MINNIE       KILMER
    ## 141      113      MORGAN      HOPKINS
    ## 142      114      MORGAN    MCDORMAND
    ## 143      137      MORGAN     WILLIAMS
    ## 144       50     NATALIE      HOPKINS
    ## 145        2        NICK     WAHLBERG
    ## 146       44        NICK     STALLONE
    ## 147      166        NICK    DEGENERES
    ## 148      171     OLYMPIA     PFEIFFER
    ## 149      162       OPRAH       KILMER
    ## 150       46      PARKER     GOLDBERG
    ## 151        1    PENELOPE      GUINESS
    ## 152       54    PENELOPE      PINKETT
    ## 153      104    PENELOPE       CRONYN
    ## 154      120    PENELOPE       MONROE
    ## 155       80       RALPH         CRUZ
    ## 156       64         RAY    JOHANSSON
    ## 157       45       REESE       KILMER
    ## 158      197       REESE         WEST
    ## 159      117       RENEE        TRACY
    ## 160      187       RENEE         BALL
    ## 161      133     RICHARD         PENN
    ## 162       26         RIP     CRAWFORD
    ## 163       68         RIP      WINSLET
    ## 164      135        RITA     REYNOLDS
    ## 165      143       RIVER         DEAN
    ## 166      188        ROCK      DUKAKIS
    ## 167      112     RUSSELL       BACALL
    ## 168      149     RUSSELL       TEMPLE
    ## 169      183     RUSSELL        CLOSE
    ## 170      122       SALMA        NOLTE
    ## 171       23      SANDRA       KILMER
    ## 172       30      SANDRA         PECK
    ## 173       81    SCARLETT        DAMON
    ## 174      124    SCARLETT       BENING
    ## 175       72        SEAN     WILLIAMS
    ## 176       90        SEAN      GUINESS
    ## 177      105      SIDNEY        CROWE
    ## 178       31       SISSY     SOBIESKI
    ## 179       87     SPENCER         PECK
    ## 180      100     SPENCER         DEPP
    ## 181      101       SUSAN        DAVIS
    ## 182      110       SUSAN        DAVIS
    ## 183      109   SYLVESTER         DERN
    ## 184      200       THORA       TEMPLE
    ## 185       32         TIM      HACKMAN
    ## 186       38         TOM     MCKELLEN
    ## 187       42         TOM      MIRANDA
    ## 188      201         TOM       CRUISE
    ## 189      202         TOM        HANKS
    ## 190      203         TOM       CRUISE
    ## 191      204         TOM        HANKS
    ## 192      205         TOM       CRUISE
    ## 193      206         TOM        HANKS
    ## 194      207         TOM       CRUISE
    ## 195      208         TOM        HANKS
    ## 196       13         UMA         WOOD
    ## 197       37         VAL       BOLGER
    ## 198       14      VIVIEN       BERGEN
    ## 199      158      VIVIEN     BASINGER
    ## 200      102      WALTER         TORN
    ## 201      108      WARREN        NOLTE
    ## 202      119      WARREN      JACKMAN
    ## 203      140      WHOOPI         HURT
    ## 204      168        WILL       WILSON
    ## 205      175     WILLIAM      HACKMAN
    ## 206       28       WOODY      HOFFMAN
    ## 207       82       WOODY        JOLIE
    ## 208       11        ZERO         CAGE

## Use SQL

``` sql
SELECT actor_id, first_name, last_name
FROM actor
ORDER by first_name, first_name
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

Retrive the actor ID, first name, and last name for actors whose last
name equals ‘WILLIAMS’ or ‘DAVIS’.

``` r
dbGetQuery(con,
  "
  SELECT actor_id, first_name, last_name
  FROM actor
  WHERE first_name = 'WILLIAMS'
  ")
```

    ## [1] actor_id   first_name last_name 
    ## <0 rows> (or 0-length row.names)

``` r
# clean up
dbDisconnect(con)
```
