# 📦 Case Study 2: Ad Segment Performance Analysis

## 🧠 Business Scenario

As a Data Analyst on the Marketing Performance team, you are tasked with evaluating the effectiveness of our custom audience segments and lookalike audiences in driving user acquisition and conversions. Your team aims to optimize advertising strategies by analyzing key performance metrics such as ad impressions, total conversions, and cost per conversion across different audience segments. By leveraging this data, you will provide actionable insights to enhance campaign efficiency and improve overall marketing performance.

---

## 🧾 Table format 

1. ad_performance(ad_id, audience_segment_id, impressions, conversions, ad_spend, date)
2. audience_segments(audience_segment_id, segment_name)

---

## ❓ Questions & SQL Solutions

Ques 1: How many total ad impressions did we receive from custom audience segments in October 2024?

```sql
SELECT SUM(a.impressions)
FROM ad_performance as a
JOIN audience_segments as b 
ON a.audience_segment_id = b.audience_segment_id 
WHERE 
  b.segment_name LIKE "Custom Audience%" 
  AND 
  a.date LIKE "2024-10%" ;

```
Ques 2: What is the total number of conversions we achieved from each custom audience segment in October 2024?
```sql
 SELECT b.segment_name , SUM(a.conversions) as total_conversions
FROM ad_performance as a 
JOIN audience_segments as b
ON a.audience_segment_id = b.audience_segment_id
WHERE a.date LIKE "2024-10%" AND b.segment_name LIKE "Custom Audience%"
GROUP BY b.segment_name ;
```

Ques 3: For each custom audience or lookalike segment, calculate the cost per conversion. Only return this for segments that had non-zero spend and non-zero conversions.

Cost per conversion = Total ad spend / Total number of conversions

```sql
 SELECT b.segment_name ,(SUM(a.ad_spend) / SUM(a.conversions) )as Cost_per_conversion
FROM ad_performance as a 
JOIN audience_segments as b
ON a.audience_segment_id = b.audience_segment_id
WHERE 
   (b.segment_name LIKE "Custom Audience%" 
   OR
   b.segment_name LIKE "Lookalike Audience%")
GROUP BY b.segment_name 
HAVING SUM(a.ad_spend) >0 AND SUM(a.conversions) > 0;

```