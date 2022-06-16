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

*NB: Nulls will not be dropped through the table since a "not null" will be used in the "Where" statement to affect all tables when required.*

The usertype column is checked to make sure it only contain two distinct inputs

 `select distinct usertype
from hands-344017.Cyclist.full_2019`

![distinct_username](https://user-images.githubusercontent.com/107520777/173951885-71a9b49f-ba35-47ff-9098-01badbee511a.PNG)


# Descriptive Analysis

A new table was created with extra columns created to host the extracted month(mm), day name and hour(hh) part of the "start_time" column and also to cast the "tripduration" column as integer and named as "tripduration_int". analysis will be conducted on this new table.

`create table hands-344017.Cyclist.new_full_2019 as
select *, 
cast(tripduration as int64) as triduration_int,
extract(month from start_time) as start_month,
FORMAT_DATE("%A", start_time) as start_dayname,
extract(hour from start_time) as start_hour,
from `hands-344017.Cyclist.full_2019``

***The following queries are to select the count of rides ordered for each particular day of the week and another for months in the year. The returned tables will be plotted to visualise the trend in bike rides as they are grouped.***

*days of the week*

`select FORMAT_DATE("%A", start_time) as Day_of_Week, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
group by FORMAT_DATE("%A", start_time)`

![orders_by_day](https://user-images.githubusercontent.com/107520777/174084279-59470c63-a71e-4055-bde8-e0af0147cd2e.PNG)

*for months of the year*

`select FORMAT_DATE("%B", start_time) as month_of_year, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
group by FORMAT_DATE("%B", start_time)`

![order_by_months](https://user-images.githubusercontent.com/107520777/174095342-847c8131-2ae1-4c73-b726-e99252de215a.PNG)

***The next set of queries are to select the statistics on the stations and how they relate in total and to the various usertypes***

*total rides per from_station_name*

===Top 5===

`select count(trip_id) as from_station_rides, from_station_name
from hands-344017.Cyclist.full_2019
group by from_station_name
order by count(trip_id) desc
limit 5`

![generaltopfromstation](https://user-images.githubusercontent.com/107520777/174107493-8b086bf7-01dd-414c-9fdb-1354f35c4999.PNG)

===Bottom 5===

`select count(trip_id) as from_station_rides, from_station_name
from hands-344017.Cyclist.full_2019
group by from_station_name
order by count(trip_id)
limit 5`

![generalbuttomfromstation](https://user-images.githubusercontent.com/107520777/174108499-a0462ff7-90b3-4f3d-a7bc-47f392656392.PNG)

*Customer rides per from_station_name*

===Top 5===

`select count(trip_id) as from_station_rides_Cus, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by from_station_name
order by count(trip_id) desc
limit 5`

![Customertopfromstation](https://user-images.githubusercontent.com/107520777/174115608-b12bd63d-5cbb-4ac7-8259-520c485a6dbb.PNG)


===Bottom 5===

`select count(trip_id) as from_station_rides_Cus, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by from_station_name
order by count(trip_id)
limit 5`

![Customerbottomfromstation](https://user-images.githubusercontent.com/107520777/174117443-35974586-9eec-4559-8979-1166d47c28ad.PNG)

*Subscriber rides per from_station_name*

===Top 5===

`select count(trip_id) as from_station_rides_Sub, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by from_station_name
order by count(trip_id) desc
limit 5`

![Customertopfromstation](https://user-images.githubusercontent.com/107520777/174115608-b12bd63d-5cbb-4ac7-8259-520c485a6dbb.PNG)

===Bottom 5===

`select count(trip_id) as from_station_rides_Sub from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by from_station_name
order by count(trip_id)
limit 5`

![Customerbottomfromstation](https://user-images.githubusercontent.com/107520777/174117443-35974586-9eec-4559-8979-1166d47c28ad.PNG)



*total rides per to_station_name*

===Top 5===

`select count(trip_id) as to_station_rides, to_station_name
from hands-344017.Cyclist.full_2019
group by to_station_name
order by count(trip_id) desc
limit 5`



===Bottom 5===

`select count(trip_id) as to_station_rides, to_station_name
from hands-344017.Cyclist.full_2019
group by to_station_name
order by count(trip_id)
limit 5`



*Customer rides per to_station_name*

===Top 5===

`select count(trip_id) as to_station_rides_Cus, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by to_station_name
order by count(trip_id) desc
limit 5`




===Bottom 5===

`select count(trip_id) as to_station_rides_Cus, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by to_station_name
order by count(trip_id)
limit 5`



*Subscriber rides per to_station_name*

===Top 5===

`select count(trip_id) as to_station_rides_Sub, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by to_station_name
order by count(trip_id) desc
limit 5`




===Bottom 5===

`select count(trip_id) as to_station_rides_Sub, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by to_station_name
order by count(trip_id)
limit 5`




