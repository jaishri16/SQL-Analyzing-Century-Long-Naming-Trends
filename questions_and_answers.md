
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
Results:
547 rows affected.
| first_name | sum    | popularity_type |
|------------|--------|-----------------|
| Aaliyah    | 15870  | Trendy          |
| Aaron      | 530592 | Semi-classic    |
| Abigail    | 338485 | Semi-trendy     |
| Adam       | 497293 | Semi-trendy     |
| Addison    | 107433 | Trendy          |
| Adrian     | 147741 | Semi-trendy     |
| Aidan      | 68566  | Trendy          |
| Aiden      | 216194 | Trendy          |
| Alan       | 162041 | Semi-trendy     |

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
Results:
| name_rank | first_name | sum     |
|-----------|------------|---------|
| 1         | Mary       | 3215850 |
| 2         | Patricia   | 1479802 |
| 3         | Elizabeth  | 1436286 |
| 4         | Jennifer   | 1404743 |
| 5         | Linda      | 1361021 |
| 6         | Barbara    | 1343901 |
| 7         | Susan      | 1025728 |
| 8         | Jessica    | 994210  |
| 9         | Lisa       | 920119  |
| 10        | Betty      | 893396  |
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
Results:
19 rows affected.
| first_name |
|------------|
| Olivia     |
| Emma       |
| Ava        |
| Sophia     |
| Isabella   |
| Mia        |
| Amelia     |
| Ella       |
| Sofia      |
| Camila     |
| Aria       |
| Victoria   |
| Layla      |
| Nora       |

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
Results:
30 rows affected
| year | first_name | num  | cumulative_olivias |
|------|------------|------|--------------------|
| 1991 | Olivia     | 5601 | 5601               |
| 1992 | Olivia     | 5809 | 11410              |
| 1993 | Olivia     | 6340 | 17750              |
| 1994 | Olivia     | 6434 | 24184              |

## Many males with the same name
6. Write a query that selects the year and the maximum num of babies given any male name in that year.
```
select year, max(num) as max_num
from baby_names
where sex ='M'
group by year
```
Results:
101 rows affected.
| year | max_num |
|------|---------|
| 1970 | 85291   |
| 2000 | 34483   |
| 1947 | 94764   |
| 1962 | 85041   |
| 1975 | 68451   |
| 1980 | 68704   |
| 1931 | 60518   |

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
Results:
101 rows affected.
| year | first_name | num   |
|------|------------|-------|
| 2020 | Liam       | 19659 |
| 2019 | Liam       | 20555 |
| 2018 | Liam       | 19924 |
| 2017 | Liam       | 18824 |
| 2016 | Noah       | 19154 |
| 2015 | Noah       | 19650 |

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
```
Results:
8 rows affected.
| first_name | count_top_name |
|------------|----------------|
| Michael    | 44             |
| Robert     | 17             |
| Jacob      | 14             |
| James      | 13             |
| Noah       | 4              |
| John       | 4              |
| Liam       | 4              |
| David      | 1              |

order by COUNT(first_name) desc  ;
```

