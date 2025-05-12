# 📦 Case Study 7: User Playlist Engagement for Discovery

## 🧠 Business Scenario

Your analyses will help the Amazon Music Recommendation Team understand how playlist size and user engagement relate, enabling them to tailor playlists that maximize listening time and music discovery. This insight is key to enhancing user experience through optimized playlist recommendations.

---

## 🧾 Table format 

1. playlists(playlist_id, playlist_name, number_of_tracks)
2. playlist_engagement(playlist_id, user_id, listening_time_minutes, engagement_date)

---

## ❓ Questions & SQL Solutions

Ques 1: The Amazon Music Recommendation Team wants to know which playlists have the least number of tracks. Can you find out the playlist with the minimum number of tracks?

```sql
SELECT playlist_name  , number_of_tracks
FROM playlists
ORDER BY number_of_tracks 
LIMIT 1 ;

```
Ques 2: We are interested in understanding the engagement level of playlists. Specifically, we want to identify which playlist has the lowest average listening time per track. This means calculating the total listening time for each playlist in October 2024 and then normalizing it by the number of tracks in that playlist. Can you provide the name of the playlist with the lowest value based on this calculation?

```sql
 SELECT a.playlist_name, (b.total_listening_time)/(SUM(a.number_of_tracks)) as average
FROM (
   SELECT playlist_id , SUM(listening_time_minutes) as total_listening_time
   FROM playlist_engagement
   WHERE engagement_date LIKE "2024-10%"
   GROUP BY playlist_id 
) as b
JOIN playlists as a 
ON a.playlist_id = b.playlist_id
GROUP BY a.playlist_id
ORDER BY average
LIMIT 1 ;
```

Ques 3: To optimize our recommendations, we need the average monthly listening time per listener for each playlist in October 2024. For readibility, please round down to the average listening time to the nearest whole number.

```sql
 SELECT a.playlist_id , 
   b.playlist_name ,
   FLOOR( SUM(a.listening_time_minutes)/COUNT(DISTINCT a.user_id)) as average
FROM playlist_engagement as a
JOIN playlists as b
ON a.playlist_id = b.playlist_id
WHERE a.engagement_date LIKE "2024-10%"
GROUP BY  a.playlist_id ;

```