
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
```
Select Count(*)
From `bigquery-public-data.austin_311.311_service_requests`

```

7. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
``` 
Select count(distinct(complaint_description))
From `bigquery-public-data.austin_311.311_service_requests`
```
  
8. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
``` 
Select owning_department as Department, count(owning_department) as deptcount
From `bigquery-public-data.austin_311.311_service_requests`
Group by owning_department
Order by deptcount Desc
```

9. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
```
Select complaint_description as Description, count(complaint_description) as complaint_count
From `bigquery-public-data.austin_311.311_service_requests`
Group by complaint_description
Order by complaint_count Desc
Limit 5
  ```
10. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
```
Select complaint_description as Description, count(complaint_description) as complaint_count
From `bigquery-public-data.austin_311.311_service_requests`
Where owning_department = 'Animal Services Office'
Group by complaint_description
Order by complaint_count Desc

```

11. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
```
Select unique_key, count(unique_key)
From `bigquery-public-data.austin_311.311_service_requests`
Group by unique_key
Having count(unique_key) > 1
```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
```
    Select census_2000.zipcode, census_2000.population as pop2000, census_2010.population as pop2010
From `bigquery-public-data.census_bureau_usa.population_by_zip_2000` as census_2000
  JOIN `bigquery-public-data.census_bureau_usa.population_by_zip_2010` as census_2010
  ON census_2000.zipcode =  census_2010.zipcode
  
```

### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd. 
```
    
SELECT advertiser_name, SUM(spend_usd) as total
FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
Group by advertiser_name
Order by total desc
Limit 1


```

2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
```
Tom Steyer
```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
```
2020-09-20
```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only. 
```
SELECT week_start_date, SUM(spend_usd) AS total
FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
WHERE week_start_date >= '2020-08-01' and week_start_date <'2020-09-01'
GROUP BY week_start_date 
order by week_start_date
```

6.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
```
50
```

7. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
```
WITH spent AS (
  SELECT advertiser_name, SUM(spend_usd) AS total
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name ),
  
  numads AS( SELECT advertiser_name, COUNT(advertiser_name) AS adscount
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name )
  
SELECT spent.advertiser_name, spent.total, numads.adscount
FROM spent
JOIN numads ON spent.advertiser_name = numads.advertiser_name
ORDER BY total desc

```
8. For each advertiser_name, find the average spend per ad. 
```
WITH spent AS (
  SELECT advertiser_name, SUM(spend_usd) AS total
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name ),
  
  numads AS( SELECT advertiser_name, COUNT(advertiser_name) AS adscount
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name )
SELECT spent.advertiser_name, spent.total/numads.adscount as avg_per_ad
FROM spent
JOIN numads ON spent.advertiser_name = numads.advertiser_name
ORDER BY total desc
```
10. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
``` 
WITH spent AS (
  SELECT advertiser_name, SUM(spend_usd) AS total
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name ),
  
  numads AS( SELECT advertiser_name, COUNT(advertiser_name) AS adscount
  FROM `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  GROUP BY advertiser_name )
SELECT spent.advertiser_name, spent.total/numads.adscount as avg_per_ad
FROM spent
JOIN numads ON spent.advertiser_name = numads.advertiser_name
WHERE spent.total/numads.adscount > 0
ORDER BY avg_per_ad


BRANDY K. CHAMBERS

```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
```
SELECT gender, count(*)
FROM bigquery-public-data.new_york_citibike.citibike_trips
Group by gender

males
```
2. What was the average, shortest, and longest bike trip taken in minutes?
```
SELECT AVG(tripduration) as avgtrip, MIN(tripduration) as mintrip, MAX(tripduration) as maxtrip
FROM bigquery-public-data.new_york_citibike.citibike_trips

avg - 962.4909855388895
min - 60
max - 19510049
```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
```
SELECT start_station_name as Station, start_count, end_count
FROM  (SELECT start_station_name, count(*) as start_count
  FROM bigquery-public-data.new_york_citibike.citibike_trips
  GROUP BY start_station_name) t1 left join (SELECT end_station_name, count(*) as end_count
  FROM bigquery-public-data.new_york_citibike.citibike_trips
  GROUP BY end_station_name) t2 on t1.start_station_name = t2.end_station_name


```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 

	https://colab.research.google.com/drive/1slcu0xDGH20XEnagyVNHkOYGRuw649fT
    
9. Complete the two questions in the colab notebook file. 
