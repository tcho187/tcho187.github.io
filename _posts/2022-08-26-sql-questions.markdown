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



