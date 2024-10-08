## Data sources
Both sources are fictionalized to provide analysts the opportunity to practice.

* https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2019_Q4.zip
* https://divvy-tripdata.s3.amazonaws.com/Divvy_Trips_2020_Q1.zip

## Background information
Cyclistic has determined that annual members create more income than casual day-trip riders. They now need to determine what marketing to do in order to create more annual members, which requires further analysis of the data. The end goal is to examine the data and find trends that can be converted into suggestions for the marketing and executive teams.

## Stakeholders
Lily Moreno, Director of Marketing
Cyclistic
Marketing team
Executive team

### Business Task
How do annual members and casual riders use Cyclistic bikes differently?

## Data Preparation
Two files covering q4 of 2019 and q1 of 2020 are the source data. When the structure of the two datasets are compared, two issues are discovered.

First, the table column names are renamed to make them consisten across matching information. For example, the 2019 data uses the column 'trip_id', but the 2020 data uses 'ride_id'. We can assume that the 2020 document has the correct columns as it is the most recent document, so column names in the 2019 dataset are renamed to match the 2020 dataset.

Third, the data types for ride_id and rideable_type need to be the same or we won't be able to combine them. It is changed to character.

There Were column names in the 2019 data that don't exist in the 2020 data. Again, we will assume that the 2020 has the correct columns, so the extra columns are removed from the new combined table.

Now that the data is clean, the two sets are combined together into one usable file.

## Data Cleaning
In the the member_casual column, there are four different answers, but there should only be two. Again, we have to adjust for changes made in 2020, so the data is transformed to make it consisten across the whole document.

We can only see data by individual trip, which will make broader time period analysis difficult. We extract the date, month, day and year from the currently existing date column, and new columns are created to hold the broken-down data

Start and end times exist, so we can create a trip_duration column to make analysis easier. This new column is set as a factor datatype, so we change it to numeric so any calculations will perform correctly.

Some of the data show negative ride time, which are either errors or because of maintenance. All that data is removed.

## Analysis
A basic analysis caluculating average, median, min and max is run. Then we start matching up the variables we want to test against each other.
We compared members' and casual users' ride length, the average ride time by day for members versus casual riders, the average ride time by day, average ride time by type and weekday, and rides by rider type. Along the way, the days of the week were adjusted to be in the correct order.

## Visualization
Two charts were created, and can be found in the Appendix item called 'presentation'.

## Summary
Overall, the company has more monthly members than casual users. Breaking the data down another way, the company's monthly members use the bikes more during the week and less on the weekend. However, casual riders are just the opposite - less used during the week, but an uptick over the weekends.

## Appendix
Annotated R markdown document https://github.com/twofistededitor/Cyclistic-Customer-Analysis/blob/main/R%20code%20for%20Cyclistic%20Customer%20Analysis.Rmd

Presentation https://github.com/twofistededitor/Cyclistic-Customer-Analysis/blob/main/Cyclistic%20Presentation.pptx

Changelog https://github.com/twofistededitor/Cyclistic-Customer-Analysis/blob/main/Changelog.docx
