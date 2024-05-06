# DataLemur-Answer

DataLemur is a platform to help us practice questions for SQL, Python, Machine Learning, Probability, and certain related data science or analyst topics. There are five categories of questions. Each category has a difficulty level.

---
## Table of Contenst
## SQL-Easy
1. [Histogram of Tweets [Twitter SQL Interview]](#easy1)
2. [Data Science Skills [Linkedin SQL Interview]](#easy2)
3. [Page With No Likes [Facebook SQL Interview]](#easy3)
4. [Unfinished Parts[Tesla SQL Interview Question]](#easy4)


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
