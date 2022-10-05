lab07
================
sl
2022-10-05

``` r
setwd("/Users/samuellu/Desktop/PM566/GitHub/pm566-fall2022-labs_Sam/lab07/")
```

## Q1: Howmany papers are on Sars-cov-2?

``` r
# Downloading the website
website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2")

# Finding the counts
counts <- xml2::xml_find_first(website, "/html/body/main/div[9]/div[2]/div[2]/div[1]/div[1]")

# Turning it into text
counts <- as.character(counts)

# Extracting the data using regex
stringr::str_extract(counts, "[0-9,]+")
```

    ## [1] "179,021"

## Question 2: Academic publications on COVID19 and Hawaii

You need to query the following The parameters passed to the query are
documented here.

Use the function httr::GET() to make the following query:

Baseline URL:
<https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi>

Query parameters:

db: pubmed term: covid19 hawaii retmax: 1000

``` r
library(httr)
query_ids <- GET(
  url   = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi",
  query = list(
    db      = "pubmed",
    term    = "covid19 hawaii",
    retmax  = 1000)
)

# Extracting the content of the response of GET
ids <- httr::content(query_ids)
```

## Question 3: Get details about the articles

The Ids are wrapped around text in the following way: <Id>… id number
…</Id>. we can use a regular expression that extract that information.
Fill out the following lines of code:

``` r
# Turn the result into a character vector
ids <- as.character(ids)

# Find all the ids 
ids <- stringr::str_extract_all(ids, "<Id>[[:digit:]]+</Id>")[[1]]

# Remove all the leading and trailing <Id> </Id>. Make use of "|"
ids <- stringr::str_remove_all(ids, "</?Id>")
```

Grab publications with these Pubmed ID list.

``` r
publications <- GET(
  url   = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi",
  query = list(
    db      = "pubmed",
    id      = paste(ids,collapse =","),
    retmax  = 1000,
    rettype = "abstract"
    )
)

# Turning the output into character vector
publications <- httr::content(publications)
publications_txt <- as.character(publications)
```
