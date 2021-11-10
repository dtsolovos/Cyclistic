# Cyclistic
Capstone project for the Google Data Analytics Certificate on Coursera

## About
For the purpose of this project I will be working as a data analyst for the marketing analyst team at Cyclistic, a fictional bike-share company in Chicago with a fleet of 5,824 bikes and 692 docking stations. The company's marketing director, Lily Moreno, believes that future success lies in maximizing the number of annual memberships. As such, my team is tasked with understanding how casual clients and annual members behave differently and using the resulting insights to design a new marketing strategy to convert casual riders to annual members.

The company offers three pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders, while customers who purchase annual memberships are Cyclistic members. Up until now, Cyclistic's marketing strategy relied on building general awareness and appealing to broad consumer insights, an approach which was helped by the flexibility of the company's pricing plans.

Cyclistic’s finance analysts have concluded that Cyclistic members are considerably more profitable than casual riders, which is why the marketing director believes that converting casual riders to annual members is key for future growth. Rather than creating another marketing campaign which will target new customers, Ms. Moreno believes that there is a very good chance to convert casual riders into members, especially since casual riders are already familiar with and have chosen Cyclistic for their mobility needs.

My team's goal is to design marketing strategies to convert casual riders into annual members and my own task is to understand how casual riders and Cyclistic members behave differently.

## Data
While Cyclistic is a fictional company, the data I will be using is from Divvy, Chicago city's bike share program. The data is collected by sensors on the docking stations and produce the following variables:

* ride_id: unique id of a single trip (string)
* rideable_type: type of bike used (string)
* started_at: date and time the bike was undocked (datetime)
* ended_at: date and time the bike was docked (datetime)
* start_station_name: location of start docking station (string)
* start_station_id: id of start docking station (string)
* end_station_name: location of end docking station (string)
* end_station_id: id of end docking station (string)
* start_lat: start docking station latitude (numeric)
* start_lng: start docking station longitude (numeric)
* end_lat: end docking station latitude (numeric)
* end_lng: end docking station longitude (numeric)
* member_casual: rider type (string)

The data is published as CSV files and can be found at [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com/index.html).

# Data Cleaning and Transformation
Given that the total number of observations is greater than five million, I decided to clean and process with R, using RStudio. 

After setting up the working directory, the first thing to do was to setup the environment. For the purpose of this project, I needed the ```tidyverse``` and ```lubridate``` packages:

```
library(tidyverse)
library(lubridate)
```

My analysis will cover the period from October 2020 to September 2021, so I downloaded and imported the relevant CSV files in RStudio:

```
oct_2020 <- read_csv("CSV\\202010-divvy-tripdata.csv")
nov_2020 <- read_csv("CSV\\202011-divvy-tripdata.csv")
dec_2020 <- read_csv("CSV\\202012-divvy-tripdata.csv")
jan_2021 <- read_csv("CSV\\202101-divvy-tripdata.csv") 
feb_2021 <- read_csv("CSV\\202102-divvy-tripdata.csv") 
mar_2021 <- read_csv("CSV\\202103-divvy-tripdata.csv")
apr_2021 <- read_csv("CSV\\202104-divvy-tripdata.csv")
may_2021 <- read_csv("CSV\\202105-divvy-tripdata.csv")
jun_2021 <- read_csv("CSV\\202106-divvy-tripdata.csv")
jul_2021 <- read_csv("CSV\\202107-divvy-tripdata.csv")
aug_2021 <- read_csv("CSV\\202108-divvy-tripdata.csv")
sep_2021 <- read_csv("CSV\\202109-divvy-tripdata.csv") 
```

While checking the resulting tables to see if the data types were consistent, I noticed that the fields *start_station_id* and *end_station_id* in *oct_2020* and *nov_2020* were double numeric types, while in all other tables they were character types. After checking the data to see if there are any other general differences in these particular fields, I coerced the fields in *oct_2020* and *nov_2020* into character type, so that they are consistent with the other tables:

```
oct_2020$start_station_id <- as.character(oct_2020$start_station_id)
oct_2020$end_station_id <- as.character(oct_2020$end_station_id)
nov_2020$start_station_id <- as.character(nov_2020$start_station_id)
nov_2020$end_station_id <- as.character(nov_2020$end_station_id)
```

Next, I merged all the tables into a single one:

```
cyc <- rbind(oct_2020, nov_2020, dec_2020, jan_2021, feb_2021, mar_2021, 
             apr_2021, may_2021, jun_2021, jul_2021, aug_2021, sep_2021)
```

After that, I had to remove some test entries and empty fields that were caused by bad sensor data:

```
cyc_filtered <- cyc %>% 
  filter(start_station_id != "TEST", 
         start_station_name != "WATSON TESTING - DIVVY") %>% 
  filter(end_station_id != "TEST", 
          end_station_name != "WATSON TESTING - DIVVY") %>% 
  drop_na()
```

Then, I created a table which contained only the fields relevant to my analysis, namely *started_at*, *ended_at* and *member_casual*:

```
cyc_clean <- cyc_filtered[c(3, 4, 13)]
cyc_clean
```

For the analysis, I needed to create a trip duration variable. Since the data type of *started_at* and *ended_at* is datetime, I used ```difftime()``` to calculate the duration of each trip. I had to account for negative durations due to faulty data, and durations of a few seconds caused by bike docking tests, so I filtered out all durations under one minute:

```
cyc_clean <- cyc_clean %>% 
  mutate(trip_length = difftime(ended_at, started_at, units = "mins")) %>% 
  filter(trip_length >= 1)
```

I also needed create day and month fields, which is where the ```lubridate``` package came in handy:

```
cyc_clean$weekday <- wday(cyc_clean$started_at, label = TRUE, abbr = TRUE)
cyc_clean$month <- month(cyc_clean$started_at, label = TRUE, abbr = TRUE)
```

# Analysis
While I could use ```ggplot2```, I decided to export the resulting tables and use Tableau to create the visualizations I needed.
In order to generate some insights, I needed to see daily, monthly and total rides for each user, as well as the average ride duration during these periods.

## Total rides and average ride duration
```
cyc_clean %>% 
  count(member_casual, name = "number_of_trips")
```
```
cyc_clean %>% 
  group_by(member_casual) %>% 
  summarize(mean(trip_length))
```  
![total & average rides](https://github.com/dtsolovos/Cyclistic/blob/main/Total%20%26%20Average%20Trip.png)

As we can see in the graph, Cyclistic members use the bikes more often than casual riders, but for significantly less time.

## Daily rides

```
cyc_clean %>% 
  group_by(member_casual) %>% 
  count(weekday, name = "number_of_trips")
```
![daily trips](https://github.com/dtsolovos/Cyclistic/blob/main/Daily%20Trips.png)

According to the graph, casual riders use the bikes much more during the weekend, while annual members use them more consistently, with a slight increase during the middle of the week.

## Average daily ride duration

```
cyc_clean %>% 
  group_by(member_casual, weekday) %>% 
  summarize(mean(trip_length))
```
![daily average](https://github.com/dtsolovos/Cyclistic/blob/main/Daily%20Average%20Trip%20Duration.png)

This graph shows that the casual riders use the bikes significantly more during the weekend, while Cyclistic members use them consistently through the week, with a slight increase during the weekend.

## Monthly rides

```
cyc_clean %>% 
  group_by(member_casual) %>% 
  count(month, name = "number_of_trips")
```
~[monthly trips](https://github.com/dtsolovos/Cyclistic/blob/main/Monthly%20Trips.png)

Both casual riders and members use the bikes considerably less during the colder years. However, while members consistently use the service more often than casual riders, the casual riders use it more during the summer months.

## Average monthly ride duration

```
cyc_clean %>% 
  group_by(member_casual, month) %>% 
  summarize(mean(trip_length))
```
![monthly average](https://github.com/dtsolovos/Cyclistic/blob/main/Monthly%20Average%20Trip%20Duration.png)

As is evident by this graph, while casual riders use the bikes longer than members, their pattern is somewhat erratic and unpredictable. As in all other graphs, Cyclistic members are consistent in their use of the bikes.

# Conclusion
The above graphs show that while Cyclistic members consistently use them each day for a short time, casual members use them more erratically. The generally ride for a considerably longer time than members, but their use of the bikes is dropping during the weekdays and sharply rises during the weekend. Also, casual riders take more trips during the summer months than annual members. All these observations suggest that Cyclistic members use the service as a mode of transportation, while casual riders mostly use it as a mode of recreation.

# Recommendations

* In order to convince casual users to become annual members, we need to convince them to see bicycles as a proper mode of transportation. This could be done by creating incentives for consistent use. An example would be presenting our bikes as an environmentally friendly alternative to cars and public transportation and create an environmental awareness program, with rewards for annual members who consistently use the service for a certain duration.

* According to my analysis, the vast majority of casual riders use the bikes during the weekend. We could create limited time offers that only last during specific weekends, in order to both target as many casual members as possible and entice them with a time incentive.

* Casual riders' use of the bikes rises sharply, to even surpass that of members,  during the summer months. Thus, the best time to launch a major marketing campaign would be from mid May to early/mid September.

























