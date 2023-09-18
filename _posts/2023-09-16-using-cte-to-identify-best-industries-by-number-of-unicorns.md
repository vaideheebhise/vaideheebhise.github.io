<p>
In this project, I will be working with data from investment firm to analyze trends in high-growth companies. The goal is to identify industries which are producing the highest valuations and the rate at which new high-value companies are emerging. This data will provide insightful information to give investment firms a competitive insight about the ongoing industry trends and how they should structure their portfolio looking forward.
</p>

The dataset tables are as follows:

## dates

```
| Column       | Description                                  |
|------------- |--------------------------------------------- |
| `company_id`   | A unique ID for the company.                 |
| `date_joined` | The date that the company became a unicorn.  |
| `year_founded` | The year that the company was founded.       |
```

## funding

```
| Column           | Description                                  |
|----------------- |--------------------------------------------- |
| `company_id`       | A unique ID for the company.                 |
| `valuation`        | Company value in US dollars.                 |
| `funding`          | The amount of funding raised in US dollars.  |
| `select_investors` | A list of key investors in the company.      |
```

## industries

```
| Column       | Description                                  |
|------------- |--------------------------------------------- |
| `company_id`   | A unique ID for the company.                 |
| `industry`     | The industry that the company operates in.   |
```

## companies

```
| Column       | Description                                       |
|------------- |-------------------------------------------------- |
| `company_id`   | A unique ID for the company.                      |
| `company`      | The name of the company.                          |
| `city`         | The city where the company is headquartered.      |
| `country`      | The country where the company is headquartered.   |
| `continent`    | The continent where the company is headquartered. |
```

Here I will use CTE ( common table expression ) to first find top industries, then get the 
yearly ranking and then based on data I will write a query which  will give top 3 industries for the years 2019, 2020, 2021 ordered by the calendar year, and number of unicorns 

```python
--- finding top industries

WITH top_industry AS (
	SELECT i.industry, COUNT(i.*) AS company_count
	FROM industries AS i
	INNER JOIN dates AS d
	ON i.company_id = d.company_id
	WHERE EXTRACT(year FROM d.date_joined) IN ('2019', '2020', '2021')
	GROUP BY industry
	ORDER BY company_count DESC
	limit 3
),

--Gathering yearly ranking
yearly_ranking AS (
	SELECT COUNT(i.*) AS num_unicorns,
	i.industry,EXTRACT(year FROM d.date_joined) AS year,
	AVG(f.valuation) AS avg_valuation
	FROM industries AS i 
	INNER JOIN dates AS d
	ON i.company_id = d.company_id
	INNER JOIN funding AS f
	ON i.company_id = f.company_id
	GROUP BY industry, year
)
SELECT industry, year, num_unicorns, 
ROUND(AVG(avg_valuation)/1000000000,2) AS average_valuation_billions
FROM yearly_ranking
WHERE year IN (2019,2020, 2021) AND industry IN (SELECT industry FROM top_industry)
GROUP BY industry, year, num_unicorns
ORDER BY year, num_unicorns;
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>industry</th>
      <th>year</th>
      <th>num_unicorns</th>
      <th>average_valuation_billions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>E-commerce &amp; direct-to-consumer</td>
      <td>2019</td>
      <td>12</td>
      <td>2.58</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Internet software &amp; services</td>
      <td>2019</td>
      <td>13</td>
      <td>4.23</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fintech</td>
      <td>2019</td>
      <td>20</td>
      <td>6.80</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Fintech</td>
      <td>2020</td>
      <td>15</td>
      <td>4.33</td>
    </tr>
    <tr>
      <th>4</th>
      <td>E-commerce &amp; direct-to-consumer</td>
      <td>2020</td>
      <td>16</td>
      <td>4.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Internet software &amp; services</td>
      <td>2020</td>
      <td>20</td>
      <td>4.35</td>
    </tr>
    <tr>
      <th>6</th>
      <td>E-commerce &amp; direct-to-consumer</td>
      <td>2021</td>
      <td>47</td>
      <td>2.47</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Internet software &amp; services</td>
      <td>2021</td>
      <td>119</td>
      <td>2.15</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Fintech</td>
      <td>2021</td>
      <td>138</td>
      <td>2.75</td>
    </tr>
  </tbody>
</table>
</div>


