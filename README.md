# DataLemur-Answer

DataLemur is a platform to help us practice questions for SQL, Python, Machine Learning, Probability, and certain related data science or analyst topics. There are five categories of questions. Each category has a difficulty level.

---
## Table of Contenst
## SQL-Easy
1. [Histogram of Tweets [Twitter SQL Interview]](#easy1)
2. [Data Science Skills [Linkedin SQL Interview]](#easy2)
3. [Page With No Likes [Facebook SQL Interview]](#easy3)
4. [Unfinished Parts [Tesla SQL Interview Question]](#easy4)
5. [Laptop vs. Mobile Viewership [New York Times SQL Interview Question]](#easy5)
6. [Average Post Hiatus (Part 1) [Facebook SQL Interview Question]](#easy6)
7. [Teams Power Users [Microsoft SQL Interview Question]](#easy7)
8. [Duplicate Job Listings [Linkedin SQL Interview Question]](#easy8)
9. [Cities With Completed Trades [Robinhood SQL Interview Question]](#easy9)
10. [Average Review Ratings [Amazon SQL Interview Question]](#easy10)
11. [App Click-through Rate (CTR) [Facebook SQL Interview Question]](#easy11)
12. [Second Day Confirmation [Tiktok SQL Interview Question]](#easy12)
13. [Cards Issued Difference [JPMorgan SQL Interview Question]](#easy13)
14. [Compressed Mean [Alibaba SQL Interview Question]](#easy14)
15. [Pharmacy Analytics (Part 1) [CVS Health Interview Question]](#easy15)
16. [Pharmacy Analytics (Part 2) [CVS Health SQL Interview Question]](#easy16)
17. [Pharmacy Analytics (Part 3) [CVS Health SQL Interview Question]](#easy17)
18. [Patient Support Analysis (Part 1) [UnitedHelath SQL Interview Question]](#easy18)


### <a id="easy1"></a>1. Histogram of Tweets [Twitter SQL Interview]
Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

`tweets` **Table**:
| Column Name | Type    |
| ----------- | ------- |
| tweet_id    | integer |
| user_id   | integer |
| msg | string |
| tweet_date | timestamp |


`tweets` **Example Input**:
| twwet_id | user_id | msg  | tweet_date |
| ---- | ----| ------------------------- | ---------- |
|214252|	111	|Am considering taking Tesla private at $420. Funding secured.|	12/30/2021 00:00:00|
|739252|	111	|Despite the constant negative press covfefe|	01/01/2022 00:00:00|
|846402|	111	|Following @NickSinghTech on Twitter changed my life!|	02/14/2022 00:00:00|
|241425|	254	|If the salary is so competitive why won’t you tell me what it is?|	03/01/2022 00:00:00|
|231574|	148	|I no longer have a manager. I can't be managed|	03/23/2022 00:00:00|

`tweets` **Table**:
| twwet_bucket |  users_num |
| -------| ------- |
|   1  |   2   |
|   2  |   1   |

**Explanation**:
Based on the example output, there are two users who posted only one tweet in 2022, and one user who posted two tweets in 2022. The query groups the users by the number of tweets they posted and displays the number of users in each group.

The dataset you are querying against may have different input & output - this is just an example!


**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  x.tweet_bucket,
  COUNT(x.user_id) as users_num
FROM (SELECT COUNT(tweet_id) AS tweet_bucket, user_id 
      FROM tweets 
      WHERE tweet_date >= '2022-01-01'
      GROUP BY 2) x
GROUP BY 1
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  tweet_count_per_user AS tweet_bucket, 
  COUNT(user_id) AS users_num 
FROM (
  SELECT 
    user_id, 
    COUNT(tweet_id) AS tweet_count_per_user 
  FROM tweets 
  WHERE tweet_date BETWEEN '2022-01-01' 
    AND '2022-12-31'
  GROUP BY user_id) AS total_tweets 
GROUP BY tweet_count_per_user;
</code></pre>
</details>

**The Solution#2**
<details>
  <summary>Click to reveal the second solution </summary>
<pre><code>
WITH total_tweets AS (
  SELECT 
    user_id, 
    COUNT(tweet_id) AS tweet_count_per_user
  FROM tweets 
  WHERE tweet_date BETWEEN '2022-01-01' 
    AND '2022-12-31' 
  GROUP BY user_id) 
  
SELECT 
  tweet_count_per_user AS tweet_bucket, 
  COUNT(user_id) AS users_num 
FROM total_tweets 
GROUP BY tweet_count_per_user;
</code></pre>
</details>

### <a id="easy2"></a>2. Data Science Skills [Linkedin SQL Interview]
Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

**Assumption:**

`* There are no duplicates in the candidates table.`

`candidates` **Table**:
| Column Name | Type    |
| ----------- | ------- |
| candidate_id | integer |
| skill | varchar |

`candidates` **Example Input**:
| candidate_id | skill    |
| ----------- | --------- |
|123|	Python |
|123|	Tableau |
|123|	PostgreSQL |
|234|	R |
|234|	PowerBI |
|234|	SQL Server |
|345|	Python |
|345|	Tableau |

`candidates` **Example Output**:
| candidate_id |
| ----------- | 
| 123 |

**Expalanation:**
Candidate 123 is displayed because they have Python, Tableau, and PostgreSQL skills. 345 isn't included in the output because they're missing one of the required skills: PostgreSQL.

The dataset you are querying against may have different input & output - this is just an example!

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  x.candidate_id
FROM
  (SELECT
      candidate_id,
      STRING_AGG(skill, ', ') AS skill_required
  FROM
      candidates
  WHERE
      skill IN ('Python', 'Tableau', 'PostgreSQL')
  GROUP BY
      candidate_id
  HAVING
      COUNT(DISTINCT skill) = 3) x;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  candidate_id
FROM 
  candidates
WHERE 
  skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY 
  candidate_id
HAVING 
  COUNT(skill) = 3
ORDER BY 
  candidate_id;
</code></pre>
</details>

### <a id="easy3"></a>3. Page With No Likes [Facebook SQL Interview Question]
Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.
`pages` **Table**:
| Column Name | Type    |
| ----------- | ------- |
| page_id | integer |
| page_name | varchar |

`pages` **Example Input**:
| page_id | page_name    |
| --------- | ---------- |
|20001|	SQL Solutions |
|20045|	Brain Exercises |
|20701|	Tips for Data Analysts |

`page_likes` **Table**:
| Column Name | Type    |
| ----------- | ------- |
| user_id | integer |
| page_id | integer |
| liked_date | datetime |

`page_likes` **Example Input**:
| user_id | page_id |    liked_date     |
| -----   | ------- | ------------------|
|111|	20001 |	04/08/2022 00:00:00 |
|121|	20045 |	03/12/2022 00:00:00 |
|156|	20001 |	07/25/2022 00:00:00 |

**Example Output**:
| page_id |
| --------|
| 20701   |

The dataset you are querying against may have different input & output - this is just an example!

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  x.page_id
FROM
  (SELECT 
    p.page_id,
    COUNT(pl.liked_date) AS like_count
  FROM 
    pages p
  LEFT JOIN
      page_likes pl ON p.page_id = pl.page_id
  GROUP BY 1) x
WHERE x.like_count < 1
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  page_id
FROM 
  pages
EXCEPT
SELECT 
  page_id
FROM 
  page_likes;
</code></pre>
</details>

**The Solution#2**
<details>
  <summary>Click to reveal the second solution </summary>
<pre><code>
SELECT 
  pages.page_id
FROM 
  pages
LEFT OUTER JOIN page_likes AS likes
  ON pages.page_id = likes.page_id
WHERE 
  likes.page_id IS NULL;
</code></pre>
</details>

**The Solution#3**
<details>
  <summary>Click to reveal the third solution </summary>
<pre><code>
SELECT 
  page_id
FROM 
  pages
WHERE page_id NOT IN (
    SELECT 
      page_id
  FROM 
    page_likes
  WHERE page_id IS NOT NULL
  );
</code></pre>
</details>

**The Solution#4**
<details>
  <summary>Click to reveal the fourth solution </summary>
<pre><code>
SELECT 
  page_id
FROM 
  pages
WHERE NOT EXISTS (
  SELECT page_id
  FROM page_likes AS likes
  WHERE likes.page_id = pages.page_id
  );
</code></pre>
</details>


### <a id="easy4"></a>4. Unfinished Parts [Tesla SQL Interview Question]
Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query to determine which parts have begun the assembly process but are not yet finished.

Assumptions:

* parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
* An unfinished part is one that lacks a finish_date.

This question is straightforward, so let's approach it with simplicity in both thinking and solution.

Effective April 11th 2023, the problem statement and assumptions were updated to enhance clarity.

`part_assembly` **Table**:
|Column Name|	Type |
|-----------|--------|
|part|	string |
|finish_date|	datetime |
|assembly_step|	integer |

`part_assembly` **Example Input**:
|part|	finish_date	|assembly_step|
|-------| ------------------- | ---- |
|battery|	01/22/2022 00:00:00 |	1 |
|battery|	02/22/2022 00:00:00| 2 |
|battery|	03/22/2022 00:00:00|	3 |
|bumper|	01/22/2022 00:00:00 |	1 |
|bumper|	02/22/2022 00:00:00 |	2 |
|bumper|		| 3 | 
|bumper|		| 4 |

**Example Input**:
| part |	assembly_step |
| -----| ---------------|
|bumper|	3 |
|bumper|	4 |

**Explanation**
The bumpers in step 3 and 4 are the only item that remains unfinished as it lacks a recorded finish date.

The dataset you are querying against may have different input & output - this is just an example!

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT 
  part,
  assembly_step
FROM 
  parts_assembly
WHERE
  finish_date IS NULL;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT part, assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;
</code></pre>
</details>

### <a id="easy5"></a>5. Laptop vs. Mobile Viewership [New York Times SQL Interview Question]
Assume you're given the table on user viewership categorised by device type where the three types are laptop, tablet, and phone.

Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as laptop_reviews and the total viewership for mobile devices as mobile_views.

Effective 15 April 2023, the solution has been updated with a more concise and easy-to-understand approach.

`viewership` **Table**:
|Column Name|	Type |
|-----------|--------|
| user_id     | integer |
| device_type | string ('laptop', 'tablet', 'phone') |
| view_time   | timestamp |

`viewership` **Example Input**:
|user_id|	device_type |      view_time       |
|-------|-------------| -------------------- |
|  123  |	  tablet    |	 01/02/2022 00:00:00 |
|  125  |	  laptop    |	 01/07/2022 00:00:00 |
|  128  |	  laptop    |	 02/09/2022 00:00:00 |
|  129  |	  phone     |	 02/09/2022 00:00:00 |
|  145  |	  tablet    |	 02/24/2022 00:00:00 |

`viewership` **Example Output**:
|laptop_views|mobile_views|
|------------|------------|
|     2      |      3     |

**Explanation**
Based on the example input, there are a total of 2 laptop views and 3 mobile views.

The dataset you are querying against may have different input & output - this is just an example!

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT 
  x.laptop_views,
  y.mobile_views
FROM  
  (SELECT COUNT(view_time) as laptop_views FROM viewership WHERE device_type = 'laptop') x,
  (SELECT COUNT(view_time) as mobile_views FROM viewership WHERE device_type IN ('phone', 'tablet')) y;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  COUNT(*) FILTER (WHERE device_type = 'laptop') AS laptop_views,
  COUNT(*) FILTER (WHERE device_type IN ('tablet', 'phone'))  AS mobile_views 
FROM viewership;
</code></pre>
</details>

**The Solution#2**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  SUM(CASE WHEN device_type = 'laptop' THEN 1 ELSE 0 END) AS laptop_views, 
  SUM(CASE WHEN device_type IN ('tablet', 'phone') THEN 1 ELSE 0 END) AS mobile_views 
FROM viewership;
</code></pre>
</details>

### <a id="easy6"></a>6. Average Post Hiatus (Part 1) [Facebook SQL Interview Question]
Given a table of Facebook posts, for each user who posted at least twice in 2021, write a query to find the number of days between each user's first post of the year and last post of the year in the year 2021. Output the user and number of the days between each user's first and last post.

`posts` **Table**:
|Column Name|	Type |
|-----------|-----------|
|  user_id  | integer   |
|  post_id  | integer   |
|post_content| text     |
| post_date | timestamp |

`posts` **Example Input**:
|  user_id  |  post_id  | post_content |      post_date        |
|-----------|-----------|--------------|-----------------------|
|  151652	  |  599415   |	Need a hug   |	07/10/2021 12:00:00  |
|  661093	  |  624356	  |  Bed. Class 8-12. Work 12-3. Gym 3-5 or 6. Then class 6-10. Another day that's gonna fly by. I miss my girlfriend|	07/29/2021 13:00:00 |
|  004239   |  784254	  | Happy 4th of July! | 07/04/2021 11:00:00 |
|  661093	  |  442560	  | Just going to cry myself to sleep after watching Marley and Me.|	07/08/2021 14:00:00  |
|  151652	  |  111766	  | I'm so done with covid - need travelling ASAP! |	07/12/2021 19:00:00 |

`posts` **EXample Output**:
|Column Name|	Type |
|-----------|-----------|
|  151652  |	  2   |
|  661093  |    21  |

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  user_id,
  DATE_PART('day', MAX(post_date) - MIN(post_date)) AS days_between
FROM
  posts
WHERE
  post_date BETWEEN '2021-01-01' AND '2021-12-31'
GROUP BY
  user_id
HAVING 
  COUNT(*) > 1
ORDER BY 
  user_id;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
	user_id, 
    MAX(post_date::DATE) - MIN(post_date::DATE) AS days_between
FROM posts
WHERE DATE_PART('year', post_date::DATE) = 2021 
GROUP BY user_id
HAVING COUNT(post_id)>1;
</code></pre>
</details>

### <a id="easy7"></a>7. Teams Power Users [Microsoft SQL Interview Question]
Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.

Assumption:
* No two users have sent the same number of messages in August 2022.

`messages` **Table**:
|Column Name|	Type |
|-----------|-----------|
|message_id| integer   |
|sender_id| integer   |
|receiver_id| integer     |
|content| varchar |
|sent_date| datetime |

`message` **Example Input**:
|message_id|sender_id|receiver_id|content|sent_date|
|----------|---------|-----------|-----------------------------------|------------------|
|901|	3601 |	4500	 |  You up?	 |  08/03/2022 00:00:00  |
|902|	4500 |	3601	 |Only if you're buying	|08/03/2022 00:00:00|
|743|	3601 |	8752	 |Let's take this offline|	06/14/2022 00:00:00  |
|922|	3601 |	4500	 | Get on the call	|08/10/2022 00:00:00|


**Example Output:**
|sender_id|	message_count |
|---------|---------------|
|  3601   |        2      |
|  4500   |        1      |

The dataset you are querying against may have different input & ouput - **this is just an example!**

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT 
  sender_id,
  COUNT(content) AS message_count
FROM
  messages
WHERE 
  sent_date BETWEEN '2022-08-01' AND '2022-08-31'
GROUP BY
    sender_id
ORDER BY 
  message_count DESC
LIMIT 2
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  sender_id,
  COUNT(message_id) AS count_messages
FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = '8'
  AND EXTRACT(YEAR FROM sent_date) = '2022'
GROUP BY sender_id
ORDER BY count_messages DESC
LIMIT 2;
</code></pre>
</details>

### <a id="easy8"></a>8. Duplicate Job Listings [Linkedin SQL Interview Question]
Assume you're given a table containing job postings from various companies on the LinkedIn platform. Write a query to retrieve the count of companies that have posted duplicate job listings.

Definition:
* Duplicate job listings are defined as two job listings within the same company that share identical titles and descriptions.

`job_listings` **Table**:
|Column Name|	  Type   |
|-----------|----------|
|job_id     |  integer |
|company_id |  string  |
|title      |  string  |
|description|  string  |

`job_listings` **Example Input**:
|job_id|company_id|  title  |    description    |
|------|----------|---------|-------------------|
|  248 |	 827	  |Business Analyst|	Business analyst evaluates past and current business data with the primary goal of improving decision-making processes within organizations.|
|  149 |	 845	  |Business Analyst|	Business analyst evaluates past and current business data with the primary goal of improving decision-making processes within organizations.|
|  945 |	 345	  |Data Analyst|	Data analyst reviews data to identify key insights into a business's customers and ways the data can be used to solve problems.|
|  164 |	 345	  |Data Analyst|	Data analyst reviews data to identify key insights into a business's customers and ways the data can be used to solve problems.|
|  172 |	 244	  |Data Engineer|	Data engineer works in a variety of settings to build systems that collect, manage, and convert raw data into usable information for data scientists and business analysts to interpret.|

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT
  COUNT(x.company_id) AS duplicate_companies
FROM
  (SELECT
    company_id,
    title,
    description,
    COUNT(company_id) AS total_company
  FROM
    job_listings
  GROUP BY 1, 2, 3) x
WHERE
  x.total_company > 1;
</code></pre>
</details>

**The Solution#1 (CTE)**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
WITH job_count_cte AS (
  SELECT 
    company_id, 
    title, 
    description, 
    COUNT(job_id) AS job_count
  FROM job_listings
  GROUP BY company_id, title, description
)

SELECT COUNT(DISTINCT company_id) AS duplicate_companies
FROM job_count_cte
WHERE job_count > 1;
</code></pre>
</details>

**The Solution#2 (subqueries)**
<details>
  <summary>Click to reveal the second solution </summary>
<pre><code>
SELECT COUNT(DISTINCT company_id) AS duplicate_companies
FROM (
  SELECT 
    company_id, 
    title, 
    description, 
    COUNT(job_id) AS job_count
  FROM job_listings
  GROUP BY company_id, title, description
) AS job_count_cte
WHERE job_count > 1;
</code></pre>
</details>

### <a id="easy9"></a>9. Cities With Completed Trades [Robinhood SQL Interview Question]
Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.

Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.

`trades` **Table**:
|Column Name|	  Type   |
|-----------|-------------------|
|order_id|integer|
|user_id|integer|
|quantity|	integer|
|status|	string ('Completed', 'Cancelled')|
|date|	timestamp|
|price|	decimal (5, 2)|

`trades` **Example Input**:
|order_id|	user_id  |	quantity  |	status  |	       date       |	price  |
|--------|-----------|------------|---------|-------------------|--------|
|100101|	111	|  10	|  Cancelled  |	 08/17/2022 12:00:00	|  9.80  |
|100102|	111	|  10 |	 Completed	|  08/17/2022 12:00:00	|  10.00 |
|100259|	148	|  35	|  Completed	|  08/25/2022 12:00:00	|  5.10  |
|100264|	148	|  40	|  Completed	|  08/26/2022 12:00:00	|  4.80  |
|100305|	300	|  15	|  Completed	|  09/05/2022 12:00:00	|  10.00 |
|100400|	178	|  32	|  Completed	|  09/17/2022 12:00:00	|  12.00 |
|100565|	265	|  2	|  Completed	|  09/27/2022 12:00:00	|  8.70  |

`users` **Table**:
|Column Name|	Type|
|-------------|------|
|user_id|	integer|
|city|	string|
|email|	string|
|signup_date|	datetime|

`users` **Example Input**:
|user_id|	city	|email|	signup_date |
|-------|-------------|----------------|------------------------|
|111|	San Francisco	|rrok10@gmail.com|	08/03/2021 12:00:00|
|148|	Boston	|sailor9820@gmail.com|	08/20/2021 12:00:00|
|178|	San Francisco	|harrypotterfan182@gmail.com|	01/05/2022 12:00:00|
|265|	Denver	|shadower_@hotmail.com|	02/26/2022 12:00:00|

**Example Output:**
|city|	total_orders  |
|-------------|-------|
|San Francisco|	  3   |
|Boston|	2  |
|Denver|	1  |


In the given dataset, San Francisco has the highest number of completed trade orders with 3 orders. Boston holds the second position with 2 orders, and Denver ranks third with 1 order.
300	San Francisco	houstoncowboy1122@hotmail.com	06/30/2022 12:00:00

**My Answer**
<details>
  <summary>Click to reveal my query</summary>
<pre><code>
SELECT 
  x.city,
  count(x.city) AS total_orders
FROM
  users x
LEFT JOIN trades y ON x.user_id = y.user_id
WHERE y.status = 'Completed'
GROUP BY 1
ORDER BY total_orders DESC
LIMIT 3;
</code></pre>
</details>

**The Solution#1**
<details>
  <summary>Click to reveal the first solution </summary>
<pre><code>
SELECT 
  x.city,
  count(x.city) AS total_orders
FROM
  users x
LEFT JOIN trades y ON x.user_id = y.user_id
WHERE y.status = 'Completed'
GROUP BY 1
ORDER BY total_orders DESC
LIMIT 3;
</code></pre>
</details>
