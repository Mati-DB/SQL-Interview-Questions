# Uber's Third Transaction [Uber]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/sql-third-transaction).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

***

## Additional Information

### Relational Diagram
<p align="left">
  <img src="https://github.com/user-attachments/assets/95b54175-6bbc-433a-a8fb-f0428423de22"/>
</p>

### Table

| user_id	| spend |	transaction_date |
|-------|-------|-------|
| 111 |	100.50	| 01/08/2022 12:00:00 |
| 111 |	55.00 |	01/10/2022 12:00:00 |
| 121 |	36.00	| 01/18/2022 12:00:00 |
| 145 |	24.99	| 01/26/2022 12:00:00 |
| 111 |	89.60	| 02/05/2022 12:00:00 |

### Output Example

| user_id	| spend |	transaction_date |
|-------|-------|-------|
| 111 |	89.60	| 02/05/2022 12:00:00 |
***

## Solution
I started by creating a adding a `rank` column partitioning by `user_id` and ordering by `transaction_date`.  

````sql
SELECT 
  user_id,  
  spend, 
  transaction_date,
  RANK() OVER (
    PARTITION BY  user_id 
    ORDER BY transaction_date) AS rank
FROM transactions;
````
which returned:
| user_id |	spend |	transaction_date	| rank |
|-------|-------|-------|-------|
| 111	| 100.50 |	01/08/2022 12:00:00	| 1 |
| 111	| 55.00	| 01/10/2022 12:00:00	| 2 |
| 111	| 89.60	| 02/05/2022 12:00:00	| 3 |
| 121	| 36.00	| 01/18/2022 12:00:00	| 1 |
| 121	| 22.20	| 04/01/2022 12:00:00	| 2 |
| 121	| 67.90	| 04/03/2022 12:00:00	| 3 |
| 145	| 24.99	| 01/26/2022 12:00:00	| 1 |
| 145	| 45.30	| 02/28/2022 12:00:00	| 2 |
| 230	| 78.30	| 06/14/2022 12:00:00	| 1 |
| 263	| 156.00	| 04/11/2022 12:00:00	| 1 |
| 263	| 68.12	| 07/11/2022 12:00:00	| 2 |
| 263 |	100.00	| 07/12/2022 12:00:00 |	3 |

Afterwards I created a temporary table named `transaction_rank`. With that, I added a query to filter by the transactions with ranked third.

````sql
WITH transaction_rank AS (
  SELECT 
    user_id,  
    spend, 
    transaction_date,
    RANK() OVER (
      PARTITION BY  user_id 
      ORDER BY transaction_date) AS rank
  FROM transactions)
  
SELECT user_id, spend, transaction_date
FROM transaction_rank
WHERE rank = 3  
````
which returned:
| user_id |	spend	| transaction_date |
|-------|-------|-------|
| 111	| 89.60	| 02/05/2022 12:00:00 |
| 121	| 67.90	| 04/03/2022 12:00:00 |
| 263 |	100.00	| 07/12/2022 12:00:00 |

Though this was considered correct, in a working environment I would have also needed to change the `transaction_date` format to 'yyyy-mm-dd hh-mi-ss' to avoid any possible mistakes.
