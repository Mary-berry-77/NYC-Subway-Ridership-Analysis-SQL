
# NYC Subway Ridership Analysis SQL

## Overview
This project analyzes the New York City subway ridership data to identify trends, duplicates, and fluctuations in usage across various stations and routes. 
The data is sourced from the BigQuery public dataset: [New York Subway Ridership](https://bigquery.cloud.google.com/dataset/bigquery-public-data:new_york_subway).

## Analysis Steps

### 1. Identifying Duplicate Station Names
This query identifies station names that appear more than once in the dataset and counts the distinct routes associated with each station.

```SQL

WITH duplicate_table AS (
  SELECT station_name
  FROM bigquery-public-data.new_york_subway.subway_ridership_2013_present
  GROUP BY station_name
  HAVING COUNT(station_name) > 1
)

SELECT
  tb.station_name,
  COUNT(DISTINCT tb.routes) AS route_count
FROM 
  bigquery-public-data.new_york_subway.subway_ridership_2013_present AS tb
INNER JOIN
  duplicate_table AS dt
ON 
  tb.station_name = dt.station_name
GROUP BY 
  tb.station_name
ORDER BY
  route_count ASC;

```


### 2. Analyzing Year-on-Year Changes
This query calculates the year-on-year changes in ridership for each station and route, helping to determine if ridership has increased or decreased over the years.

```SQL

SELECT 
  station_name,
  routes,
  ridership_2014 - ridership_2013 AS change_2014,
  ridership_2015 - ridership_2014 AS change_2015,
  ridership_2016 - ridership_2015 AS change_2016,
  ridership_2017 - ridership_2016 AS change_2017,
  ridership_2018 - ridership_2017 AS change_2018
FROM 
  bigquery-public-data.new_york_subway.subway_ridership_2013_present
ORDER BY
  change_2018 DESC;
```

### 3. Analyzing Total Year-on-Year Changes
This query sums the year-on-year changes to identify stations and routes with the greatest fluctuations in ridership.

```SQL

WITH change_ridership AS (
  SELECT 
    station_name,
    routes,
    ridership_2014 - ridership_2013 AS change_2014,
    ridership_2015 - ridership_2014 AS change_2015,
    ridership_2016 - ridership_2015 AS change_2016,
    ridership_2017 - ridership_2016 AS change_2017,
    ridership_2018 - ridership_2017 AS change_2018
  FROM 
    bigquery-public-data.new_york_subway.subway_ridership_2013_present
)

SELECT
  station_name,
  routes,
  (change_2014 + change_2015 + change_2016 + change_2017 + change_2018) AS total_change
FROM 
  change_ridership
ORDER BY 
  total_change DESC;
```

## Conclusion
The queries in this repository provide insights into the NYC subway system's ridership patterns, helping to identify potential areas for improvement and further research.

Feel free to adjust any wording to better fit your style or add any additional sections you think are necessary!

