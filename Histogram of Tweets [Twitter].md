# Histogram of Tweets [Twitter]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/sql-histogram-tweets).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

***

## Additional Information

### Relational Diagram
<p align="left">
  <img src="https://github.com/Mati-DB/SQL-Interiview-Questions/assets/170549189/8e5b7b4b-1e4c-47f1-a70e-fc3c7f3627fe"/>
</p>

### Table
<p align="left">
  <img src="https://github.com/Mati-DB/SQL-Interiview-Questions/assets/170549189/45091782-7faf-426e-98e6-712dc13ec3c2">
</p>

### Output Example

| tweet_bucket | users_num |
|-------|-------|
| 1 | 2 |
| 2 | 1 |
***

## Solution
I started by looking for the amount of tweets per ``user_id``

````sql
SELECT user_id, COUNT(*) AS tweet_count
FROM tweets
WHERE tweet_date BETWEEN '2022-01-01'
    AND '2022-12-31'
GROUP BY user_id;
````
which returned:
| user_id | tweet_count |
| --- | --- |
| 148 | 1 |
| 254 | 1 |
| 111 | 2 |

Afterwards I changed the column ``tweet_count`` to ``tweet_bucket`` and used it as a subquery.

````sql
SELECT tweet_bucket, COUNT(user_id) AS users_num
FROM (
  SELECT user_id, COUNT(*) AS tweet_bucket
  FROM tweets
  WHERE tweet_date BETWEEN '2022-01-01'
      AND '2022-12-31'
  GROUP BY user_id
) AS tweet_count
GROUP BY tweet_bucket;
````
which returned:
| tweet_bucket | users_num |
| --- | --- |
| 1 | 2 |
| 2 | 1 |

An alternative solution is to use a ``WITH`` statement:

````sql
WITH tweet_count AS (
  SELECT user_id, COUNT(*) AS tweet_bucket
  FROM tweets
  WHERE tweet_date BETWEEN '2022-01-01'
      AND '2022-12-31'
  GROUP BY user_id
)
SELECT tweet_bucket, COUNT(user_id) AS users_num
FROM tweet_count
GROUP BY tweet_bucket
````
