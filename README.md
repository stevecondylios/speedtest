speedtest
================

Tools to Test and Compare Internet Bandwidth Speeds

## Description

The ‘Ookla’ ‘Speedtest’ site <http://beta.speedtest.net/about> provides
interactive and programmatic services to test and compare bandwidth
speeds from a source node on the Internet to thousands of test servers.
Tools are provided to obtain test server lists, identify target servers
for testing and performing speed/bandwidth tests.

## What’s Inside The Tin

The following functions are implemented:

  - `spd_best_servers`: Find “best” servers (latency-wise) from master
    server list
  - `spd_closest_servers`: Find “closest” servers (geography-wise) from
    master server list
  - `spd_compute_bandwidth`: Compute bandwidth from bytes transferred
    and time taken
  - `spd_config`: Retrieve client configuration information for the
    speedtest
  - `spd_download_test`: Perform a download speed/bandwidth test
  - `spd_servers`: Retrieve a list of SpeedTest servers
  - `spd_upload_test`: Perform an upload speed/bandwidth test

## TODO

Folks interested in contributing can take a look at the TODOs and pick
as many as you like\! Ones with question marks are truly a “I dunno if
we shld” kinda thing. Ones with exclamation marks are essentials.

  - \[ \] Cache config in memory at startup vs pass around to functions?
  - \[ \] Figure out how to use beta sockets hidden API vs the old Flash
    API?
  - \[ \] Ensure the efficacy of relying on the cURL timings for speed
    measures for the Flash API
  - \[ \] Figure out best way to capture the results for post-processing
  - \[ \] Upload results to speedtest (tis only fair)\!
  - \[ \] Incorporate more network or host measures for better
    statistical determination of the best target\!
  - \[ \] `autoplot` support\!
  - \[ \] RStudio Add-in
  - \[ \] CLI wrapper
  - \[ \] Shiny app?

## Installation

``` r
devtools::install_github("hrbrmstr/speedtest")
```

## Usage

``` r
library(speedtest)
library(stringi)
library(hrbrthemes)
library(ggbeeswarm)
library(tidyverse)

# current verison
packageVersion("speedtest")
```

    ## [1] '0.1.0'

### Download Speed

``` r
config <- spd_config()

servers <- spd_servers(config=config)
closest_servers <- spd_closest_servers(servers, config=config)
only_the_best_severs <- spd_best_servers(closest_servers, config)
```

### Individual download tests

``` r
glimpse(spd_download_test(closest_servers[1,], config=config))
```

    ## Observations: 1
    ## Variables: 15
    ## $ url     <chr> "http://speed0.xcelx.net/speedtest/upload.php"
    ## $ lat     <dbl> 42.3875
    ## $ lng     <dbl> -71.1
    ## $ name    <chr> "Somerville, MA"
    ## $ country <chr> "United States"
    ## $ cc      <chr> "US"
    ## $ sponsor <chr> "Axcelx Technologies LLC"
    ## $ id      <chr> "5960"
    ## $ host    <chr> "speed0.xcelx.net:8080"
    ## $ url2    <chr> "http://speed1.xcelx.net/speedtest/upload.php"
    ## $ min     <dbl> 15.08594
    ## $ mean    <dbl> 72.25247
    ## $ median  <dbl> 77.6195
    ## $ max     <dbl> 126.3629
    ## $ sd      <dbl> 42.88343

``` r
glimpse(spd_download_test(only_the_best_severs[1,], config=config))
```

    ## Observations: 1
    ## Variables: 18
    ## $ ping_time      <dbl> 0.036793
    ## $ total_time     <dbl> 0.06847
    ## $ retrieval_time <dbl> 1.5e-05
    ## $ url            <chr> "http://speedtest.norwoodlight.com/speedtest/upload.php"
    ## $ lat            <dbl> 42.1944
    ## $ lng            <dbl> -71.2
    ## $ name           <chr> "Norwood, MA"
    ## $ country        <chr> "United States"
    ## $ cc             <chr> "US"
    ## $ sponsor        <chr> "Norwood Light Broadband"
    ## $ id             <chr> "4920"
    ## $ host           <chr> "speedtest.norwoodlight.com:8080"
    ## $ url2           <chr> "http://netgauge.norwoodlight.com/speedtest/upload.php"
    ## $ min            <dbl> 10.0315
    ## $ mean           <dbl> 65.53074
    ## $ median         <dbl> 29.70143
    ## $ max            <dbl> 169.5498
    ## $ sd             <dbl> 62.35961

### Individual upload tests

``` r
glimpse(spd_upload_test(only_the_best_severs[1,], config=config))
```

    ## Observations: 1
    ## Variables: 18
    ## $ ping_time      <dbl> 0.036793
    ## $ total_time     <dbl> 0.06847
    ## $ retrieval_time <dbl> 1.5e-05
    ## $ url            <chr> "http://speedtest.norwoodlight.com/speedtest/upload.php"
    ## $ lat            <dbl> 42.1944
    ## $ lng            <dbl> -71.2
    ## $ name           <chr> "Norwood, MA"
    ## $ country        <chr> "United States"
    ## $ cc             <chr> "US"
    ## $ sponsor        <chr> "Norwood Light Broadband"
    ## $ id             <chr> "4920"
    ## $ host           <chr> "speedtest.norwoodlight.com:8080"
    ## $ url2           <chr> "http://netgauge.norwoodlight.com/speedtest/upload.php"
    ## $ min            <dbl> 6.855551
    ## $ mean           <dbl> 8.643362
    ## $ median         <dbl> 8.680511
    ## $ max            <dbl> 10.2592
    ## $ sd             <dbl> 1.52269

``` r
glimpse(spd_upload_test(closest_servers[1,], config=config))
```

    ## Observations: 1
    ## Variables: 15
    ## $ url     <chr> "http://speed0.xcelx.net/speedtest/upload.php"
    ## $ lat     <dbl> 42.3875
    ## $ lng     <dbl> -71.1
    ## $ name    <chr> "Somerville, MA"
    ## $ country <chr> "United States"
    ## $ cc      <chr> "US"
    ## $ sponsor <chr> "Axcelx Technologies LLC"
    ## $ id      <chr> "5960"
    ## $ host    <chr> "speed0.xcelx.net:8080"
    ## $ url2    <chr> "http://speed1.xcelx.net/speedtest/upload.php"
    ## $ min     <dbl> 3.505325
    ## $ mean    <dbl> 6.558145
    ## $ median  <dbl> 7.087147
    ## $ max     <dbl> 9.341766
    ## $ sd      <dbl> 2.102159

### Moar download tests

Choose closest, “best” and randomly (there can be, and are, some dups as
a result for best/closest), run the test and chart the results. This
will show just how disparate the results are from these core/crude
tests. Most of the test servers compensate when they present the
results. Newer, “socket”-based tests are more accurate but there are no
free/hidden exposed APIs yet for most of them.

``` r
set.seed(8675309)

bind_rows(

  closest_servers[1:3,] %>%
    mutate(type="closest"),

  only_the_best_severs[1:3,] %>%
    mutate(type="best"),

  filter(servers, !(id %in% c(closest_servers[1:3,]$id, only_the_best_severs[1:3,]$id))) %>%
    sample_n(3) %>%
    mutate(type="random")

) %>%
  group_by(type) %>%
  ungroup() -> to_compare

select(to_compare, sponsor, name, country, host, type)
```

    ## # A tibble: 9 x 5
    ##                   sponsor            name       country                                host    type
    ##                     <chr>           <chr>         <chr>                               <chr>   <chr>
    ## 1 Axcelx Technologies LLC  Somerville, MA United States               speed0.xcelx.net:8080 closest
    ## 2                 Comcast      Boston, MA United States stosat-ndhm-01.sys.comcast.net:8080 closest
    ## 3            Starry, Inc.      Boston, MA United States    speedtest-server.starry.com:8080 closest
    ## 4 Norwood Light Broadband     Norwood, MA United States     speedtest.norwoodlight.com:8080    best
    ## 5                 vzalpha     Waltham, MA United States         pdi-ookla2.vzalpha.com:8080    best
    ## 6          BELD Broadband   Braintree, MA United States                 wotan.beld.net:8080    best
    ## 7                  SowNet         Gliwice        Poland            speedtest.sownet.pl:8080  random
    ## 8                 SingTel Los Angeles, CA United States    speedtest-la.singnet.com.sg:8080  random
    ## 9                 StarNet         Bandung     Indonesia       speedtest.starnet.net.id:8080  random

``` r
map_df(1:nrow(to_compare), ~{
  spd_download_test(to_compare[.x,], config=config, summarise=FALSE, timeout=30)
}) -> dl_results_full
```

``` r
mutate(dl_results_full, type=stri_trans_totitle(type)) %>%
  ggplot(aes(type, bw, fill=type)) +
  geom_quasirandom(aes(size=size, color=type), width=0.15, shape=21, stroke=0.25) +
  scale_y_continuous(expand=c(0,5), labels=c(sprintf("%s", seq(0,150,50)), "200 Mb/s"), limits=c(0,200)) +
  scale_size(range=c(2,6)) +
  scale_color_manual(values=c(Random="#b2b2b2", Best="#2b2b2b", Closest="#2b2b2b")) +
  scale_fill_ipsum() +
  labs(x=NULL, y=NULL, title="Download bandwidth test by selected server type",
       subtitle="Circle size scaled by size of file used in that speed test") +
  theme_ipsum_rc(grid="Y") +
  theme(legend.position="none")
```

![](README_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

### Moar upload tests

Choose closest and “best” and filter duplicates out since we’re really
trying to measure here vs show the disparity:

``` r
bind_rows(
  closest_servers[1:3,] %>% mutate(type="closest"),
  only_the_best_severs[1:3,] %>% mutate(type="best")
) %>%
  distinct(.keep_all=TRUE) -> to_compare

select(to_compare, sponsor, name, country, host, type)
```

    ## # A tibble: 6 x 5
    ##                   sponsor           name       country                                host    type
    ##                     <chr>          <chr>         <chr>                               <chr>   <chr>
    ## 1 Axcelx Technologies LLC Somerville, MA United States               speed0.xcelx.net:8080 closest
    ## 2                 Comcast     Boston, MA United States stosat-ndhm-01.sys.comcast.net:8080 closest
    ## 3            Starry, Inc.     Boston, MA United States    speedtest-server.starry.com:8080 closest
    ## 4 Norwood Light Broadband    Norwood, MA United States     speedtest.norwoodlight.com:8080    best
    ## 5                 vzalpha    Waltham, MA United States         pdi-ookla2.vzalpha.com:8080    best
    ## 6          BELD Broadband  Braintree, MA United States                 wotan.beld.net:8080    best

``` r
map_df(1:nrow(to_compare), ~{
  spd_upload_test(to_compare[.x,], config=config, summarise=FALSE, timeout=30)
}) -> ul_results_full
```

``` r
ggplot(ul_results_full, aes(x="Upload Test", y=bw)) +
  geom_quasirandom(aes(size=size, fill="col"), width=0.1, shape=21, stroke=0.25, color="#2b2b2b") +
  scale_y_continuous(expand=c(0,0.5), breaks=seq(0,16,4),
                     labels=c(sprintf("%s", seq(0,12,4)), "16 Mb/s"), limits=c(0,16)) +
  scale_size(range=c(2,6)) +
  scale_fill_ipsum() +
  labs(x=NULL, y=NULL, title="Upload bandwidth test by selected server type",
       subtitle="Circle size scaled by size of file used in that speed test") +
  theme_ipsum_rc(grid="Y") +
  theme(legend.position="none")
```

![](README_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](CONDUCT.md). By participating in this project you agree to
abide by its terms.