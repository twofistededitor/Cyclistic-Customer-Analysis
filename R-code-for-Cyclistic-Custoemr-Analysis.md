R code for Cyclistic project
================
Autumn Theriault
2024-08-28

## Load the necessary libraries

## Set dplyr::filter and dplyr::lag as the default choices

``` r
conflict_prefer("filter", "dplyr")
```

    ## [conflicted] Will prefer dplyr::filter over any other package.

``` r
conflict_prefer("lag", "dplyr")
```

    ## [conflicted] Will prefer dplyr::lag over any other package.

## Upload Divvy datasets (csv files) here

``` r
q4_2019 <- read_csv("Divvy_Trips_2019_Q4.csv")
```

    ## Rows: 704054 Columns: 12
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (4): from_station_name, to_station_name, usertype, gender
    ## dbl  (5): trip_id, bikeid, from_station_id, to_station_id, birthyear
    ## num  (1): tripduration
    ## dttm (2): start_time, end_time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
q1_2020 <- read_csv("Divvy_Trips_2020_Q1.csv")
```

    ## Rows: 426887 Columns: 13
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (5): ride_id, rideable_type, start_station_name, end_station_name, memb...
    ## dbl  (6): start_station_id, end_station_id, start_lat, start_lng, end_lat, e...
    ## dttm (2): started_at, ended_at
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Examine and compare column names each of the files

``` r
colnames(q4_2019)
```

    ##  [1] "trip_id"           "start_time"        "end_time"         
    ##  [4] "bikeid"            "tripduration"      "from_station_id"  
    ##  [7] "from_station_name" "to_station_id"     "to_station_name"  
    ## [10] "usertype"          "gender"            "birthyear"

``` r
colnames(q1_2020)
```

    ##  [1] "ride_id"            "rideable_type"      "started_at"        
    ##  [4] "ended_at"           "start_station_name" "start_station_id"  
    ##  [7] "end_station_name"   "end_station_id"     "start_lat"         
    ## [10] "start_lng"          "end_lat"            "end_lng"           
    ## [13] "member_casual"

## Rename columns to make them all consistent with q1_2020

``` r
(q4_2019 <- rename(q4_2019
                   ,ride_id = trip_id
                   ,rideable_type = bikeid
                   ,started_at = start_time
                   ,ended_at = end_time
                   ,start_station_name = from_station_name
                   ,start_station_id = from_station_id
                   ,end_station_name = to_station_name
                   ,end_station_id = to_station_id
                   ,member_casual = usertype
                   ))
```

    ## # A tibble: 704,054 × 12
    ##     ride_id started_at          ended_at            rideable_type tripduration
    ##       <dbl> <dttm>              <dttm>                      <dbl>        <dbl>
    ##  1 25223640 2019-10-01 00:01:39 2019-10-01 00:17:20          2215          940
    ##  2 25223641 2019-10-01 00:02:16 2019-10-01 00:06:34          6328          258
    ##  3 25223642 2019-10-01 00:04:32 2019-10-01 00:18:43          3003          850
    ##  4 25223643 2019-10-01 00:04:32 2019-10-01 00:43:43          3275         2350
    ##  5 25223644 2019-10-01 00:04:34 2019-10-01 00:35:42          5294         1867
    ##  6 25223645 2019-10-01 00:04:38 2019-10-01 00:10:51          1891          373
    ##  7 25223646 2019-10-01 00:04:52 2019-10-01 00:22:45          1061         1072
    ##  8 25223647 2019-10-01 00:04:57 2019-10-01 00:29:16          1274         1458
    ##  9 25223648 2019-10-01 00:05:20 2019-10-01 00:29:18          6011         1437
    ## 10 25223649 2019-10-01 00:05:20 2019-10-01 02:23:46          2957         8306
    ## # ℹ 704,044 more rows
    ## # ℹ 7 more variables: start_station_id <dbl>, start_station_name <chr>,
    ## #   end_station_id <dbl>, end_station_name <chr>, member_casual <chr>,
    ## #   gender <chr>, birthyear <dbl>

## Inspect the dataframes and check for consistency in data types

``` r
str(q4_2019)
```

    ## spc_tbl_ [704,054 × 12] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ ride_id           : num [1:704054] 25223640 25223641 25223642 25223643 25223644 ...
    ##  $ started_at        : POSIXct[1:704054], format: "2019-10-01 00:01:39" "2019-10-01 00:02:16" ...
    ##  $ ended_at          : POSIXct[1:704054], format: "2019-10-01 00:17:20" "2019-10-01 00:06:34" ...
    ##  $ rideable_type     : num [1:704054] 2215 6328 3003 3275 5294 ...
    ##  $ tripduration      : num [1:704054] 940 258 850 2350 1867 ...
    ##  $ start_station_id  : num [1:704054] 20 19 84 313 210 156 84 156 156 336 ...
    ##  $ start_station_name: chr [1:704054] "Sheffield Ave & Kingsbury St" "Throop (Loomis) St & Taylor St" "Milwaukee Ave & Grand Ave" "Lakeview Ave & Fullerton Pkwy" ...
    ##  $ end_station_id    : num [1:704054] 309 241 199 290 382 226 142 463 463 336 ...
    ##  $ end_station_name  : chr [1:704054] "Leavitt St & Armitage Ave" "Morgan St & Polk St" "Wabash Ave & Grand Ave" "Kedzie Ave & Palmer Ct" ...
    ##  $ member_casual     : chr [1:704054] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
    ##  $ gender            : chr [1:704054] "Male" "Male" "Female" "Male" ...
    ##  $ birthyear         : num [1:704054] 1987 1998 1991 1990 1987 ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   trip_id = col_double(),
    ##   ..   start_time = col_datetime(format = ""),
    ##   ..   end_time = col_datetime(format = ""),
    ##   ..   bikeid = col_double(),
    ##   ..   tripduration = col_number(),
    ##   ..   from_station_id = col_double(),
    ##   ..   from_station_name = col_character(),
    ##   ..   to_station_id = col_double(),
    ##   ..   to_station_name = col_character(),
    ##   ..   usertype = col_character(),
    ##   ..   gender = col_character(),
    ##   ..   birthyear = col_double()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

``` r
str(q1_2020)
```

    ## spc_tbl_ [426,887 × 13] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ ride_id           : chr [1:426887] "EACB19130B0CDA4A" "8FED874C809DC021" "789F3C21E472CA96" "C9A388DAC6ABF313" ...
    ##  $ rideable_type     : chr [1:426887] "docked_bike" "docked_bike" "docked_bike" "docked_bike" ...
    ##  $ started_at        : POSIXct[1:426887], format: "2020-01-21 20:06:59" "2020-01-30 14:22:39" ...
    ##  $ ended_at          : POSIXct[1:426887], format: "2020-01-21 20:14:30" "2020-01-30 14:26:22" ...
    ##  $ start_station_name: chr [1:426887] "Western Ave & Leland Ave" "Clark St & Montrose Ave" "Broadway & Belmont Ave" "Clark St & Randolph St" ...
    ##  $ start_station_id  : num [1:426887] 239 234 296 51 66 212 96 96 212 38 ...
    ##  $ end_station_name  : chr [1:426887] "Clark St & Leland Ave" "Southport Ave & Irving Park Rd" "Wilton Ave & Belmont Ave" "Fairbanks Ct & Grand Ave" ...
    ##  $ end_station_id    : num [1:426887] 326 318 117 24 212 96 212 212 96 100 ...
    ##  $ start_lat         : num [1:426887] 42 42 41.9 41.9 41.9 ...
    ##  $ start_lng         : num [1:426887] -87.7 -87.7 -87.6 -87.6 -87.6 ...
    ##  $ end_lat           : num [1:426887] 42 42 41.9 41.9 41.9 ...
    ##  $ end_lng           : num [1:426887] -87.7 -87.7 -87.7 -87.6 -87.6 ...
    ##  $ member_casual     : chr [1:426887] "member" "member" "member" "member" ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   ride_id = col_character(),
    ##   ..   rideable_type = col_character(),
    ##   ..   started_at = col_datetime(format = ""),
    ##   ..   ended_at = col_datetime(format = ""),
    ##   ..   start_station_name = col_character(),
    ##   ..   start_station_id = col_double(),
    ##   ..   end_station_name = col_character(),
    ##   ..   end_station_id = col_double(),
    ##   ..   start_lat = col_double(),
    ##   ..   start_lng = col_double(),
    ##   ..   end_lat = col_double(),
    ##   ..   end_lng = col_double(),
    ##   ..   member_casual = col_character()
    ##   .. )
    ##  - attr(*, "problems")=<externalptr>

## Make sure ride_id and rideable_type have the same data type

``` r
q4_2019 <-  mutate(q4_2019, ride_id = as.character(ride_id)
                   ,rideable_type = as.character(rideable_type)) 
```

## Now that everything matches, the two dataframes can be combined into a new one called all_trips

``` r
all_trips <- bind_rows(q4_2019, q1_2020)#, q3_2019)#, q4_2019, q1_2020)
```

## Remove lat, long, birthyear, and gender fields

``` r
all_trips <- all_trips %>%  
  select(-c(start_lat, start_lng, end_lat, end_lng, birthyear, gender,  "tripduration"))
```

## Inspect the new all_trips dataframe

``` r
colnames(all_trips)
```

    ## [1] "ride_id"            "started_at"         "ended_at"          
    ## [4] "rideable_type"      "start_station_id"   "start_station_name"
    ## [7] "end_station_id"     "end_station_name"   "member_casual"

``` r
nrow(all_trips)
```

    ## [1] 1130941

``` r
dim(all_trips)
```

    ## [1] 1130941       9

``` r
head(all_trips)
```

    ## # A tibble: 6 × 9
    ##   ride_id started_at          ended_at            rideable_type start_station_id
    ##   <chr>   <dttm>              <dttm>              <chr>                    <dbl>
    ## 1 252236… 2019-10-01 00:01:39 2019-10-01 00:17:20 2215                        20
    ## 2 252236… 2019-10-01 00:02:16 2019-10-01 00:06:34 6328                        19
    ## 3 252236… 2019-10-01 00:04:32 2019-10-01 00:18:43 3003                        84
    ## 4 252236… 2019-10-01 00:04:32 2019-10-01 00:43:43 3275                       313
    ## 5 252236… 2019-10-01 00:04:34 2019-10-01 00:35:42 5294                       210
    ## 6 252236… 2019-10-01 00:04:38 2019-10-01 00:10:51 1891                       156
    ## # ℹ 4 more variables: start_station_name <chr>, end_station_id <dbl>,
    ## #   end_station_name <chr>, member_casual <chr>

``` r
str(all_trips)  
```

    ## tibble [1,130,941 × 9] (S3: tbl_df/tbl/data.frame)
    ##  $ ride_id           : chr [1:1130941] "25223640" "25223641" "25223642" "25223643" ...
    ##  $ started_at        : POSIXct[1:1130941], format: "2019-10-01 00:01:39" "2019-10-01 00:02:16" ...
    ##  $ ended_at          : POSIXct[1:1130941], format: "2019-10-01 00:17:20" "2019-10-01 00:06:34" ...
    ##  $ rideable_type     : chr [1:1130941] "2215" "6328" "3003" "3275" ...
    ##  $ start_station_id  : num [1:1130941] 20 19 84 313 210 156 84 156 156 336 ...
    ##  $ start_station_name: chr [1:1130941] "Sheffield Ave & Kingsbury St" "Throop (Loomis) St & Taylor St" "Milwaukee Ave & Grand Ave" "Lakeview Ave & Fullerton Pkwy" ...
    ##  $ end_station_id    : num [1:1130941] 309 241 199 290 382 226 142 463 463 336 ...
    ##  $ end_station_name  : chr [1:1130941] "Leavitt St & Armitage Ave" "Morgan St & Polk St" "Wabash Ave & Grand Ave" "Kedzie Ave & Palmer Ct" ...
    ##  $ member_casual     : chr [1:1130941] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...

``` r
summary(all_trips)
```

    ##    ride_id            started_at                    
    ##  Length:1130941     Min.   :2019-10-01 00:01:39.00  
    ##  Class :character   1st Qu.:2019-10-22 07:43:06.00  
    ##  Mode  :character   Median :2019-12-03 23:35:50.00  
    ##                     Mean   :2019-12-13 04:03:06.08  
    ##                     3rd Qu.:2020-02-01 08:29:15.00  
    ##                     Max.   :2020-03-31 23:51:34.00  
    ##                                                     
    ##     ended_at                      rideable_type      start_station_id
    ##  Min.   :2019-10-01 00:06:34.00   Length:1130941     Min.   :  2.0   
    ##  1st Qu.:2019-10-22 07:55:12.00   Class :character   1st Qu.: 77.0   
    ##  Median :2019-12-04 00:20:41.00   Mode  :character   Median :174.0   
    ##  Mean   :2019-12-13 04:23:47.42                      Mean   :206.2   
    ##  3rd Qu.:2020-02-01 08:49:25.00                      3rd Qu.:294.0   
    ##  Max.   :2020-05-19 20:10:34.00                      Max.   :675.0   
    ##                                                                      
    ##  start_station_name end_station_id end_station_name   member_casual     
    ##  Length:1130941     Min.   :  2    Length:1130941     Length:1130941    
    ##  Class :character   1st Qu.: 77    Class :character   Class :character  
    ##  Mode  :character   Median :174    Mode  :character   Mode  :character  
    ##                     Mean   :206                                         
    ##                     3rd Qu.:294                                         
    ##                     Max.   :675                                         
    ##                     NA's   :1

## How many observations fall under each usertype

``` r
table(all_trips$member_casual)
```

    ## 
    ##     casual   Customer     member Subscriber 
    ##      48480     106194     378407     597860

## Reassign to the new values

``` r
all_trips <-  all_trips %>% 
  mutate(member_casual = recode(member_casual, "Subscriber" = "member", "Customer" = "casual"))
```

## Check to make sure the proper number of observations were reassigned

``` r
table(all_trips$member_casual)
```

    ## 
    ## casual member 
    ## 154674 976267

## Add columns that list the date, month, day, and year of each ride

``` r
all_trips$date <- as.Date(all_trips$started_at)
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```

## Add a “ride_length” calculation to all_trips (in seconds)

``` r
all_trips$ride_length <- difftime(all_trips$ended_at,all_trips$started_at)
```

## Inspect the structure of the columns

``` r
str(all_trips)
```

    ## tibble [1,130,941 × 15] (S3: tbl_df/tbl/data.frame)
    ##  $ ride_id           : chr [1:1130941] "25223640" "25223641" "25223642" "25223643" ...
    ##  $ started_at        : POSIXct[1:1130941], format: "2019-10-01 00:01:39" "2019-10-01 00:02:16" ...
    ##  $ ended_at          : POSIXct[1:1130941], format: "2019-10-01 00:17:20" "2019-10-01 00:06:34" ...
    ##  $ rideable_type     : chr [1:1130941] "2215" "6328" "3003" "3275" ...
    ##  $ start_station_id  : num [1:1130941] 20 19 84 313 210 156 84 156 156 336 ...
    ##  $ start_station_name: chr [1:1130941] "Sheffield Ave & Kingsbury St" "Throop (Loomis) St & Taylor St" "Milwaukee Ave & Grand Ave" "Lakeview Ave & Fullerton Pkwy" ...
    ##  $ end_station_id    : num [1:1130941] 309 241 199 290 382 226 142 463 463 336 ...
    ##  $ end_station_name  : chr [1:1130941] "Leavitt St & Armitage Ave" "Morgan St & Polk St" "Wabash Ave & Grand Ave" "Kedzie Ave & Palmer Ct" ...
    ##  $ member_casual     : chr [1:1130941] "member" "member" "member" "member" ...
    ##  $ date              : Date[1:1130941], format: "2019-10-01" "2019-10-01" ...
    ##  $ month             : chr [1:1130941] "10" "10" "10" "10" ...
    ##  $ day               : chr [1:1130941] "01" "01" "01" "01" ...
    ##  $ year              : chr [1:1130941] "2019" "2019" "2019" "2019" ...
    ##  $ day_of_week       : chr [1:1130941] "Tuesday" "Tuesday" "Tuesday" "Tuesday" ...
    ##  $ ride_length       : 'difftime' num [1:1130941] 941 258 851 2351 ...
    ##   ..- attr(*, "units")= chr "secs"

## Convert “ride_length” from Factor to numeric

``` r
is.factor(all_trips$ride_length)
```

    ## [1] FALSE

``` r
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```

    ## [1] TRUE

## We will create a new version of the dataframe (v2)

``` r
all_trips_v2 <- all_trips[!(all_trips$start_station_name == "HQ QR" | all_trips$ride_length<0),]
```

## Descriptive analysis on ride_length (figures in seconds)

``` r
mean(all_trips_v2$ride_length)
```

    ## [1] 1245.514

``` r
median(all_trips_v2$ride_length)
```

    ## [1] 574

``` r
max(all_trips_v2$ride_length)
```

    ## [1] 9387024

``` r
min(all_trips_v2$ride_length)
```

    ## [1] 1

## Compare members and casual users

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
```

    ##   all_trips_v2$member_casual all_trips_v2$ride_length
    ## 1                     casual                4425.1888
    ## 2                     member                 754.0262

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
```

    ##   all_trips_v2$member_casual all_trips_v2$ride_length
    ## 1                     casual                     1314
    ## 2                     member                      523

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
```

    ##   all_trips_v2$member_casual all_trips_v2$ride_length
    ## 1                     casual                  9387024
    ## 2                     member                  5627611

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)
```

    ##   all_trips_v2$member_casual all_trips_v2$ride_length
    ## 1                     casual                        2
    ## 2                     member                        1

## Average ride time by each day for members vs casual users

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```

    ##    all_trips_v2$member_casual all_trips_v2$day_of_week all_trips_v2$ride_length
    ## 1                      casual                   Friday                4880.5070
    ## 2                      member                   Friday                 723.4151
    ## 3                      casual                   Monday                3387.8809
    ## 4                      member                   Monday                 733.7172
    ## 5                      casual                 Saturday                4270.0765
    ## 6                      member                 Saturday                 920.0550
    ## 7                      casual                   Sunday                4456.7825
    ## 8                      member                   Sunday                 847.5988
    ## 9                      casual                 Thursday                4791.8870
    ## 10                     member                 Thursday                 708.9436
    ## 11                     casual                  Tuesday                4591.5132
    ## 12                     member                  Tuesday                 747.8537
    ## 13                     casual                Wednesday                4647.2634
    ## 14                     member                Wednesday                 717.0983

## Correctly order the days of the week

``` r
all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```

## Average ride time by each day for members vs casual users

``` r
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```

    ##    all_trips_v2$member_casual all_trips_v2$day_of_week all_trips_v2$ride_length
    ## 1                      casual                   Sunday                4456.7825
    ## 2                      member                   Sunday                 847.5988
    ## 3                      casual                   Monday                3387.8809
    ## 4                      member                   Monday                 733.7172
    ## 5                      casual                  Tuesday                4591.5132
    ## 6                      member                  Tuesday                 747.8537
    ## 7                      casual                Wednesday                4647.2634
    ## 8                      member                Wednesday                 717.0983
    ## 9                      casual                 Thursday                4791.8870
    ## 10                     member                 Thursday                 708.9436
    ## 11                     casual                   Friday                4880.5070
    ## 12                     member                   Friday                 723.4151
    ## 13                     casual                 Saturday                4270.0765
    ## 14                     member                 Saturday                 920.0550

## Analyze ridership data by type and weekday

``` r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)
```

    ## `summarise()` has grouped output by 'member_casual'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 14 × 4
    ## # Groups:   member_casual [2]
    ##    member_casual weekday number_of_rides average_duration
    ##    <chr>         <ord>             <int>            <dbl>
    ##  1 casual        Sun               40213            4457.
    ##  2 casual        Mon               16489            3388.
    ##  3 casual        Tue               16624            4592.
    ##  4 casual        Wed               15428            4647.
    ##  5 casual        Thu               16433            4792.
    ##  6 casual        Fri               17541            4881.
    ##  7 casual        Sat               28174            4270.
    ##  8 member        Sun               90048             848.
    ##  9 member        Mon              162346             734.
    ## 10 member        Tue              179475             748.
    ## 11 member        Wed              158589             717.
    ## 12 member        Thu              158211             709.
    ## 13 member        Fri              146556             723.
    ## 14 member        Sat               81034             920.

## Visualize the number of rides by rider type

``` r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")
```

    ## `summarise()` has grouped output by 'member_casual'. You can override using the
    ## `.groups` argument.

![](R-code-for-Cyclistic-Custoemr-Analysis_files/figure-gfm/unnamed-chunk-25-1.png)<!-- -->

## Let’s create a visualization for average duration

``` r
all_trips_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")
```

    ## `summarise()` has grouped output by 'member_casual'. You can override using the
    ## `.groups` argument.

![](R-code-for-Cyclistic-Custoemr-Analysis_files/figure-gfm/unnamed-chunk-26-1.png)<!-- -->

``` r
counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
write.csv(counts, file = 'avg_ride_length.csv')
```
