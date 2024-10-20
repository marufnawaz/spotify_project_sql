# Spotify Advanced SQL Project and Query Optimization

### 1. Retrieve the names of all tracks that have more than 1 billion streams.
```sql
SELECT * FROM spotify
WHERE stream > 1000000000
```
### 2. List all albums along with their respective artists.
```sql
SELECT DISTINCT album, artist
FROM spotify
ORDER BY artist
```
### 3. Get the total number of comments for tracks where licensed = TRUE.
```sql
SELECT * FROM spotify
WHERE licensed=1
```
### 4. Find all tracks that belong to the album type single.
```sql
SELECT * FROM spotify
WHERE album_type='single'
```
### 5. Count the total number of tracks by each artist.
```sql
SELECT artist, count(*) AS tot_number_of_tracks
FROM spotify
GROUP BY artist
ORDER BY tot_number_of_tracks DESC
```

### 6. Calculate the average danceability of tracks in each album.
```SQL
SELECT album, AVG(danceability) AS avg_danceability
FROM spotify
GROUP BY album
ORDER BY avg_danceability DESC
```

### 7. Find the top 5 tracks with the highest energy values.
```SQL
SELECT TOP 5 track, MAX(energy) AS high_energy
FROM spotify
GROUP BY track
ORDER BY high_energy DESC
```
### 8. List all tracks along with their views and likes where official_video = TRUE.
```SQL
SELECT track, SUM(views) AS tot_views, SUM(likes) AS tot_likes
FROM spotify
WHERE official_video = 1
GROUP BY track
ORDER BY tot_views DESC, tot_likes DESC
```
### 9. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT * FROM (
SELECT track,
       COALESCE(SUM(CASE WHEN most_played_on='Youtube' THEN stream END),0) AS streamed_on_yt,
	   COALESCE(SUM(CASE WHEN most_played_on='Spotify' THEN stream END),0) AS streamed_on_spotify
FROM spotify
GROUP BY track) t1
WHERE streamed_on_yt < streamed_on_spotify
AND streamed_on_yt !=0
```
### 10. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
WITH rank_by_artist AS (
						SELECT artist, 
							   track, 
							   SUM(views) AS tot_views
							   ,DENSE_RANK() OVER (PARTITION BY artist ORDER BY SUM(views) DESC) AS rnk
						FROM spotify
						GROUP BY artist, track
						)
SELECT * FROM rank_by_artist
WHERE rnk <=3
```
### 11. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT track, artist, liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
```
### 12. Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.
```sql
WITH energy AS
				(SELECT album,
						MAX(energy) as highest_energy,
						MIN(energy) as lowest_energery
				FROM spotify
				GROUP BY album
				)
SELECT album,
	   highest_energy - lowest_energery as energy_diff
FROM energy
ORDER BY energy_diff DESC
```
### 13. Find tracks where the energy-to-liveness ratio is greater than 1.2.
```sql
SELECT * FROM (
				SELECT track, ROUND(1.0*energy/NULLIF(liveness,0),1) AS energy_to_liveness_ratio
				FROM spotify ) t1
WHERE energy_to_liveness_ratio >1.2
```
### 14. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.

```sql
SELECT track, 
       views, 
       likes, 
       SUM(likes) OVER (ORDER BY views,likes) AS cumulative_likes
FROM spotify
ORDER BY views;
```
