# DataLemur-Answer

DataLemur is a platform to help us practice questions for SQL, Python, Machine Learning, Probability, and certain related data science or analyst topics. There are five categories of questions. Each category has a difficulty level.

---
## Table of Contenst
## SQL-Easy
1. [Histogram of Tweets [Twitter SQL Interview]](#easy1)
2. [Data Science Skills [Linkedin SQL Interview]](#easy2)
3. [Page With No Likes [Facebook SQL Interview]](#easy3)
4. [Unfinished Parts[Tesla SQL Interview Question]](#easy4)

## Python-Easy
1. 
---

### <a id="easy1"></a>Histogram of Tweets [Twitter SQL Interview]
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


**Solution**
<details>
  <summary>Click to reveal the solution</summary>
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
---
