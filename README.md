# Spotify-Youtube-Project

<img width="1536" height="1024" alt="767d52ae-838f-41df-ad9c-79c394c90609" src="https://github.com/user-attachments/assets/00d04ea5-87d8-4d0e-8520-14edcd0217b1" />



# 🎵 Spotify & YouTube SQL Data Analysis Project

## 📌 Project Overview

This project demonstrates SQL-based data analysis using a Spotify and YouTube dataset. The objective is to analyze music streaming performance, artist popularity, album insights, and audience engagement using PostgreSQL.

The project covers SQL concepts from beginner to advanced, including filtering, aggregation, subqueries, Common Table Expressions (CTEs), window functions, conditional aggregation, and business reporting.

---

## 🎯 Objectives

- Analyze Spotify streaming performance.
- Identify top-performing artists, songs, and albums.
- Compare YouTube views, likes, and engagement.
- Generate business reports using SQL.
- Practice real-world SQL interview questions.

---

## 🛠️ Tools & Technologies

- PostgreSQL
- SQL
- pgAdmin 4
- CSV Dataset
- Git & GitHub

---

## 📂 Dataset Information(https://www.kaggle.com/datasets/salvatorerastelli/spotify-and-youtube)

The dataset contains Spotify and YouTube music information including:

- Artist
- Track
- Album
- Album Type
- Danceability
- Energy
- Loudness
- Tempo
- Duration
- Spotify Streams
- YouTube Views
- Likes
- Comments
- Licensed
- Official Video

---

## Project_Solutions(https://github.com/stej07033/Spotify-Youtube-Project/blob/main/project_spotify_sql.sql)

```sql

--- Spotify & Youtube_project

CREATE TABLE spotify_project (
    id INT PRIMARY KEY,
    artist VARCHAR(255),
    url_spotify TEXT,
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    uri TEXT,
    danceability DECIMAL(5,4),
    energy DECIMAL(5,4),
    key INT,
    loudness DECIMAL(6,3),
    speechiness DECIMAL(6,4),
    acousticness DECIMAL(6,4),
    instrumentalness DECIMAL(8,6),
    liveness DECIMAL(6,4),
    valence DECIMAL(6,4),
    tempo DECIMAL(8,3),
    duration_ms BIGINT,
    url_youtube TEXT,
    title TEXT,
    channel VARCHAR(255),
    views BIGINT,
    likes BIGINT,
    comments BIGINT,
    description TEXT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT
);

select * from spotify_project;

--- SELECT, WHERE, ORDER BY, LIMIT

---1. Display the Top 10 most streamed songs.

select track,
       artist,
	   stream
from spotify_project
order by stream desc
limit 10;

---2. Find songs with energy greater than 0.80, sorted by energy in descending order.

select track,
      artist,
	  energy
from spotify_project
where energy > 0.80
order by energy desc;

---3. Display the 5 longest songs.

select track,
       artist,
	   duration_ms
from spotify_project
order by duration_ms desc
limit 5;

---4. Find all songs by the artist Ed Sheeran.

select track,
       album,
	   views,
	   stream
from spotify_project
where Lower(artist) = 'Ed sheeran';


---5. Display the Top 10 songs with the highest YouTube likes.

select track,
       stream,
	   likes
from spotify_project
order by likes
limit 10;

---WHERE + Aggregate Functions + GROUP BY + HAVING 

---1.Find artists whose total Spotify streams are greater than 1 billion.

select artist,
       sum(stream) as total_stream
from spotify_project
where stream is not null
group by artist
having sum(stream) > 1000000000;
     
---2.Display albums that have an average danceability greater than 0.80.

select album,
         avg(danceability) as avg_danceability
from spotify_project 
where album is not null
group by album
having avg(danceability) > 0.80;

---3.Find YouTube channels that have more than 10 songs uploaded.

select channel,
        count(track) as total_track
from spotify_project
where channel is not null
group by channel
having count(track) > 10;

---4.Display artists who have released at least 5 songs and whose average energy is greater than 0.75.

select  artist,
       count(track) as total_track,
	   avg(energy) as avg_energry
from spotify_project
where artist is not null
group by artist
having count(track) >= 5
and avg(energy) > 0.75;
	  
---5.Find album types whose total YouTube views exceed 5 billion

select album_type,
         sum(views) as total_views
from spotify_project
where album_type is not null
group by album_type
having sum(views) > 5000000000;

---CASE Statement

---1. Categorize songs as High Energy, Medium Energy, or Low Energy.

select track,
       artist,
	   energy,
	case
	when energy >= 0.90 then 'High Energy'
	when energy >= 0.50 then 'Medium Energy'
	else 'Low Energy'
end as category_energy
from spotify_project;

---2. Classify songs as Hit, Popular, or Average based on streams.

select track,
       artist,
	   stream,
	 case 
	 when stream >= 1465980600 then 'Hit'
	 when stream >= 995490565 then 'Popular'
	 else 'Average'
end as classify_stream
from spotify_project;

---3. Display whether each song is Long or Short based on duration.

select track,
       stream,
	   duration_ms,
	case
	when duration_ms >= 300000 then 'Long'
	else 'Short'
end as classify_duration
from spotify_project;


---DISTINCT

---1. Display all unique artists.

select distinct artist
from spotify_project;

---2. Display unique album types.

select distinct album_type
from spotify_project

---3. Display unique YouTube channels.

select distinct channel
from spotify_project;

---Subqueries

---1. Find songs whose streams are greater than the average streams.

select track,
       artist,
	   stream
from spotify_project
where stream > (
          select avg(stream)
		  from spotify_project
);


---2. Find artists whose average likes are greater than the overall average.

select artist,
      avg(likes) as avg_likes
from spotify_project
group by artist
having avg(likes) > (
     select avg(likes) 
		  from spotify_project
);

---3 Display the artist having the maximum total stream.

select artist,
        sum(stream) as total_streams
from spotify_project
group by artist
having sum(stream) = (
   select max(total_streams)
   from (
select artist,
        sum(stream) as total_streams
from spotify_project
group by artist
   ) as artist_streams
 );


--- CTE (Common Table Expressions)

---1. Find the Top 10 artists based on total streams using a CTE.

with artist_streams as (
        select artist,
		    sum(stream) as total_streams
	from spotify_project
	where stream is not null
	group by artist
)

select artist,
       total_streams
from artist_streams
order by total_streams desc
limit 10;

---2. Display artists whose total views are above the average using a CTE.

with artist_views as (
         select artist,
		       sum(views) as total_views
		from spotify_project
		group by artist
)

select artist,
       total_views
from artist_views
where total_views > (
        select avg(total_views)
		from artist_views
);
          
---3. Find albums with the highest average likes using a CTE.

with album_likes as (
          select album,
		         avg(likes) as avg_likes
		from spotify_project
		where likes is not null
		group by album
)

select album,
       avg_likes
from album_likes
where avg_likes > (
           select max(avg_likes)
		   from album_likes
);


---Window Functions

---1. Rank songs based on Spotify streams.

select artist,
       track,
	   stream,
	rank() over(order by stream desc) as song_rank
from spotify_project
where stream is not null;

---2.Display the Top 3 songs for each artist using ROW_NUMBER().

select * from(
         select artist,
		         track,
				 stream,
			row_number() over(partition by artist order by stream desc) as row_num
	from spotify_project
) as ranked_songs
where row_num <= 3;

---3. Rank artists by total streams using DENSE_RANK().

select artist,
      total_streams,
	  dense_rank() over(order by total_streams desc) as artist_rank
from(
      select artist,
	        sum(stream) as total_streams
		from spotify_project
		where stream is not null
		group by artist
	)as artist_totals;

---4. Display the second most viewed song using RANK().

select track,
       artist,
	   views
from (
  select track,
         artist,
		 views,
		 rank() over(order by views desc) as view_rank
from spotify_project
where views is not null
) as ranked_views
where view_rank = 2;

---Business Reporting

---1. Generate an artist performance report showing:
--Artist Name
--Total Songs
--Total Streams
--Total YouTube Views
--Total Likes
--Average Danceability
--Average Energy
--Rank based on Total Streams

select 
    artist as artist_name,
	count(track) as total_tracks,
	sum(stream) as total_streams,
	sum(views) as total_youtube_views,
	sum(likes) as total_likes,
	avg(danceability) as avg_danceability,
	avg(energy) as avg_enegry,
	dense_rank() over(order by sum(stream) desc ) as stream_rank
from spotify_project
where stream is not null
  and views is not null
  and likes is not null
group by artist
order by stream_rank;
```



## 📋 SQL Concepts Covered

- SELECT
- WHERE
- ORDER BY
- LIMIT
- Aggregate Functions (SUM, AVG, COUNT, MAX, MIN)
- GROUP BY
- HAVING
- CASE Statements
- DISTINCT
- Subqueries
- Common Table Expressions (CTEs)
- Window Functions
  - ROW_NUMBER()
  - RANK()
  - DENSE_RANK()
- Conditional Aggregation
- Business Reporting

---

## 📊 Business Problems Solved

This project includes 40 real-world SQL business problems such as:

- Top streamed songs
- Top performing artists
- Album performance analysis
- YouTube engagement analysis
- Energy and danceability analysis
- Licensed vs Non-Licensed songs
- Official vs Non-Official videos
- Artist performance reporting
- Ranking artists and songs
- Business dashboards using SQL

---

## 📁 Project Structure

```
Spotify-SQL-Project/
│
├── spotify_project.sql
├── spotify_dataset.csv
├── README.md
└── screenshots/
```

---

## 📈 Sample Business Report

The final report includes:

- Artist Name
- Total Songs
- Total Spotify Streams
- Total YouTube Views
- Total Likes
- Average Danceability
- Average Energy
- Artist Ranking

---

## 🚀 Key Learning Outcomes

- Database creation and management
- Data cleaning and querying
- Advanced SQL techniques
- Business analytics using SQL
- Query optimization
- Reporting with Window Functions and CTEs

---
## 👩‍💻 Author

### **SAI M**

**Aspiring Data Analyst | SQL & PostgreSQL Enthusiast**

### 💼 Skills

- SQL
- PostgreSQL
- Python
- Microsoft Excel
- Data Cleaning
- Data Analysis
- Data Visualization
- Business Analytics
- Git & GitHub

### 📬 Connect with Me

- **GitHub:** https://github.com/stej07033
- **LinkedIn:** https://www.linkedin.com/in/madanapalli-sai-19b835389
- **Resume:** https://drive.google.com/file/d/1XADghy4C0PWW03jkAkpOiRHkFuLIOXwR/view?usp=sharing


