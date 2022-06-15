# GoogleDataAnalyticsCapstoneProject

This repository is the project capstone of the google data analytics certificate program


# Introduction

Cyclistic (a fictional comapny) launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a  network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders or Customers. Customers who purchase annual memberships are Cyclistic members or Subscribers.

The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.


# Business Task

The 2019 cyclistic data will be analysed to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, a new marketing strategy will be designed to convert casual riders into annual members.


# Preparing the Data

The cyclistic data from 2019 will be used in this particular project. For the purposes of this case study, the datasets are appropriate and will enable you to answer the business questions. The data has been made available by Motivate International Inc. under this[license](https://www.divvybikes.com/data-license-agreement). This is public data that you can use to explore how different customer types are using Cyclistic bikes. But note that data-privacy issues prohibit you from using riders’ personally identifiable information.

The data is in four (4) separate csv files one for each quater of the year. The files are checked to ensure all the data are same for all the files. The columns are same in each file as described as follows;

trip_id: this is a unique ID for each rider

start_time: this is a datetime of when the rider took the bike

end_time: this is a datetime of when the rider returned the bike

bikeid: the unique ID for the bike used on a particular trip

tripduration: the time a rider spends on a single trip in seconds

from_station_name: this is the name of the station where the rider returned the bike

from_station_id: this is the id of the start station

to_station_name: this is the name of the end station

to_station_id: this is the id of the end station

usertype: this is the membership status of the rider, either a Subscriber or a Customer

gender: the gender of the rider

birthyear: the birth year of the rider in the format yyyy


# Processing The Data

The data processing is done with sql in the bigquery sandbox console. The four (4) table files for the 1st, 2nd, 3rd and 4th quarters are uploaded into the bigquery consoles as Q1_2019, Q2_2019, Q3_2019 and Q4_2019 respectively into the the dataset named "cyclistic" under the project "hands-344017".

To get all tables into one table which will contain everything, a new table called "full_2019" is created and the table for the 1st quater (Q1_2019) is inserted first in the creation of the data.

`create table hands-344017.Cyclist.full_2019 as
select *
from hands-344017.Cyclist.Q1_2019`

This process of joining all tables is possible because data in all tables are the same. Data for the remaining tables are added into the "full_2019" table

*for 2nd quarter*

`insert into hands-344017.Cyclist.full_2019 (
  trip_id,	
start_time,	
end_time,	
bikeid,	
tripduration,	
from_station_id,	
from_station_name,	
to_station_id,	
to_station_name,	
usertype,	
gender,	
birthyear)
select * 
FROM hands-344017.Cyclist.Q2_2019`

*for 3rd quarter*

`insert into hands-344017.Cyclist.full_2019 (
  trip_id,	
start_time,	
end_time,	
bikeid,	
tripduration,	
from_station_id,	
from_station_name,	
to_station_id,	
to_station_name,	
usertype,	
gender,	
birthyear)
select * 
FROM hands-344017.Cyclist.Q3_2019`

*for 4th quarter*

`insert into hands-344017.Cyclist.full_2019 (
  trip_id,	
start_time,	
end_time,	
bikeid,	
tripduration,	
from_station_id,	
from_station_name,	
to_station_id,	
to_station_name,	
usertype,	
gender,	
birthyear)
select * 
FROM hands-344017.Cyclist.Q4_2019`

The nulls in the "full_2019" table will have to be cleared but before then, so as not to loose the original, a duplicate of the table is created as "dropnull_cyclist". This new table is what will be used for the analysis.

`create table hands-344017.Cyclist.dropnull_cyclist as
select *
from hands-344017.Cyclist.full_2019`

The usertype column is checked to make sure it only contain two distinct inputs

 `select distinct usertype
from hands-344017.Cyclist.dropnull_cyclist`

![distinct_username](https://user-images.githubusercontent.com/107520777/173951885-71a9b49f-ba35-47ff-9098-01badbee511a.PNG)
