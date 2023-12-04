# SQL_IPL_database_between_2008_and_2020
1. Create a table named ‘matches’ with appropriate data types for columns
2. Create a table named ‘deliveries’ with appropriate data types for columns
3. Import data from csv file ’IPL_matches.csv’ attached in resources to the table ‘matches’ which was created in Q1
4. Import data from csv file ’IPL_Ball.csv’ attached in resources to the table ‘deliveries’ which was created in Q2
5. Select the top 20 rows of the deliveries table after ordering them by id, inning, over, ball in ascending order.
6. Select the top 20 rows of the matches table.
7. Fetch data of all the matches played on 2nd May 2013 from the matches table..
8. Fetch data of all the matches where the result mode is ‘runs’ and margin of victory is more than 100 runs.
9. Fetch data of all the matches where the final scores of both teams tied and order it in descending order of the date.
10. Get the count of cities that have hosted an IPL match.
11. Create table deliveries_v02 with all the columns of the table ‘deliveries’ and an additional column ball_result containing values boundary, dot or other depending on the total_run (boundary for >= 4, 
    dot for 0 and other for any other number)
12. Write a query to fetch the total number of boundaries and dot balls from the deliveries_v02 table.
13. Write a query to fetch the total number of boundaries scored by each team from the deliveries_v02 table and order it in descending order of the number of boundaries scored.
14. Write a query to fetch the total number of dot balls bowled by each team and order it in descending order of the total number of dot balls bowled.
15. Write a query to fetch the total number of dismissals by dismissal kinds where dismissal kind is not NA
16. Write a query to get the top 5 bowlers who conceded maximum extra runs from the deliveries table
17. Write a query to create a table named deliveries_v03 with all the columns of deliveries_v02 table and two additional column (named venue and match_date) of venue and date from table matches
18. Write a query to fetch the total runs scored for each venue and order it in the descending order of total runs scored.
19. Write a query to fetch the year-wise total runs scored at Eden Gardens and order it in the descending order of total runs scored.
20. Get unique team1 names from the matches table, you will notice that there are two entries for Rising Pune Supergiant one with Rising Pune Supergiant and another one with Rising Pune Supergiants.  Your 
    task is to create a matches_corrected table with two additional columns team1_corr and team2_corr containing team names with replacing Rising Pune Supergiants with Rising Pune Supergiant. Now analyse 
    these newly created columns.
21. Create a new table deliveries_v04 with the first column as ball_id containing information of match_id, inning, over and ball separated by ‘-’ (For ex. 335982-1-0-1 match_id-inning-over-ball) and rest 
    of the columns same as deliveries_v03)
22. Compare the total count of rows and total count of distinct ball_id in deliveries_v04;
23. SQL Row_Number() function is used to sort and assign row numbers to data rows in the presence of multiple groups. For example, to identify the top 10 rows which have the highest order amount in each 
    region, we can use row_number to assign row numbers in each group (region) with any particular order (decreasing order of order amount) and then we can use this new column to apply filters. Using this 
    knowledge, solve the following exercise. You can use hints to create an additional column of row number.
    Create table deliveries_v05 with all columns of deliveries_v04 and an additional column for row number partition over ball_id. (HINT : Syntax to add along with other columns,  row_number() over 
    (partition by ball_id) as r_num)
24. Use the r_num created in deliveries_v05 to identify instances where ball_id is repeating. (HINT : select * from deliveries_v05 WHERE r_num=2;)
25. Use subqueries to fetch data of all the ball_id which are repeating. (HINT: SELECT * FROM deliveries_v05 WHERE ball_id in (select BALL_ID from deliveries_v05 WHERE r_num=2);




1. create table matches(
id int,
city varchar,
date DATE,
player_of_match varchar,
venue varchar,
neutral_venue varchar,
team1 varchar,
team2 varchar,
toss_winner varchar,
toss_decision varchar,
winner varchar,
result varchar,
result_margin varchar,
eliminator varchar,
method varchar,
umpire1 varchar,
umpire2 varchar);

2. create table deliveries(
id integer,
inning int,
over int,
ball int,
batsman varchar,
non_striker varchar,
bowler varchar,
batsman_runs int,
extra_runs int,
total_runs int,
is_wicket int,
dismissal_kind varchar,
player_dismissed varchar,
fielder varchar,
extras_type varchar,
batting_team varchar,
bowling_team varchar);

3. copy matches
from 'C:\Program Files\PostgreSQL\16rc1\IPLMatches+IPLBall\IPL_matches.csv'
delimiter ',' csv header;

4. copy deliveries
from 'C:\Program Files\PostgreSQL\16rc1\IPLMatches+IPLBall\IPL_Ball.csv'
delimiter ',' csv header;

5. select *
from deliveries 
order by id asc,
inning asc,
over asc,
ball asc
limit 20;

6. select *
from matches 
order by id asc,
inning asc,
over asc,
ball asc
limit 20;

7. select * from matches
where date='2013-05-02';

8. select * from matches 
where result='runs'and result_margin>'100';

9. select * from matches where result='tie'
order by date desc;

10. select count(distinct city)
from matches;

11. create table deliveries_v02 as select *,
case when total_runs>=4 then 'boundary'
when total_runs=0 then 'dot'
else 'other'
end as ball_result
from deliveries 
order by inning asc,over asc,ball asc;

12. select * from deliveries_v02
where not ball_result='other';

13. select batting_team,
count(ball_result)as "Number of Boundaries"
from deliveries_v02
where ball_result= 'boundary'
group by batting_team
order by "Number of Boundaries" desc;

14. select bowling_team,
count(ball_result) as "Number of Dot Balls Bowled"
from deliveries_v02
where ball_result='dot'
group by bowling_team
order by "Number of Dot Balls Bowled" desc;

15. select dismissal_kind,
count(dismissal_kind) as "Number of dismissals"
from deliveries
where not dismissal_kind='NA'
group by dismissal_kind
order by "Number of dismissals" desc;

16. select bowler,
sum(extra_runs) as "Extra Runs Conceded"
from deliveries 
group by bowler
order by "Extra Runs Conceded"desc
limit 5;

17. create table deliveries_v03 as
select a.*,
b.venue,
b.date
from deliveries_v02 as a
left join matches as b
on
a.id=b.id
order by a.id;

18. select venue,
sum(total_runs) as "Total Runs Scored"
from deliveries_v03
group by venue
order by "Total Runs Scored" desc;

19. select venue,
sum(total_runs) as "Total Runs Scored"
from deliveries_v03
group by venue
order by "Total Runs Scored" desc;

20. create table matches_corrected1
as select *,
replace(team1,'Rising Pune Supergiants' , 'Rising Pune Supergiant') as team1_corr,
replace(team2,'Rising Pune Supergiants','Rising Pune Supergiant') as team2_corr
from matches ;

21.create table deliveries_v04 
as select *,
concat(id||'-'||inning||'-'||over||'-'||ball) 
as ball_id from deliveries_v03;
select * from deliveries_v04;

22. select count(*),
count(distinct ball_id)as ball_id
from deliveries_v04;

23. create table deliveries_v05 as select *,
row_number() over (partition by  ball_id) as r_num from deliveries_v04;

24. select * from deliveries_v05
where  r_num = '1';

25. select* from deliveries_v05
where ball_id in (select ball_id from deliveries_v05 where r_num = '1');
