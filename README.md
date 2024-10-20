# Spotify Advanced SQL Project
**Dataset Link:** [Spotify Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](https://github.com/marufnawaz/spotify_project_sql/blob/main/spotify_logo.jpg)

## Overview

This project focuses on analyzing a Spotify dataset containing detailed information about tracks, albums, and artists using SQL. It encompasses the complete process of normalizing a previously denormalized dataset, executing SQL queries at different difficulty levels (ranging from simple to advanced), and enhancing query performance. The main objectives are to hone advanced SQL techniques while extracting meaningful insights from the dataset

## Schema

```sql
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BIT,
    official_video BIT,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## Data Exploration

Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:

- Artist: The performer of the track.
- Track: The name of the song.
- Album: The album to which the track belongs.
- Album_type: The type of album (e.g., single or album).
- Various metrics such as danceability, energy, loudness, tempo, and more.
  
## Business Problems and Solutions

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
## Conclusion of Spotify SQL Project

This project provided a comprehensive analysis of a Spotify dataset using SQL, covering a wide range of queries from basic data retrieval to advanced aggregations and optimizations. The following key insights and takeaways were derived:

### 1. Data Exploration and Retrieval:

- We efficiently queried tracks, albums, and artist-related information, allowing us to retrieve high-stream tracks (e.g., tracks with over 1 billion streams) and list albums along with their respective artists.
- The project included various data exploration techniques, such as retrieving specific attributes (licensed tracks, album types) and grouping results based on artist and album characteristics.
  
### 2. Aggregations and Grouping:

- Using SQL aggregation functions, we calculated important metrics such as the total number of tracks per artist and average danceability per album. This helped identify patterns in track production and stylistic attributes across different albums.
- Window functions were leveraged to compute running totals (cumulative likes) and rank tracks based on views. This added more granularity and depth to the analysis.

## Key Achievements:

- **Insightful Metrics:** By aggregating and analyzing attributes such as views, streams, energy, and danceability, the project provided actionable insights into music trends and artist performance.
- **Advanced SQL Practices:** The project showcased the use of complex SQL operations like window functions, Common Table Expressions (CTEs), and aggregate functions, enhancing both analytical depth and SQL skill proficiency.
  
In conclusion, the Spotify SQL project was successful in combining fundamental SQL queries with advanced analytics techniques, offering meaningful insights while reinforcing optimization practices.
