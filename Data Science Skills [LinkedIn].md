# Data Science Skills [LinkedIn]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/matching-skills).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by ``candidate ID`` in ascending order.

Assume there are no duplicates in the ``candidates`` table

***

## Additional Information

### Relational Diagram
![Untitled (1)](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/ecb2b9f6-62aa-48cb-a284-2080de41cecd)

### Table
![image](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/16b90caf-9db9-4a4e-8d4b-6aceba2a7878)

### Output Example

| candidate_id |
|-------|
| 123 |
***

## Solution
I started by looking for the candidates that only had the skills requested

````sql
SELECT candidate_id, skill
FROM candidates
WHERE skill IN ('Python','Tableau','PostgreSQL')
;
````
which returned:

![image](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/75b47e15-66b7-4938-b0f8-1e721b471843)

Afterwards I used that query to select the candidates the desired skills
````sql
WITH desired_cand AS
  (
    SELECT candidate_id, skill
    FROM candidates
    WHERE skill IN ('Python','Tableau','PostgreSQL')
  )
  
SELECT candidate_id
FROM desired_cand
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) >= 3
ORDER BY candidate_id;
````
which returned:

![image](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/92e60452-108b-4d89-84ce-8bd9ebf9cde2)

An alternative solution could be:

````sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3;
````
