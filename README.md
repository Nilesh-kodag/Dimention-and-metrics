# Dimention-and-metrics
Implement Ga3 Metrics to understand Data ( data analytics big query)




UA (GA3)- Dimension & Metrics
The most important user dimensions and metrics for GA360 BigQuery export, like user type and users.

Check Return Users and New User
Note: Why Count (1)
COUNT(1) gives the total number of records in the table including null values.

SELECT
case
when totals.newVisits =1 then 'newVisits' else  'Return user' end as user_type,count(1)
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` GROUP BY user_type
LIMIT 1000
What is Visit number?
The numerical identifier of the visit

SELECT
distinct (visitId),
count (visitNumber) as count_session
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
group by 1
LIMIT 1000
Count number of visitor complete sessions

SELECT
visitNumber as count_session
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
Now i want to understand user

SELECT
DISTINCT (fullVisitorId) as users
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
Want to check new user

SELECT distinct (case  when totals.newVisits =1 then fullVisitorId else null end )as new_users
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
New session with percentage

new session with percentage
SELECT
count (distinct (case  when totals.newVisits =1 then fullVisitorId else null end ))/count (distinct(concat(fullVisitorId, cast(visitstarttime as string)))) as perc
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
_____
SELECT
visitstarttime
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
_____
SELECT
distinct(concat(fullVisitorId, cast(visitstarttime as string))) as fullvisit_time
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
SELECT
count(distinct concat(fullvisitorid, cast(visitstarttime as string))) / count(distinct fullvisitorid) as number_of_sessions_per_user,
-- hits (metric)
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
____
SELECT
sum((select totals.hits from unnest(hits) group by totals.hits)) as hits
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
LIMIT 1000
Combine all

select
-- user type (dimension)
case when totals.newvisits = 1 then 'New visitor' else 'Returning visitor' end as user_type,
-- count of sessions (dimension)
visitnumber as count_of_sessions,
-- users (metric)
count(distinct fullvisitorid) as users,
-- new users (metric)
count(distinct(case when totals.newvisits = 1 then fullvisitorid else null end)) as new_users,
-- % new sessions (metric)
count(distinct(case when totals.newvisits = 1 then fullvisitorid else null end)) / count(distinct concat(fullvisitorid, cast(visitstarttime as string))) as percentage_new_sessions,
-- number of sessions per user (metric)
count(distinct concat(fullvisitorid, cast(visitstarttime as string))) / count(distinct fullvisitorid) as number_of_sessions_per_user,
-- hits (metric)
sum((select totals.hits from unnest(hits) group by totals.hits)) as hits
from
`bigquery-public-data.google_analytics_sample.ga_sessions_20160801`
group by
user_type,
count_of_sessions
order by
count_of_sessions


