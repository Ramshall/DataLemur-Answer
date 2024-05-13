# Table of Contenst
## SQL-Medium
1. [Tweets Rolling Averages [Twitter SQL Interview]](#medium1)
2. [User's Third Transaction[Uber SQL Interview Question]](#medium2)
3. [Sending vs. Opening Snaps [Snapchat SQL Interview Question]](#medium3)

   
### <a id="medium1"></a>1. Tweets Rolling Averages [Twitter SQL Interview]
Given a table of tweet data over a specified time period, calculate the 3-day rolling average of tweets for each user. Output the user ID, tweet date, and rolling averages rounded to 2 decimal places.

Notes:

* A rolling average, also known as a moving average or running mean is a time-series technique that examines trends in data over a specified period of time.
* In this case, we want to determine how the tweet count for each user changes over a 3-day period.

Effective April 7th, 2023, the problem statement, solution and hints for this question have been revised.

`tweets` **Table**:
| Column Name | Type      |
| ----------- | -------   |
| user_id     | integer   |
| tweet_date  | timestamp |
| tweet_count | integer   |

`tweets` **Example_Input**:

| user_id |     tweet_date        | twwet_count  |
| -----   |   ----------------    | -----------  |
|  111    |	06/01/2022 00:00:00   |	    2        |
|  111    |	06/02/2022 00:00:00   |	    1        |
|  111	  | 06/03/2022 00:00:00   |	    3        |
|  111    |	06/04/2022 00:00:00   |	    4        |
|  111    |	06/05/2022 00:00:00   |	    5        |

`tweets` **Example_Output**:
| user_id |     tweet_date        | rolling_avg_3d|
| -----   |   ----------------    | -----------   |
|  111    |	06/01/2022 00:00:00   |	    2.00      |
|  111    |	06/02/2022 00:00:00   |	    1.50      |
|  111    |	06/03/2022 00:00:00   |	    2.00      |
|  111    |	06/04/2022 00:00:00   |	    2.67      |
|  111    |	06/05/2022 00:00:00   |	    4.00      |

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  user_id,
  tweet_date,
  ROUND(AVG(tweet_count) OVER(
    PARTITION BY user_id
    ORDER BY tweet_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ), 2) AS rolling_avg_3d
FROM
  tweets;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT    
  user_id,    
  tweet_date,   
  ROUND(AVG(tweet_count) OVER (
    PARTITION BY user_id     
    ORDER BY tweet_date     
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)
  ,2) AS rolling_avg_3d
FROM tweets;
</code></pre>
</details>

### <a id="medium2"></a>2. User's Third Transaction [Uber SQL Interview Question]
Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.

`transactions` **Table**:
| Column Name | Type      |
| ----------- | -------   |
| user_id     | integer   |
| spend       | decimal   |
| transaction_date | timestamp   |

`transactions` **Example Input**:
| user_id | spend |  transaction_date  |
| ------- | ------|  ------------------|
|   111   |	100.50|	01/08/2022 12:00:00|
|   111   |	55.00 |	01/10/2022 12:00:00|
|   121	  | 36.00 |	01/18/2022 12:00:00|
|   145   |	24.99	| 01/26/2022 12:00:00|
|   111	  | 89.60 |	02/05/2022 12:00:00|

**Example Output**:
| user_id | spend |  transaction_date  |
| ------- | ------|  ------------------|
|  111	  | 89.60	| 02/05/2022 12:00:00|

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  x.user_id,
  x.spend,
  x.transaction_date
FROM
  (SELECT
    user_id,
    spend,
    transaction_date,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) AS rank_transaction
  FROM
    transactions) x
WHERE x.rank_transaction = 3;
</code></pre>
</details>

**The Solution#1(Using Subquery)**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  user_id,
  spend,
  transaction_date
FROM (
  SELECT 
    user_id, 
    spend, 
    transaction_date, 
    ROW_NUMBER() OVER (
      PARTITION BY user_id ORDER BY transaction_date) AS row_num
  FROM transactions) AS trans_num 
WHERE row_num = 3;
</code></pre>
</details>

**The Solution#2(Using CTE)**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
WITH trans_num AS (
  SELECT 
    user_id, 
    spend, 
    transaction_date, 
    ROW_NUMBER() OVER (
      PARTITION BY user_id ORDER BY transaction_date) AS row_num 
  FROM transactions)
 
SELECT 
  user_id, 
  spend, 
  transaction_date 
FROM trans_num 
WHERE row_num = 3;
</code></pre>
</details>

### <a id="medium3"></a>3. Sending vs. Opening Snaps [Snapchat SQL Interview Question]
Assume you're given tables with information on Snapchat users, including their ages and time spent sending and opening snaps.

Write a query to obtain a breakdown of the time spent sending vs. opening snaps as a percentage of total time spent on these activities grouped by age group. Round the percentage to 2 decimal places in the output.

Notes:
Calculate the following percentages:
* time spent sending / (Time spent sending + Time spent opening)
* Time spent opening / (Time spent sending + Time spent opening)
* To avoid integer division in percentages, multiply by 100.0 and not 100.

Effective April 15th, 2023, the solution has been updated and optimised.

`activities` **Table**:
| Column Name |       Type       |
| ----------- | -----------------|
|  activity_id|	     integer    |
|  user_id	  |      integer    |
|activity_type|	string ('send', 'open', 'chat') |
|time_spent	  |      float      |
|activity_date|	    datetime    |

`activities` **Example Input**:
| activity_id | user_id |  activity_type  | time_spent |  activity_date    |
| ----------- |---------|-----------------|------------|-------------------|
|    7274	    |   123   |	     open	      |    4.50	   |06/22/2022 12:00:00|
|    2425	    |   123	  |      send	      |    3.50	   |06/22/2022 12:00:00|
|    1413	    |   456	  |      send	      |    5.67	   |06/23/2022 12:00:00|
|    1414	    |   789	  |      chat	      |    11.00	 |06/25/2022 12:00:00|
|    2536	    |   456	  |      open	      |    3.00	   |06/25/2022 12:00:00|

`age_breakdown` **Table**:
| Column Name |       Type       |
| ----------- | -----------------|
|   user_id	  |    integer       |
|  age_bucket	|string ('21-25', '26-30', '31-25')|

`age_breakdown` **Example Input**:
| user_id |     age_bucekt   |
| ------- | -----------------|
|   123	  |       31-35      |
|   456	  |       26-30      |
|   789	  |       21-25      |

**Example Output**
| age_bucket |  send_perc  |  open_perc  |
| -------    | ------------|-------------|
|   26-30	   |    65.40	   |    34.60    |
|   31-35	   |    43.75	   |    56.25    |

**Explanation**
Using the age bucket 26-30 as example, the time spent sending snaps was 5.67 and the time spent opening snaps was 3.

To calculate the percentage of time spent sending snaps, we divide the time spent sending snaps by the total time spent on sending and opening snaps, which is 5.67 + 3 = 8.67.

So, the percentage of time spent sending snaps is 5.67 / (5.67 + 3) = 65.4%, and the percentage of time spent opening snaps is 3 / (5.67 + 3) = 34.6%.

The dataset you are querying against may have different input & output - this is just an example!

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
WITH open_send_activities AS (
  SELECT
    y.age_bucket,
    x.activity_type,
    SUM(time_spent) FILTER (WHERE activity_type IN  ('send', 'open')) AS time_spent
  FROM
    activities x
  LEFT JOIN age_breakdown y
  ON x.user_id = y.user_id
  WHERE x.activity_type IN ('send', 'open')
  GROUP BY 1, 2
)
  
SELECT
  age_bucket,
  ROUND(SUM(time_spent) FILTER (WHERE activity_type = 'send') / SUM(time_spent) * 100.0, 2) AS send_perc,
  ROUND(SUM(time_spent) FILTER (WHERE activity_type = 'open') / SUM(time_spent) * 100.0, 2) AS open_perc
FROM
  open_send_activities
GROUP BY 1;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  age.age_bucket, 
  ROUND(100.0 * 
    SUM(activities.time_spent) FILTER (WHERE activities.activity_type = 'send')/
      SUM(activities.time_spent),2) AS send_perc, 
  ROUND(100.0 * 
    SUM(activities.time_spent) FILTER (WHERE activities.activity_type = 'open')/
      SUM(activities.time_spent),2) AS open_perc
FROM activities
INNER JOIN age_breakdown AS age 
  ON activities.user_id = age.user_id 
WHERE activities.activity_type IN ('send', 'open') 
GROUP BY age.age_bucket;
</code></pre>
</details>
