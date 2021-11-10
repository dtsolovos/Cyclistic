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

The data can be found at [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com/index.html)
