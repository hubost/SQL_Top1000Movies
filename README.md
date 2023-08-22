<h1>Queries and functions of IMDB Top 1000 Movies Dataset</h1>
<h2> 
🔶Queries and functions made in SQL code in PostgreSQL RDBMS. <br>
🔶Imported from excel using CONCATENATE <br>
🔶Inserts available at Excel file on R column cells. <br>
🔶Dataset downloaded from Kaggle <br> (https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows) <br> <br>  </h2> 

<h2> 🟡Creating table <br> </h2>

CREATE TABLE IF NOT EXISTS public.imdb  <br>
( <br>
    series_title text  , <br>
    released_year integer,  <br>
    certificate text  , <br>
    runtime_in_mins numeric  ,  <br>
    genre text  , <br>
    imdb_rating numeric, <br>
    overview text   , <br>
    meta_score integer, <br>
    director text   , <br>
    star1 text  , <br>
    star2 text  , <br>
    star3 text   , <br>
    star4 text  , <br>
    no_of_votes integer, <br>
    gross integer  <br>
    ); 
![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/c80cc723-77c6-4f3a-9cec-4a81ad9301ae)


<h2> 🟡 Analysis of the gross of a movie vs directors. <br> </h2>

select director, avg(gross)::integer, max(gross), min(gross),sum(gross),count(gross) as movies_in_top1000 from imdb <br>
WHERE gross is not null <br>
group by director <br>
order by count(gross) desc <br>
![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/77f2b1cc-de56-4f03-8678-399c023db7a9)

<h2> 🟡 Analysis of the gross of a movie vs different - different stars. <br> </h2>

select series_title, avg(gross)::integer as avg_gross, max(gross) as max_gross, <br>
min(gross) as min_gross, sum(gross) as total_gross,  actor <br>
from ( <br>
        select series_title, gross, star1 as actor from imdb <br>
        union all  <br>
        select series_title, gross, star2 as actor from imdb <br>
        union all <br>
        select series_title, gross, star3 as actor from imdb <br>
        union all <br>
        select series_title, gross, star4 as actor from imdb <br>
    ) actors <br>
     where actor ilike '%leonardo%' <br>
     group by actor, series_title <br>
     having avg(gross) is not null<br>
     order by avg(gross) desc<br>
  ![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/386cfc42-d4f2-4108-9e62-fe7bf6d69792)



<h2> 🟡 Analysis of the No_of_votes of a movie vs directors. <br></h2>

select director, avg(no_of_votes)::integer as average_no_of_votes, max(no_of_votes),min(no_of_votes), sum(no_of_votes), count(no_of_votes) as movies_count from imdb <br> 
where director is not null and director not ilike '' <br>
and no_of_votes is not null <br>
group by director <br>
 
![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/4863f8bb-b285-4093-869e-c2aa2a1fdb80)


<h2> 🟡 Analysis of the No_of_votes of a movie vs different - different stars. <br></h2>

select actor, avg(no_of_votes)::integer as avg_votes, max(no_of_votes) as max_votes, <br>
min(no_of_votes) as min_votes, sum(no_of_votes) as total_votes, count(no_of_votes) as movies <br>
from ( select series_title, no_of_votes, star1 as actor from imdb <br>
        union all <br>
        select series_title, no_of_votes, star2 as actor from imdb <br>
        union all <br>
        select series_title, no_of_votes, star3 as actor from imdb<br>
        union all <br>
        select series_title, no_of_votes, star4 as actor from imdb<br>
     ) actors<br>
     where actor is not null and actor not ilike ''<br>
     group by actor<br>
     order by total_votes desc<br>
    
![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/89113f16-fddc-4011-a272-3d3c5bfd4f1f)

<h2> 🟡 Which actor prefer which Genre more? <br></h2>

select actor,genre as favorite_genre from<br>
    (<br>
        select star1 AS actor, genre, ROW_NUMBER() OVER (PARTITION BY star1 ORDER BY COUNT(*) DESC) AS rn FROM imdb<br>
        GROUP BY star1, genre   <br>
        UNION <br>
        select star2 AS actor, genre, ROW_NUMBER() OVER (PARTITION BY star2 ORDER BY COUNT(*) DESC) AS rn FROM imdb<br>
        GROUP BY star2, genre <br>
        UNION <br>
        select star3 AS actor, genre, ROW_NUMBER() OVER (PARTITION BY star3 ORDER BY COUNT(*) DESC) AS rn FROM imdb<br>
        GROUP BY star3, genre <br>
        UNION <br>
        select star4 AS actor, genre, ROW_NUMBER() OVER (PARTITION BY star4 ORDER BY COUNT(*) DESC) AS rn FROM imdb<br>
        GROUP BY star4, genre ) as actors<br>
        where rn=1;<br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/8daf6009-75dd-4d4b-ad91-e6e781d4e5ed)

<h2> 🟡 Which combination of actors are getting good IMDB_Rating maximum time?<br></h2>

select star1,star2,star3,star4, imdb_rating from imdb<br>
where '' not in (star1,star2,star3,star4)<br>
group by star1,star2,star3,star4, imdb_rating<br>
order by imdb_rating desc limit 10 <br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/642454d1-bfd3-405a-acc7-b292ecb3a9b2)

<h2> 🟡 Which combination of actors are getting good gross?<br></h2>

select CONCAT(star1,' ',star2,' ',star3,' ',star4), gross from imdb<br>
where ''not in (star1, star2, star3, star4)<br>
and gross is not null <br>
order by gross desc<br>
limit 10<br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/0b5fd38a-6610-45a7-9ba2-ffbb8e5d89d3)

<h2> 🟡 Which genres are making best gross for directors? <br></h2>

select distinct director, genre, max(gross) as highest_gross from imdb<br>
where gross is not null<br>
group by gross, director, genre<br>
order by max(gross) desc<br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/37d1be05-d805-46a4-a8ec-bef3d948f7a3)



<h2> 🟡 Analysis of movie runtime over the years <br></h2>

select '1930-1950'as years,avg(runtime_in_mins)::integer as runtime_mins_avg from imdb <br>
    where released_year between 1930 and 1950<br>
    UNION<br>
    select '1951-1970'as years,avg(runtime_in_mins)::integer as runtime_mins_avg from imdb <br>
    where released_year between 1951 and 1970<br>
    UNION<br>
    select '1971-1990'as years,avg(runtime_in_mins)::integer as runtime_mins_avg from imdb <br>
    where released_year between 1971 and 1990<br>
    UNION<br>
    select '1991-2010'as years,avg(runtime_in_mins)::integer as runtime_mins_avg from imdb <br>
    where released_year between 1991 and 2010<br>
    UNION<br>
    select '2011-2020'as years,avg(runtime_in_mins)::integer as runtime_mins_avg from imdb <br>
    where released_year between 2011 and 2020<br>
    order by years<br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/e6427ab1-253d-455d-8676-3488a838576f)

<h2> 🟡 Analysis of average gross over the years <br></h2>

SELECT released_year,sum(gross) as sum_gross,avg(gross)::integer as avg_gross, count(series_title) as total_movies from imdb<br>
where gross is not null and released_year is not null<br>
group by released_year<br>
order by released_year desc<br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/304b8c06-40d0-422b-b019-cc8e7ce662bc)


<h1> ▶️ Functions <br></h1>

<h2>Search by actor<h2>
<h3>select * from search_by_dir_or_actor('','Al Pacino')</h3>

CREATE OR REPLACE FUNCTION search_by_dir_or_actor(director_s text, actor text) <br>
RETURNS TABLE (searched_movie text, searched_dir text, actors text) <br>
LANGUAGE plpgsql AS $$ <br>
BEGIN <br>
    IF (director_s != '') THEN <br>
        RETURN QUERY <br>
            SELECT series_title, director, CONCAT(star1,' ',star2,' ',star3,' ',star4) <br>
            FROM imdb <br>
            WHERE director ILIKE CONCAT('%', director_s, '%'); <br>
    ELSIF(actor != '') THEN <br>
        RETURN QUERY <br>
            SELECT series_title, director, CONCAT(star1,' ',star2,' ',star3,' ',star4) <br>
            FROM imdb <br>
            WHERE actor IN (star1, star2, star3, star4) AND actor NOT ILIKE ''; <br>
    ELSE <br>
        RETURN QUERY <br>
            SELECT series_title, director, CONCAT(star1,' ',star2,' ',star3,' ',star4) <br>
            FROM imdb <br>
            WHERE director ILIKE CONCAT('%', director_s, '%') <br>
            AND actor IN (star1, star2, star3, star4) AND actor NOT ILIKE ''; <br>
    END IF; <br>
END; <br>
$$; <br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/35ecec3c-28ec-4d7c-9385-0f73a6c4f14b)

<h2>Search by director<h2>
<h3>select * from search_by_dir_or_actor('Tarantino','')</h3>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/666450a0-f056-43fd-9d1e-917cc0769fda)


<h2>Get movie gross<h2>
<h3>select get_movie_gross('Inception')</h3>

Create or replace function get_movie_gross(searched_movie text) <br>
returns int <br>
language plpgsql <br>
as $$ <br>
	Declare movie_gross integer; <br>
	Begin <br>
		select gross into movie_gross from imdb <br>
		where series_title ilike searched_movie; <br>
		return movie_gross; <br>
		end; <br>
$$ <br>

![image](https://github.com/hubost/SQL_Top1000Movies/assets/103451733/36837540-43cf-41c9-9563-30fa809b794d)
