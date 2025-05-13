# 📦 Case Study 8: Content Recommendation Algorithm Performance

## 🧠 Business Scenario

You are a Data Analyst on the Content Discovery Team at Netflix, tasked with evaluating the impact of the recommendation algorithm on user engagement. Your team is focused on assessing how recommendations affect total watch time and categorizing user watch sessions to identify engagement patterns. The end goal is to refine the recommendation engine to enhance user satisfaction and drive more diverse content exploration.

---

## 🧾 Table format 

1. fct_watch_history(watch_id, user_id, content_id, watch_time_minutes, watch_date)
2. dim_content(content_id, title, genre, release_date)
3. fct_recommendations(recommendation_id, user_id, content_id, recommended_date)

---

## ❓ Questions & SQL Solutions

Ques 1: What is the total watch time for content after it was recommended to users? To correctly attribute watch time to the recommendation, it is critical to only include watch time after the recommendation was made to the user.

```sql
SELECT SUM(a.watch_time_minutes) as total_watch_time
FROM fct_watch_history as a
JOIN fct_recommendations as b
ON a.content_id = b.content_id AND a.user_id = b.user_id
WHERE a.watch_date >= b.recommended_date ;

```
Ques 2: The team wants to know the total watch time for each genre in first quarter of 2024, split by whether or not the content was recommended vs. non-recommended to a user.

Watch time should be bucketed into 'Recommended' by joining on both user and content, regardless of when they watched it vs. when they received the recommendation.

```sql
SELECT 
  dc.genre,
  CASE 
    WHEN fr.user_id IS NOT NULL THEN 'Recommended'
    ELSE 'Not Recommended'
  END AS session_type,
  SUM(fwh.watch_time_minutes) AS total_watch_time
FROM fct_watch_history fwh
JOIN dim_content dc 
  ON fwh.content_id = dc.content_id
LEFT JOIN fct_recommendations fr 
  ON fwh.user_id = fr.user_id 
  AND fwh.content_id = fr.content_id
WHERE fwh.watch_date BETWEEN '2024-01-01' AND '2024-03-31'
GROUP BY dc.genre, session_type;

```

Ques 3: The team aims to categorize user watch sessions into 'Short', 'Medium', or 'Long' based on watch time for recommended content to identify engagement patterns.
'Short' for less than 60 minutes, 'Medium' for 60 to 120 minutes, and 'Long' for more than 120 minutes. Can you classify and count the sessions in Q1 2024 accordingly?

```sql
SELECT sub.type_of_sessions , COUNT(*) as count_of_sessions
FROM (
      SELECT 
         CASE 
               WHEN watch_time_minutes < 60 THEN "Short"
               WHEN watch_time_minutes BETWEEN 60 AND 120 THEN "Medium"
               ELSE "Long"
         END AS type_of_sessions
      FROM fct_watch_history as  fwh
      JOIN fct_recommendations as fr
      ON fwh.user_id = fr.user_id AND fwh.content_id = fr.content_id
      WHERE fwh.watch_date BETWEEN "2024-01-01" AND "2024-03-31"
  ) AS sub
GROUP BY sub.type_of_sessions ;

```
