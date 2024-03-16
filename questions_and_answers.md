
##Classic American names
1. Find names that have been given to over 5,000 babies of either sex every year for the 101 years from 1920 through 2020;
```
select first_name, sum(num) 
from baby_names
group by first_name
having count(year) =101
order by sum(num) desc
```
Results: 
| first_name | sum     |
|------------|---------|
| James      | 4748138 |
| John       | 4510721 |
| William    | 3614424 |
| David      | 3571498 |
| Joseph     | 2361382 |
| Thomas     | 2166802 |
| Charles    | 2112352 |
| Elizabeth  | 1436286 |
## Timeless or trendy?
2. Classify all names in the dataset as 'Classic,' 'Semi-classic,' 'Semi-trendy,' or 'Trendy' based on whether the name appears in the dataset more than 80, 50, 20, or 0 times, respectively.   
```
select
first_name , sum(num),
case when count(num) > 80 then 'Classic'
    when count(num) < 80 and count(num) > 50 then 'Semi-classic'
    when count(num) < 50 and count(num) > 20 then 'Semi-trendy'
    else 'Trendy' end as popularity_type
from baby_names
group by first_name 
order by first_name 
```
## Top-ranked female names since 1920
3. Let's take a look at the ten highest-ranked American female names in our dataset.
```
select 
rank() over(order by sum(num) desc) as name_rank , 
first_name , 
sum(num)
from baby_names
where sex = 'F'
group by first_name
limit 10;
```
## Picking a baby name
4. Return a list of first names which meet this friend's baby name criteria and Filter the data for results where sex equals 'F', the year is greater than 2015, and the first_name ends in an 'a.'
```
SELECT 
    first_name
FROM 
    baby_names
where sex = 'F' AND year > '2015' AND  first_name LIKE '%a'
group by first_name
order by sum(num) desc
```
 ## The Olivia expansion
5.Find the cumulative number of babies named Olivia over the years since the name first appeared in our dataset.
```
SELECT 
    year,
    first_name,
    sum(num) AS num,
    SUM(SUM(num)) OVER (ORDER BY year) AS cumulative_olivias
FROM 
    baby_names
WHERE 
    first_name LIKE '%Olivia'
GROUP BY 
    year, first_name
ORDER BY 
    year;
```
## Many males with the same name
6. Write a query that selects the year and the maximum num of babies given any male name in that year.
```
select year, max(num) as max_num
from baby_names
where sex ='M'
group by year
```
## Top male names over the years
7.  the maximum number of babies given a specific male name in a year.
```
SELECT 
    a.year,
    a.first_name,
    a.num
FROM 
    baby_names as a
join 
    (select  year, max(num) as max_num 
    from baby_names 
    where sex ='M'
    group by year
    ) as b
on b.year=a.year
and b.max_num = a.num
order by year desc;
```
## The most years at number one
8. Return a list of first names that have been the top male first name in any year along with a count of the number of years that name has been the top name
```
with jai as(
    SELECT 
    a.year,
    a.first_name,
    a.num
FROM 
    baby_names as a
join 
    (select  year, max(num) as max_num 
    from baby_names 
    where sex ='M'
    group by year
    ) as b
on b.year=a.year
and b.max_num = a.num
order by year desc
)

SELECT 
    first_name,
    count(first_name) as count_top_name
FROM jai
    
group by first_name
order by COUNT(first_name) desc  ;
```

