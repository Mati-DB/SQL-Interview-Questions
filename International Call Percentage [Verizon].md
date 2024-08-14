# International Call Percentage [Verizon]
Please note that all the information regarding the case study has been sourced from the following [link](https://datalemur.com/questions/international-call-percentage).

## Index
 - [Interview Question](#Interview-Question)
 - [Additional Information](#Additional-Information)
 - [Solution](#Solution)

***

## Interview Question
A phone call is considered an international call when the person calling is in a different country than the person receiving the call.

What percentage of phone calls are international? Round the result to 1 decimal.

Assumption:

The `caller_id` in `phone_info` table refers to both the caller and receiver.
***

## Additional Information

### Relational Diagram
<p align="left">
  <img src="https://github.com/user-attachments/assets/b6b09caf-9ebb-4b42-8b86-4856fbce5eb3"/>
</p>


### Tables

<table>
  <tr>
    <td>
      <p style="text-align: center;">phone_calls</p>
      <table border="1">
        <tr>
          <th>caller_id</th>
          <th>receiver_id</th>
          <th>call_time</th>
        </tr>
        <tr>
          <td>1</td>
          <td>2</td>
          <td>2022-07-04 10:13:49</td>
        </tr>
        <tr>
          <td>1</td>
          <td>5</td>
          <td>2022-08-21 23:54:56</td>
        </tr>
        <tr>
          <td>5</td>
          <td>1</td>
          <td>2022-05-13 17:24:06</td>
        </tr>
        <tr>
          <td>5</td>
          <td>6</td>
          <td>2022-03-18 12:11:49</td>
        </tr>
      </table>
    </td>
    <td>
      <p style="text-align: center;">phone_info</p>
      <table border="1">
        <tr>
          <th>caller_id</th>
          <th>country_id</th>
          <th>network</th>
          <th>phone_number</th>
        </tr>
        <tr>
          <td>1</td>
          <td>US</td>
          <td>Verizon</td>
          <td>+1-212-897-1964</td>
        </tr>
        <tr>
          <td>2</td>
          <td>US</td>
          <td>Verizon</td>
          <td>+1-703-346-9529</td>
        </tr>
        <tr>
          <td>3</td>
          <td>US</td>
          <td>Verizon</td>
          <td>+1-650-828-4774</td>
        </tr>
        <tr>
          <td>4</td>
          <td>US</td>
          <td>Verizon</td>
          <td>+1-415-224-6663</td>
        </tr>
        <tr>
          <td>5</td>
          <td>IN</td>
          <td>Vodafone</td>
          <td>+91 7503-907302</td>
        </tr>
        <tr>
          <td>6</td>
          <td>IN</td>
          <td>Vodafone</td>
          <td>+91 2287-664895</td>
        </tr>
      </table>
    </td>
  </tr>
</table>

### Output Example

| international_calls_pct |
|--|
| 50.0 |

***

## Solution
I started by changing how I saw the data so I had the caller's and receiver's country id. Once that was ready, I created another column named `int_calls` which was filled with 1 if it was an international call or 0 if it wasn't.  

````sql
SELECT 
  calls.caller_id, 
  info_c.country_id AS call_country, 
  calls.receiver_id,
  info_r.country_id AS receiver_country,
  CASE 
    WHEN info_c.country_id <> info_r.country_id
    THEN 1 
    ELSE 0
    END AS int_calls 
FROM phone_calls AS calls 
LEFT JOIN phone_info AS info_c
USING (caller_id)
LEFT JOIN phone_info AS info_r
ON calls.receiver_id = info_r.caller_id;
````
which returned:
| caller_id |	call_country |	receiver_id |	receiver_country |	int_calls |
|--|--|--|--|--|
| 14	| UK	| 31	| UK |	0 |
| 11	| IN	| 31	| UK	| 1 |
| 5	| US	| 33	| UK	| 1 |
| 5	| US	| 34	| UK	| 1 |
| 45	| DE	| 35	| DE	| 0 |
| 35	| DE	| 36	| DE	| 0 |
| 24	| DE	| 36	| DE	| 0 |
| 37	| DE	| 46	| DE	| 0 |
| 12	| IN	| 50 |	DE	| 1 |
| 7	| US	| 4	| US	| 1 |
| 13 |	UK	| 6	| US	| 1 |

Afterwards I created a temporary table named `call_detail`. With that, I added a query to calculate the average of interatinal calls, multiplied it by 100 and rounded it to 1 decimal.

````sql
WITH call_detail AS (
  SELECT 
    calls.caller_id, 
    info_c.country_id AS call_country, 
    calls.receiver_id,
    info_r.country_id AS receiver_country,
    CASE 
      WHEN info_c.country_id <> info_r.country_id
      THEN 1 
      ELSE 0
      END AS int_calls 
  FROM phone_calls AS calls 
  LEFT JOIN phone_info AS info_c
  USING (caller_id)
  LEFT JOIN phone_info AS info_r
  ON calls.receiver_id = info_r.caller_id)

SELECT ROUND(AVG(int_calls) * 100,1) AS international_calls_pct
FROM call_detail;
````
which returned:
| international_calls_pct |
|--|
| 54.5 |
