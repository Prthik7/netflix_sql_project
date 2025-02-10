# Netflix Movies and TV Shows Data Analysis using SQL
[Netflix Logo]{https://github.com/Anna464/netflix_sql_project/blob/main/netflix%20image}



#Objective
--NETFLIX PROJECT
drop table if exists netflix;
create table netflix
(

    show_id VARCHAR(10),
    type VARCHAR(10),
    title VARCHAR(250),
    director VARCHAR(550),
    casts  VARCHAR(1050),
    country VARCHAR(550),
    date_added VARCHAR(55),
    release_year INT,
    rating VARCHAR(15),
    duration VARCHAR(15),
    listed_in VARCHAR(250),
    description VARCHAR(550)
);  

select * from netflix;


select 
count(*)as total_count 
from netflix;

select 
 distinct type
from netflix

--15 business problems
-- Count the Number of Movies vs TV Shows
select type,
count(* )as total_content
from netflix
group by 1

--Find the Most Common Rating for Movies and TV Shows
select
 type,
 rating
from
(select 
type,
rating,
count(*),
rank()over(partition by type order by count(*)desc)as ranking
from netflix
group by 1,2)as t1
where ranking=1

--List All Movies Released in a Specific Year (e.g., 2020)
select * from netflix
where type='Movie'and release_year=2020;

--Find the Top 5 Countries with the Most Content on Netflix
select 
unnest(string_to_array(country,','))as new_country,
count(show_id) as total_content
from netflix
group by 1
order by 2 desc
limit 5

--Identify the Longest Movie
select * from netflix
where type='Movie' and duration=(select max(duration) from netflix)

--Find Content Added in the Last 5 Years
select 
*
from netflix
where
    to_date(date_added,'Month DD,YYYY')>= current_date - interval '5 years'

--Find All Movies/TV Shows by Director 'Rajiv Chilaka'
select * from netflix
where  director like'%Rajiv Chilaka%'

--List All TV Shows with More Than 5 Seasons

select
* 
from netflix
where 
type='TV Show'
and 
split_part(duration,' ',1)::numeric> 5

--Count the Number of Content Items in Each Genre
select 
unnest(string_to_array(listed_in,','))as genre,
count(show_id) as total_content
from netflix
group by 1

--Find each year and the average numbers of content release in India on netflix.
--return top 5 year with highest avg content release!
select 
extract(year from to_date(date_added,'Month DD,YYYY'))as year,
count(*),
round(count(*)::numeric/(select count(*)from netflix where country='India')::numeric * 100,2)as avg_content_per_year
from netflix
where country='India'
group by 1

--List All Movies that are Documentaries
select * from netflix 
where listed_in like 'Documentaries'

--Find All Content Without a Director
select * from netflix
where director is  null


--Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
select * from netflix
where casts ilike '%Salman khan%' 
and 
release_year > extract(year from current_date)-10

--Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
select
unnest(string_to_array(casts,','))as actors,
count(*) as total_content
from netflix
where country ilike 'India'
group by 1
order by 2 desc
limit 10


--Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords  Categorize content as 'Bad' if it contains
--'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.
with new_table
as
(
select 
*,
case
when 
description ilike '%kill%' or
description ilike '%violence%' then 'bad_film'
else 'good film'
end category
from netflix
)
select category,
count(*)as total_content from new_table
group by 1











