# Page With No Likes [Facebook]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/sql-page-with-no-likes).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page").

Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

***

## Additional Information

### Relational Diagram
![Untitled (2)](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/137b77b8-9c5a-4672-9df7-8aaf07176266)


### Tables
``pages``
<p align="left">
  <img src="https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/0ba1ee31-1345-45b6-9053-81aac7c4e943">
</p>

``page_likes``
<p align="left">
  <img src="https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/0c2a3e90-8735-44d4-90e4-302cf4297485">
</p>

### Output Example

| page_id |
|-------|
| 20701 |
***

## Solution
I started by selecting the ``page_id`` present in the ``page_likes`` table because if there was a page in it, it meant it was liked.

````sql
SELECT page_id
FROM page_likes
````
which returned:

![image](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/fc1b1a4b-8ca0-4cb4-bb7f-9dd918854019)

Afterwards I selected the page ids from the ``pages`` that were present in ``page_likes``
````sql
SELECT page_id
FROM pages
WHERE page_id NOT IN (
    SELECT page_id
    FROM page_likes
)
ORDER BY page_id;
````
which returned:

![image](https://github.com/Mati-DB/SQL-Interview-Questions/assets/170549189/8a423b42-c5ae-4d0a-8231-d4f6f125d323)
