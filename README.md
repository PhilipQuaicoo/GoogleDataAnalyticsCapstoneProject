# Google Data Analytics Capstone Project

This repository is the project capstone of the google data analytics certificate program


# Introduction

Cyclistic (a fictional comapny) launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a  network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders or Customers. Customers who purchase annual memberships are Cyclistic members or Subscribers.

The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.


# Business Task

The 2019 cyclistic data will be analysed to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, a new marketing strategy will be designed to convert casual riders (Customers) into annual members (Subscribers).


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

```
create table hands-344017.Cyclist.full_2019 as
select *
from hands-344017.Cyclist.Q1_2019
```

This process of joining all tables is possible because data in all tables are the same. Data for the remaining tables are added into the "full_2019" table

*for 2nd quarter*

```
insert into hands-344017.Cyclist.full_2019 (
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
FROM hands-344017.Cyclist.Q2_2019
```

*for 3rd quarter*

```
insert into hands-344017.Cyclist.full_2019 (
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
FROM hands-344017.Cyclist.Q3_2019
```

*for 4th quarter*

```
insert into hands-344017.Cyclist.full_2019 (
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
FROM hands-344017.Cyclist.Q4_2019
```

*NB: Nulls will not be dropped through the table since a "not null" will be used in the "Where" statement to affect all tables when required.*

The usertype column is checked to make sure it only contain two distinct inputs

 ```
 select distinct usertype
from hands-344017.Cyclist.full_2019
```

![distinct_username](https://user-images.githubusercontent.com/107520777/173951885-71a9b49f-ba35-47ff-9098-01badbee511a.PNG)


# Descriptive Analysis

***the total number of trips in 2019 per the table schema is 3,818,004 of which the table gives the number of rides per the various usertype using the query***

![usertypenumtrips](https://user-images.githubusercontent.com/107520777/174672998-2dd305b5-1882-4345-883f-b1c4e4f10274.PNG)

```
select count(trip_id) as num_of_trip, usertype
from hands-344017.Cyclist.full_2019
group by usertype
```

***the maximumn, minimum and mean trip duration is calculated for the number of trips and trips for the vatious usertypes***

*total*

```
select max(tripduration) as Maximum, min(tripduration) as Minimum, avg(tripduration) as Mean
from hands-344017.Cyclist.full_2019
```

All values in seconds

![totalMMM](https://user-images.githubusercontent.com/107520777/174689277-5016433d-c4e6-475f-b4ed-957d1a437a31.PNG)

*Customers*

```
select max(tripduration) as Maximum, min(tripduration) as Minimum, avg(tripduration) as Mean
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
```

All values in seconds

![customerMMM](https://user-images.githubusercontent.com/107520777/174689310-d2964dbe-62b0-4871-acf7-0fc164c6aa7f.PNG)

*Subscribers*

```
select max(tripduration) as Maximum, min(tripduration) as Minimum, avg(tripduration) as Mean
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
```

All values in seconds

![SubscriberMMM](https://user-images.githubusercontent.com/107520777/174689334-73b19fd7-242a-486a-884e-70477ddd8384.PNG)



>Nulls were not dropped from this data because the analysis was more trip_id based and so we didnt want to risk misrepresenting the numbers by dropping some rows. The count of rides will still be neccesary even if there was values missing in some columns of a particular trip_id.

***The following queries are to select the count of rides ordered by Customers and Subscribers for each particular day of the week, for months in the year and the hours in a day. The returned tables will be plotted to visualise the trend in bike rides as they are grouped.***

*days of the week, Customers*

```
select FORMAT_DATE("%A", start_time) as Day_of_Week, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by FORMAT_DATE("%A", start_time)
```

*days of the week, Subscribers*

```
select FORMAT_DATE("%A", start_time) as Day_of_Week, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by FORMAT_DATE("%A", start_time)
```


*months of the year, Customers*

```
select FORMAT_DATE("%B", start_time) as month_of_year, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by FORMAT_DATE("%B", start_time)
```

*months of the year, Subscriber*

```
select FORMAT_DATE("%B", start_time) as month_of_year, count(trip_id) as Number_of_Trips
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by FORMAT_DATE("%B", start_time)
```


*for hours in a day, Customer*

```
select extract(hour from start_time) as Hours, count(trip_id) as Trips_by_Hour
FROM hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by extract(hour from start_time)
```

*for hours in a day, Subscriber*

```
select extract(hour from start_time) as Hours, count(trip_id) as Trips_by_Hour
FROM hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by extract(hour from start_time)
```



***The next set of queries provide statistical insight on where various usetypes starts and end their rides***

*both usertypes rides per start station name*

===Top 5===

```
select count(trip_id) as from_station_rides, from_station_name
from hands-344017.Cyclist.full_2019
group by from_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as from_station_rides, from_station_name
from hands-344017.Cyclist.full_2019
group by from_station_name
order by count(trip_id)
limit 5
```

*Customer rides per start station name*

===Top 5===

```
select count(trip_id) as from_station_rides_Cus, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by from_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as from_station_rides_Cus, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by from_station_name
order by count(trip_id)
limit 5
```

*Subscriber rides per start station name*

===Top 5===

```
select count(trip_id) as from_station_rides_Sub, from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by from_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as from_station_rides_Sub from_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by from_station_name
order by count(trip_id)
limit 5
```


*total rides per end station name*

===Top 5===

```
select count(trip_id) as to_station_rides, to_station_name
from hands-344017.Cyclist.full_2019
group by to_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as to_station_rides, to_station_name
from hands-344017.Cyclist.full_2019
group by to_station_name
order by count(trip_id)
limit 5
```

*Customer rides per end station name*

===Top 5===

```
select count(trip_id) as to_station_rides_Cus, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by to_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as to_station_rides_Cus, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Customer"
group by to_station_name
order by count(trip_id)
limit 5
```

*Subscriber rides per end station name*

===Top 5===

```
select count(trip_id) as to_station_rides_Sub, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by to_station_name
order by count(trip_id) desc
limit 5
```

===Bottom 5===

```
select count(trip_id) as to_station_rides_Sub, to_station_name
from hands-344017.Cyclist.full_2019
where usertype = "Subscriber"
group by to_station_name
order by count(trip_id)
limit 5
```


# Visualisation of Data

### Google sheets was used to plot various graphs of the exported tables to visualize trands in how various usertypes patronize the services.

The comparison is between Customer and Subscriber usertypes and this Pie Chart visualises the fraction they each represent in the entire dataset.

![usertypenumtrips](https://user-images.githubusercontent.com/107520777/174813940-79306bd0-1ba2-4383-bfa4-1fdea96afdc7.PNG)


![USERTYPE TRIP SUMMARY, 2019](https://user-images.githubusercontent.com/107520777/174811473-e5982264-2030-4ae7-8c3d-c994de2ba965.png)


***Comparison was based on various usertypes as against days of the week, months in the year 2019 and hours in a day.***

*The days of the week*

![TRIPS ON WEKDAYS, 2019](https://user-images.githubusercontent.com/107520777/175179846-0352baf4-e56a-4382-9556-03f56842247b.png)

*Months in the year, 2019*

![TRIPS PER MONTH, 2019](https://user-images.githubusercontent.com/107520777/175179954-e710a6ff-fed1-4972-ac8b-8fec90204897.png)

*Hours in a day*

![TRIP BY HOURS OF THE DAY, 2019](https://user-images.githubusercontent.com/107520777/175179980-78a74c71-a921-474a-858f-6b1933bd82e4.png)


# Data Insights

1. Cyclistic recorded 3,818,004 rides in all, of which Subscriber rides constitutes a large fraction of 77% (2,937,367 rides), and that of Customers being 23% (880,637 rides). 
2. Comparing the data of the usertypes to the days in the week, the graph show that Subscriber group usage peaks during the working/Business days (Monday, Tuesday, Wednesday, Thursday and Friday) and dips during the weekend(Saturday and Sunday). On the contrary, the Customer group statistics shows a peak on weekends and dips during Working days.
3. Again comparing the various usertypes data to the hours in a day, the subscriber Chart shows two peaks. First, around start of business hours and second peak is seen around close of business hours. Again, there is a slight peak during lunch hours. Whiles Customer chart shows a peak value around close of business hours. Coupling this with the insight from the comparison on days of the week, activities around the hours of the day of a Subscriber can be likened to that of a Working Class individual. 
4. The graph for rides per the months shows that rides in summer are the highest whiles winter rides are relatively low. this trend is the same for all user types. This coulb be attributed to the holiday season. As observed from the trend of ride numbers by the hour of the day, majority of rides which belongs to the subscriber user type group have a trend that suggest its users being working class individuals. Should the working class mass go on holidays, this will cause the numbers to drop for those days.


# The Way Foreward










