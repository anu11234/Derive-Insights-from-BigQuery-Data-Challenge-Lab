# Derive Insights from BigQuery Data: Challenge Lab || **GSP787**

**Command:**

**Task 1**
```bash
SELECT
  SUM(cumulative_confirmed) AS total_cases_worldwide
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  date = '<DATE>';
```
**Task 2**
```bash
SELECT
  COUNT(*) AS count_of_states
FROM (
  SELECT
    subregion1_name,
    SUM(cumulative_deceased) AS total_deaths
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name = "United States of America"
    AND date = '<DATE>'
    AND subregion1_name IS NOT NULL
  GROUP BY
    subregion1_name
  HAVING
    total_deaths > <DEATH_COUNT>
);
```
**Task 3**
```bash
SELECT
  subregion1_name AS state,
  SUM(cumulative_confirmed) AS total_confirmed_cases
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  country_code = "US"
  AND date = '<DATE>'
  AND subregion1_name IS NOT NULL
GROUP BY
  subregion1_name
HAVING
  total_confirmed_cases > <CONFIRMED_CASES>
ORDER BY
  total_confirmed_cases DESC;
```

**Task 4**
```bash
SELECT
  SUM(cumulative_confirmed) AS total_confirmed_cases,
  SUM(cumulative_deceased) AS total_deaths,
  (SUM(cumulative_deceased) / SUM(cumulative_confirmed)) * 100 AS case_fatality_ratio
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  country_name = "Italy"
  AND date BETWEEN '<START_DATE>' AND '<END_DATE>';
```
**Task 5**
```bash
 SELECT
  date
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  country_name = "Italy"
GROUP BY
  date
HAVING
  SUM(cumulative_deceased) > <DEATH_COUNT>
ORDER BY
  date ASC
LIMIT 1;

```

**Task 6**
```bash
WITH india_cases_by_date AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name = "India"
    AND date BETWEEN '<START_DATE>' AND '<CLOSE_DATE>'
  GROUP BY
    date
  ORDER BY
    date ASC
),
india_previous_day_comparison AS (
  SELECT
    date,
    cases,
    LAG(cases) OVER(ORDER BY date) AS previous_day,
    cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
  FROM
    india_cases_by_date
)
SELECT
  COUNT(*)
FROM
  india_previous_day_comparison
WHERE
  net_new_cases = 0;
```

**Task 7**
```bash
WITH us_cases AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name = "United States of America"
    AND date BETWEEN '2020-03-22' AND '2020-04-20'
  GROUP BY
    date
  ORDER BY
    date ASC
),
us_previous_day_comparison AS (
  SELECT
    date AS Date,
    cases AS Confirmed_Cases_On_Day,
    LAG(cases) OVER(ORDER BY date) AS Confirmed_Cases_Previous_Day,
    ((cases - LAG(cases) OVER(ORDER BY date)) / LAG(cases) OVER(ORDER BY date)) * 100 AS Percentage_Increase_In_Cases
  FROM
    us_cases
)
SELECT
  Date,
  Confirmed_Cases_On_Day,
  Confirmed_Cases_Previous_Day,
  Percentage_Increase_In_Cases
FROM
  us_previous_day_comparison
WHERE
  Percentage_Increase_In_Cases > <LIMIT_VALUE>;
```

**Task 8**
```bash
SELECT
  country_name AS country,
  SUM(cumulative_recovered) AS recovered_cases,
  SUM(cumulative_confirmed) AS confirmed_cases,
  (SUM(cumulative_recovered) / SUM(cumulative_confirmed)) * 100 AS recovery_rate
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  date = '2020-05-10'
  AND subregion1_name IS NULL
GROUP BY
  country_name
HAVING
  confirmed_cases > 50000
ORDER BY
  recovery_rate DESC
LIMIT <LIMIT_VALUE>;
```

**Task 9**
```bash
WITH france_cases AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS total_cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name = "France"
    AND date IN ('2020-01-24', '<DATE>')
  GROUP BY
    date
  ORDER BY
    date
),
summary AS (
  SELECT
    total_cases AS first_day_cases,
    LEAD(total_cases) OVER(ORDER BY date) AS last_day_cases,
    DATE_DIFF(LEAD(date) OVER(ORDER BY date), date, DAY) AS days_diff
  FROM
    france_cases
  LIMIT 1
)
SELECT
  first_day_cases,
  last_day_cases,
  days_diff,
  POW((last_day_cases / first_day_cases), (1 / days_diff)) - 1 AS cdgr
FROM
  summary;
```

**Task 10**
```bash
SELECT
  date,
  SUM(cumulative_confirmed) AS country_cases,
  SUM(cumulative_deceased) AS country_deaths
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  country_name = "United States of America"
  AND date BETWEEN '<DATE_RANGE_START>' AND '<DATE_RANGE_END>'
GROUP BY
  date
ORDER BY
  date ASC;
```
