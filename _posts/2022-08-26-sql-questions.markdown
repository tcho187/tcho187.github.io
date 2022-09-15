---
layout: post
title: SQL questions
tags: [frontpage, jekyll, blog]
image: '/images/posts/7.jpg'
---

# Question 1

Assume you have the below events table on app analytics. Write a query to get the
clickthrough rate per app in 2019.

events
app_id
event_id
timestamp

```
select app_id, sum(case when event_id = 'click' then 1 else 0 end)/sum(case when event_id = 'impression' then 1 else 0 end) as ctr
from events
where extract('year',timestamp) = 2019
group by 1
```



# Question 2
Assume you are given the tables below containing information on trades
and users. Write a query to list the top three cities that had
the most number of completed orders.

trades
order_id
user_id
price
quantity
status
timestamp

users
user_id
city
email
signup_date

```
select city, count(distinct order_id) as completed_orders
from trades t
join users u
on t.user_id = u.user_id
where status = 'complete'
group by 1
order by 2 desc
limit 3
```


#Question 3
Assume that you are given the table below containing information on viewership
by device type (where the three types are laptop, tablet, and phone)
Define "mobile" as the sum of tablet and phone viewership numbers
Write a query to compare the viewership on laptops versus mobile devices.

viewership
user_id
device_type
view_time

```
select
sum(case when device_type in ('phone','tablet') then 1 else 0 end) as mobile,
sum(case when device_type = 'laptop' then 1 else 0 end) as laptop
from viewership
```


#Question 4
Assume you are given the table below for spending activity
by product type. Write a query to calculate the cumulative spend so far by date for each product over time in chronical order.

total_trans
order_id
user_id
product_id
spend
trans_date

```
select t1.trans_date,t2.product_id,sum(spend) as cum_spend
from total_trans t1
inner join total_trans t2
on t1.trans_date = t2.trans_date
and t1.product_id >= t2.product_id
group by 1,2
order by 1,2
```

#Question 5

Assume that you are given the table below containing
information on various orders made by customers.
Write a query to obtain the names of the ten customers who have
ordered the highest number of products among those customers who have
spent at least $1000 total.

user_transactions
transaction_id
product_id
user_id
spend
trans_date

```
select user_id, count(product_id) as num_products
from user_transactions
where user_id in
(select user_id, sum(spend) as total_spend
from user_transactions
group by user_id
having total_spend >=1000)
group by 1
order by 2
limit 10
```


#Question 6
Assume you are given the table below containing information on tweets. Write a
query to obtain a histogram of tweets posted per user in 2020.

tweets
tweet_id
user_id
msg
tweet_date

select num_messages, count(*) as cnt
from (
select user_id, count(tweet_id) as num_messages
from tweets
where extract('year' from tweet_date) = 2020
group by 1
) t1
group by 1


#Question 7
Assume you are given the table below containing information on user purchases.
Write a query to obntain the number of people who purchased at least one
or more of the same product on multiple days.

purchases
purchase_id
user_id
product_id
quantity
price
purchase_time
```
select count(distinct user_id) as num_people
from
(
select user_id,product_id,count(distinct extract('day' from purchase_time) as date) as days
from purchases
group by 1,2
having days > 1
) t1
```
#Question 8
Assume you are given the table below that shows the job postings for all companies
on the platform. Write a query to get the total number
of companies that have posted duplicate job listings
(two jobs at the same company with the same title
and description)

job_listings
job_id
company_id
title
description
post_date

```
select count(distinct company_id) as companies from
(
select company_id,unit
from (
select concat_ws("|",title,description) as unit
from job_listings
) t1
group by 1,2
having count(unit) > 1
) t2
```


#Question 9
Assume you are given the table below in user transactions.
Write a query to obtain the list of customers whose first transaction was
valued at $50 or more.

user_transactions
transaction_id
product_id
user_id
spend
transaction_date

```
with first as (
select user_id,min(transaction_date) as first_date
from user_transactions
group by 1
)
select user_id
from user_transactions t1
join first t2
on t1.user_id = t2.user_id
and t1.transaction_date = t2.first_date
where spend >= 50
```


#Question 10
Assume you are given the table below containing information on each user's tweets over a period of time. Calculate the
7-day rolling average of tweets by each user for every date.

tweets
tweet_id
msg
user_id
tweet_date

```
with base as (
select user_id,tweet_date, count(*) as cnt
from tweets
group by 1,2
)
select user_id,t1.tweet_date as date, sum(t2.cnt)
from base t1
inner join base t2
on t1.tweet_date >= t2.tweet_date - interval 7 day
and t1.user_id = t2.user_id
```

#Question 11

Assume you are given the table below on transactiosn made by users. Write
a query to obtain the third transaction of every user.


transactions
user_id
spend
transaction_date

```
select user_id,spend,transaction_id from
(
select t.*, row_number() over (partition by user_id order by transaction_date) as rk
from transactions t
) t1
where rk=3
```

#Question 12

Assume you are given the table below containing information on customer spend on
products belonging to various categories. Identify the top three highest-grossing items
within each category in 2020.

product_spend
transaction_id
category_id
product_id
user_id
spend
transaction_date

```
select category_id,product_id, rk
from (
select category_id, product_id, row_number() over (partition by category_id, product_id order by sum(spend) desc) as rk
from product_spend
where transaction_date >= '2020-01-01' and transaction_date < '2020-12-31'
) t1
where rk <=3
order by 1,3
```


#Question 13

Assume you are given the below table on transactions from users.
Bucketing users based on their latest transaction date,
write a query to obtain the number of users who made
a purchase and the total number of products bought
for each transaction date.

user_transactions
transaction_id
product_id
user_id
spend
transaction_date


```
with base as (
select t.*
from (
select t.*,
rank() over (partition by user_id order by transaction_date desc) as rk
from
user_transactions t
) t
where rk = 1
)

select transaction_date,
count(distinct user_id) as users,
count(product_id) as products
from base
group by 1
order by 1
)
```


#Question 22
Assume you are given the two tables below
containing information on the topics that each
Twitter user follows and the ranks of each of these topics
Write a query to obtain all existing users
on 2021-01-01 that did not follow any topic in the
100 most popular topics for that day.

user_topics
user_id
topic_id
follow_date

topic_rankings
topic_id
ranking
ranking_date

```
select user_id
from user_topics
where follow_date = '2021-01-01'
and topic_id not in
(
select topic_id
from topic_rankings
where ranking_date = '2021-01-01'
and ranking <=100
)
```


#Question 23

Assume you have the tables below containing information
on user actions. Write a query to obtain active user retention by month.
An active user is defined as someone who took
an action (sign-in,like, or comment) in the current month.


user_actions
user_id
event_id
timestamp


```
with base as
(
select *, to_char('YYYY-MM',timestamp) as yrmo
from
),
active as
(
select yrmo, count(distinct user_id) as active_users
from base
where user_action in ('sign-in','like', 'comment')
group by yrmo
),
all as (
select yrmo, count(distinct user_id) as all_users
from base
group by yrmo
)

select active.active_users/all.all_users as mau
from all
join active
on all.yrmo = active.yrmo
```


#Question 24

Assume you are given the tabels below containing information
user sessin activity. Write a query that ranks users according
to their total session durations for each session type
between the start date (2021-01-01) and the end date (2021-02-01)

sessions
session_id
user_id
session_type
duration
start_time

```
with user_duration as (
select user_id,
session_type,
sum(duration) as duration
from sessions
where start_time between '2021-01-01' and '2021-02-01'
group by 1,2
)

select user_id, session_type, rank() over (partition by user_id, session_type, order by duration desc) as rk
from user_duration
order by session_type, rank desc
```



#Question 25

Assume you are given the tables below on Snachat users and their time spent sending and
opening snaps. Write a query to obtain a breakdown of the time
spent sending vs opening snaps (as a percentage of total time spent) for each different age groups


activites
activity_id
user_id
type
time_spent
activity_date


age_breakdown
user_id
age_bucket


select t2.age_bucket, sum(time_spent where type='send')/sum(time_spent)*100.0 as sending,
sum(time_spent where type='open')/sum(time_spent)*100.0 as opening
from activities t1
inner join on age_breakdown t2
on t1.user_id = t2.user_id
group by 1


#Question 26

Assume you are given th table below containing information on user sessions,
including their start and end times. A session is considered to be concurrent with another user's session if they overlap.
Write a query to obtain the user sessions that is
concurrent with the largest number of other user sessions.


sessions
session_id
start_time
end_time

```
select t1.session_id, count(t2.session_id) as concurrent
from sessions t1
inner join sessions t2
on t1.end_time <= t2.start_time
group by 1
order by concurrent desc
limit 1
```


#Question 27

Assume you are given the table below containing information on user reviews.
Define a top rated business as one whose reviews contain only
4 or 5 stars. Write a query to obtain the number and percentage of businesses that are top rated.


reviews
business_id
user_id
review_text
review_stars
review_date


```
with top_rated as (
select count(distinct business_id) as top_rated_businesses
from (
select business_id
from reviews
group by 1
having min(review_stars) >=4
)
 t1
)

select * from top_rated, (select * from top_rataed/(select count(distinct business_id) from reviews)
from reviews
)
```


#Question 28

Assume you are given the table below containing measurement values obtained from
a sensor over several days. Measurements are taken several times within a give day. Write a
query to obtain the sum of the odd numbered measurements and the sum of the even numbered
measurments by date.

measurements
measurement_id
measurement_value
measurement_time


```
select(measurement_time::date) as dt,
sum(measurement_value where mod(measurement_value,2)=0) as even,
sum(measurement_value where mod(measurement_value,2)=1) as odd
from measurements
group by 1
```

```
with measurements_by_count as (
select measurement_time::date as measurement_day,
measurement_value,
row_number() over (partition by meaasurement_time::date
order by measurement_time asc) as measurement_count
from measurements
)

select measurement_day,
sum(measurement_count where mod(measurement_count,2)=0) as even,
sum(measurement_count where mod(measurement_count,2)=0) as odd
from measurements_by_count
group by 1
order by 1
```

#Question 29

Assume you are given the two tables below containing information on
user signups a d user purchases. Of the users who joined within the past week,
write a query to obtain the percentage of users that also purchased at least one item.

signups
user_id
signup_date

user_purchases
user_id
product_id
purchase_amount
purchase_date

```
from base as (
select user_id
from signups
where signup_date >= (CURRENT_DATE - INTERVAL '7 day')
),
purchased as (
select user_id
from user_purchases
where purchase_date >= (CURRENT_DATE - INTERVAL '7 day')
)

select (select count(distinct user_id) from purchased)/select count(distinct user_id) from base)
from signups
```

```
select
count(distinct p.user_id)/count(distinct s.user_id) * 100.0 as last_week_pct
from signups s
left join user_purchases p
on p.user_id = s.user_id
where s.signup_date > now() - interval '7 day'
```


#Question 30

Assume you are given the following tables on customer transactions
and products. Find the top 10 products that are most frequently bought together (purchased
in the same transaction).


transactions
transaction_id
product_id
user_id
quantity
transaction_time

products
product_id
product_name
price

```
select t1.product_id, t2.product_id, count(distinct transaction_id) as purchases
from transactions t1
join transactions t2
on t1.transaction_id = t2.transaction_id
where t1.product_id != t2.product_id
group by 1
order by purchases desc
```


#Question 31

Write a query to obtain the number of reactivated users
(ie those who didn't log in the previous month, who then logged
in during the current month)

user_logins
user_id
login_date

with first as (
select user_id, min(login_date) as first
from user_logins
),
base as (

)


#Question 32

Write a query to obtain the year on year growth rate
for the total spend of each product for each week

user_transactions
transaction_id
product_id
user_id
spend
transaction_date

```
with weekly as (
select extract(year from transaction_date) as year,
extract(week from transaction_date) as week,
product_id
sum(spend) as total_spend
from user_transactions
group by ,1,2,3
order by 3,1,2
)

select (t1.total_spend/t2.total_spend)-1 as yoy
from weekly t1
join weekly t2
on t1.year = t2.year - interval 1 year
and t1.week = t2.week
and t1.product_id = t2.product_id
```


#Question 33

Write a query to obtain the account's rolling 7 day earnings.

user_transactions
transaction_id
user_id
amount
transaction


```
with daily as (
select transaction_date::date as dt,
// another way to write the date is CAST(transaction_date as DATE)
sum(amount) as daily_amount
from user_transactions
group by 1
order by 1
)

select t1.dt,
sum(t2.daily_amount) as rolling_7
from daily t1
join daily t2
on t1.dt > t2.dt - interval 7 day
group by 1
```
















