# Spotify_Project_SQL

![Spotify Logo](https://github.com/najirh/najirh-Spotify-Data-Analysis-using-SQL/blob/main/spotify_logo.jpg)

## Overview
This project focuses on analyzing a Spotify dataset containing detailed information about tracks, albums, and artists using **SQL**. It involves the complete process of normalizing a denormalized dataset, executing SQL queries of varying complexity (ranging from basic to advanced), and optimizing query performance. The main objectives are to enhance advanced SQL proficiency and extract meaningful insights from the dataset.

```sql
DROP TABLE IF EXISTS spotify;
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
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);

DELETE FROM spotify
WHERE duration_min =0;
```
## Project Steps

### 1. Data Exploration
Before working with SQL, it's essential to thoroughly understand the dataset. It includes key attributes such as:
Artist – The performer of the track.
Track – The name of the song.
Album – The album the track belongs to.
Album_type – The type of album (e.g., single or full album).
Various musical metrics, including danceability, energy, loudness, tempo, and more.

### 4. Querying the Data
Once the data is inserted, various SQL queries can be written to explore and analyze it. These queries are grouped into **easy**, **medium**, and **advanced** levels, allowing for a structured and progressive development of SQL skills.

#### Easy Queries
- Basic data retrieval, filtering, and aggregation operations.
  
#### Medium Queries
- Advanced queries utilizing grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Complex queries incorporating nested subqueries, window functions, CTEs, and performance optimization techniques.

### 5. Query Optimization
In the later stages, the emphasis moves toward enhancing query performance. Key optimization techniques include:

**Indexing**: Creating indexes on columns that are frequently queried.
**Query Execution Plan**: Utilizing EXPLAIN ANALYZE to analyze and optimize the query's execution performance.
  
---


## 15 Practice Questions

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.
2. List all albums along with their respective artists.
3. Get the total number of comments for tracks where `licensed = TRUE`.
4. Find all tracks that belong to the album type `single`.
5. Count the total number of tracks by each artist.

### Medium Level
1. Calculate the average danceability of tracks in each album.
2. Find the top 5 tracks with the highest energy values.
3. List all tracks along with their views and likes where `official_video = TRUE`.
4. For each album, calculate the total views of all associated tracks.
5. Retrieve the track names that have been streamed on Spotify more than YouTube.
6. Find tracks where the energy-liveness ratio is greater than 1.2
### Advanced Level
1. Find the top 3 most-viewed tracks for each artist using window functions.
2. Write a query to find tracks where the liveness score is above the average.
3. Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.
4. Calculate the cumulative sum of likes for tracks for each artist using window functions.

### Easy Level
  1. Retrieve the names of all tracks that have more than 1 billion streams.

```sql
SELECT track
FROM spotify
WHERE stream > 1000000000;
``` 
  2. List all albums along with their respective artists.

```sql
SELECT DISTINCT album,artist
FROM spotify
ORDER BY 1;
``` 
3.Get the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT SUM(comments) AS total_comments
FROM spotify
WHERE licensed='true';
``` 
4.Find all tracks that belong to the album type `single`.
```sql
SELECT DISTINCT track
FROM spotify
WHERE album_type = 'single';
``` 
5.Count the total number of tracks by each artist.
```sql
SELECT COUNT(track) AS num_of_tracks,artist
FROM spotify
GROUP BY artist
ORDER BY 1 DESC;
```
### Medium Level

1. Calculate the average danceability of tracks in each album.

```sql
SELECT AVG(danceability) AS avg_danceability,track,album
FROM spotify
GROUP BY 2,3
ORDER BY 1 DESC;
```

2.Find the top 5 tracks with the highest energy values.
```sql
SELECT track
FROM (SELECT track,energy
FROM spotify
ORDER BY 2 DESC
LIMIT 5)
;

3.List all tracks along with their views and likes where `official_video = TRUE`.
```sql
SELECT track,
	   SUM(views) AS total_views,
	   SUM(likes) AS total_likes
FROM spotify
WHERE official_video ='true'
GROUP BY 1
ORDER BY 1;
```

4.For each album, calculate the total views of all associated tracks.
```sql
SELECT album,track, SUM(views) AS total_views
FROM spotify
GROUP BY 1,2
ORDER BY 3 DESC;
```

5.Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT *
FROM
(SELECT track,
	   COALESCE(SUM(CASE WHEN most_played_on ='Youtube' THEN stream END),0) AS youtube_stream_number,
	   COALESCE(SUM(CASE WHEN most_played_on ='Spotify' THEN stream END),0) AS spotify_stream_number
FROM spotify
GROUP BY 1) AS subquery
WHERE spotify_stream_number > youtube_stream_number AND youtube_stream_number!= 0;
```
6.Find tracks where the energy-liveness ratio is greater than 1.2
```sql
SELECT track,energy/liveness
FROM spotify
WHERE energy/liveness > 1.2;
```
### Advanced Level

1. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
 SELECT * 
FROM(
SELECT track,
artist,
DENSE_RANK()OVER(PARTITION BY artist ORDER BY SUM(views) DESC) AS rank_view,SUM(views)
FROM spotify
GROUP BY 1,2)
WHERE rank_view <=3;

```


2. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT track
FROM spotify
WHERE liveness >(SELECT AVG(liveness)
FROM spotify);

```

3. Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.
```sql
WITH energy_diff AS (
    SELECT album, MIN(energy) AS min_energy,MAX(energy) AS max_energy
    FROM spotify
    GROUP BY album
)
SELECT album,max_energy-min_energy AS energy_difference
FROM energy_diff
ORDER BY 2 DESC;

```

4.Calculate the cumulative sum of likes for tracks for each artist using window functions.

```sql
SELECT 
    artist,
    track,
    likes,
    SUM(likes) OVER (PARTITION BY artist ORDER BY likes DESC) AS cumulative_likes
FROM spotify;

```
