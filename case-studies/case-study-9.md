# 📦 Case Study 9: App Download Conversion Rates by Category

## 🧠 Business Scenario

You are on the Google Play store's App Marketplace team. You and your team want to understand how different app categories convert from browsing to actual downloads. This analysis is critical in informing future product placement and marketing strategies for app developers and users.

---

## 🧾 Table format 

1. dim_app(app_id, app_name, category, app_type)
2. fct_app_browsing(app_id, browse_date, browse_count)
3. fct_app_downloads(app_id, download_date, download_count)

---

## ❓ Questions & SQL Solutions

Ques 1: The marketplace team wants to identify high and low performing app categories. Provide the total downloads for the app categories for November 2024. If there were no downloads for that category, return the value as 0.

```sql
SELECT 
  da.category, 
  SUM(CASE 
        WHEN fad.download_date LIKE '2024-11%' 
        THEN fad.download_count 
        ELSE 0 
      END) AS total_downloads
FROM dim_app AS da
LEFT JOIN fct_app_downloads AS fad
  ON da.app_id = fad.app_id
GROUP BY da.category;


```
Ques 2:Our team's goal is download conversion rate -- defined as downloads per browse event. For each app category, calculate the download conversion rate in December, removing categories where browsing counts are be zero.

```sql
 WITH browser_counts as (
   SELECT app_id , SUM(browse_count) as total_browse
   FROM fct_app_browsing
   WHERE browse_date LIKE "2024-12%"
   GROUP BY app_id
 ),
download_counts as (
   SELECT app_id , SUM(download_count) as total_download
   FROM fct_app_downloads
   WHERE download_date LIKE "2024-12%"
   GROUP BY app_id
 ),
app_data as (
   SELECT 
         a.app_id,
         a.category,
         IFNULL(b.total_browse , 0) as total_browse,
         IFNULL(c.total_download , 0) as total_download
   FROM dim_app a
   LEFT JOIN browser_counts b ON b.app_id = a.app_id
   LEFT JOIN download_counts c ON c.app_id = a.app_id
)

SELECT 
      category,
      (SUM(total_download) * 1.0/ SUM(total_browse)) as download_conversion_rate
FROM app_data
WHERE total_browse > 0
GROUP BY category;

```

Ques 3: The team wants to compare conversion rates between free and premium apps across all categories. Combine the conversion data for both app types to present a unified view for Q4 2024.

```sql
WITH browser_counts as (
   SELECT app_id , SUM(browse_count) as total_browse
   FROM fct_app_browsing
   WHERE browse_date BETWEEN "2024-10-01" AND "2024-12-31"
   GROUP BY app_id
 ),
download_counts as (
   SELECT app_id , SUM(download_count) as total_download
   FROM fct_app_downloads
   WHERE download_date BETWEEN "2024-10-01" AND "2024-12-31"
   GROUP BY app_id
 ),
app_data as (
   SELECT 
         a.app_id,
         a.category,
         a.app_type,
         IFNULL(b.total_browse , 0) as total_browse,
         IFNULL(c.total_download , 0) as total_download
   FROM dim_app a
   LEFT JOIN browser_counts b ON b.app_id = a.app_id
   LEFT JOIN download_counts c ON c.app_id = a.app_id
)

SELECT 
      category,
      app_type,
      (SUM(total_download) * 1.0/ SUM(total_browse)) as download_conversion_rate
FROM app_data
WHERE total_browse > 0
GROUP BY category , app_type;

```
