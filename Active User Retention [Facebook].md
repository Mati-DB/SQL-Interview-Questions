# Active User Retention [Facebook]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/user-retention).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
Assume you're given a table containing information on Facebook user actions. Write a query to obtain number of monthly active users (MAUs) in July 2022, including the month in numerical format "1, 2, 3".

Hint:

 - An active user is defined as a user who has performed actions such as 'sign-in', 'like', or 'comment' in both the current month and the previous month.

***

## Additional Information

### Relational Diagram
<p align="left">
  <img src="https://github.com/user-attachments/assets/15bfa16e-1cb2-4f6b-804e-8e8c1525b8ec"/>
</p>

### Table

| user_id |	event_id |	event_type |	event_date |
|---|---|---|---|
| 445	| 7765 |	sign-in	| 05/31/2022 12:00:00 | 
| 742	| 6458	| sign-in |	06/03/2022 12:00:00 |
| 445	| 3634	| like	| 06/05/2022 12:00:00 |
| 742	| 1374	| comment |	06/05/2022 12:00:00 |
| 648 |	3124	| like |	06/18/2022 12:00:00 |


### Output Example

| month |	monthly_active_users |
|--|--|
| 6 |	1 |

***

## Solution
I started by filtering the `user_actions` table by the actions performed during June.

````sql
SELECT * 
FROM user_actions
WHERE event_date BETWEEN '06/01/2022' AND '06/30/2022';
````

which returned:
| user_id |	event_id |	event_type |	event_date |	month |
|--|--|--|--|--|
| 445	| 3634 |	like	| 06/05/2022 12:00:00 |	7 |
| 648	| 3124	| like	| 06/18/2022 12:00:00	| 7 |
| 648 |	2725	| sign-in |	06/22/2022 12:00:00 |	7 |

Afterwards I used the filtered data to create a temporary table named `prev_mon`. Then I used a left join to search for the users with activity in both June and July. 

````sql
WITH prev_mon AS (
  SELECT * 
  FROM user_actions
  WHERE event_date BETWEEN '06/01/2022' AND '06/30/2022')

SELECT user_actions.month, COUNT(DISTINCT user_id) AS monthly_active_users
FROM prev_mon
LEFT JOIN user_actions
USING (user_id)
WHERE user_actions.event_date BETWEEN '07/01/2022' AND '07/31/2022'
GROUP BY user_actions.month
````
which returned:
| month |	monthly_active_users |
|--|--|
| 7 |	2 |

This means that in July there were 2 active users.
