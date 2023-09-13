
<p>In this project I have worked with data provided by the United States Social Security Administration, which lists first names along with the number and sex of babies they were given to in each year. For processing speed purposes, we've limited the dataset to first names which were given to over 5,000 American babies in a given year. Our data spans 101 years, from 1920 through 2020.</p>


<h3 id="baby_names"><code>baby_names</code></h3>
<table>
<thead>
<tr>
<th style="text-align:left;">column</th>
<th>type</th>
<th>meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;"><code>year</code></td>
<td>int</td>
<td>year</td>
</tr>
<tr>
<td style="text-align:left;"><code>first_name</code></td>
<td>varchar</td>
<td>first name</td>
</tr>
<tr>
<td style="text-align:left;"><code>sex</code></td>
<td>varchar</td>
<td><code>sex</code> of babies given <code>first_name</code></td>
</tr>
<tr>
<td style="text-align:left;"><code>num</code></td>
<td>int</td>
<td>number of babies of <code>sex</code> given <code>first_name</code> in that <code>year</code></td>
</tr>
</tbody>
</table>


## 1. Classic American names
<p>Let's get oriented to American baby name tastes by looking at the names that have stood the test of time!</p>


```sql
%%sql
postgresql:///names

-- Select first names and the total babies with that first_name
-- Group by first_name and filter for those names that appear in all 101 years
-- Order by the total number of babies with that first_name, descending
SELECT first_name, SUM(num)
FROM baby_names
GROUP BY first_name
HAVING COUNT(year) = 101
ORDER BY SUM(num) DESC;


```

    8 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>James</td>
            <td>4748138</td>
        </tr>
        <tr>
            <td>John</td>
            <td>4510721</td>
        </tr>
        <tr>
            <td>William</td>
            <td>3614424</td>
        </tr>
        <tr>
            <td>David</td>
            <td>3571498</td>
        </tr>
        <tr>
            <td>Joseph</td>
            <td>2361382</td>
        </tr>
        <tr>
            <td>Thomas</td>
            <td>2166802</td>
        </tr>
        <tr>
            <td>Charles</td>
            <td>2112352</td>
        </tr>
        <tr>
            <td>Elizabeth</td>
            <td>1436286</td>
        </tr>
    </tbody>
</table>


## 2. Timeless or trendy?

<p>Here I have captured the type of popularity that each name in the dataset enjoyed. Was the name classic and popular across many years or trendy, only popular for a few years? </p>


```sql
%%sql

-- Classify first names as 'Classic', 'Semi-classic', 'Semi-trendy', or 'Trendy'
-- Alias this column as popularity_type
-- Select first_name, the sum of babies who have ever had that name, and popularity_type
-- Order the results alphabetically by first_name

SELECT first_name, SUM(num),
    CASE WHEN COUNT(year) > 90 THEN 'Classic'
         WHEN COUNT(year) > 40 THEN 'Semi-classic'
         WHEN COUNT(year) > 10 THEN 'Semi-trendy'
        ELSE 'Trendy' END AS popularity_type
FROM baby_names
GROUP BY first_name
ORDER BY first_name
LIMIT 10;

```

     * postgresql:///names
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>sum</th>
            <th>popularity_type</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Aaliyah</td>
            <td>15870</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aaron</td>
            <td>530592</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Abigail</td>
            <td>338485</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Adam</td>
            <td>497293</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Addison</td>
            <td>107433</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Adrian</td>
            <td>147741</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Aidan</td>
            <td>68566</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aiden</td>
            <td>216194</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alan</td>
            <td>162041</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Albert</td>
            <td>260945</td>
            <td>Semi-trendy</td>
        </tr>
    </tbody>
</table>





## 3. Top-ranked female names since 1920

<p>Here the search is limited to names which were given to female babies. </p>
<p>Window function is used by assigning a rank to female names based on the number of babies that have ever been given that name. What are the top-ranked female names since 1920?</p>


```sql
%%sql

-- RANK names by the sum of babies who have ever had that name (descending), aliasing as name_rank
-- Select name_rank, first_name, and the sum of babies who have ever had that name
-- Filter the data for results where sex equals 'F'
-- Limit to ten results

SELECT
    RANK() OVER (ORDER BY SUM(num) DESC) AS name_rank,
    first_name,
    sum(num)
FROM baby_names
WHERE sex ='F'
GROUP BY first_name
LIMIT 10;

```

     * postgresql:///names
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>name_rank</th>
            <th>first_name</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>Mary</td>
            <td>3215850</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Patricia</td>
            <td>1479802</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Elizabeth</td>
            <td>1436286</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Jennifer</td>
            <td>1404743</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Linda</td>
            <td>1361021</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Barbara</td>
            <td>1343901</td>
        </tr>
        <tr>
            <td>7</td>
            <td>Susan</td>
            <td>1025728</td>
        </tr>
        <tr>
            <td>8</td>
            <td>Jessica</td>
            <td>994210</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Lisa</td>
            <td>920119</td>
        </tr>
        <tr>
            <td>10</td>
            <td>Betty</td>
            <td>893396</td>
        </tr>
    </tbody>
</table>




## 4. Picking a baby name

<p>We are analyzing the data for traditionally female name ending with the letter 'a' and a name that has been popular in the years since 2015. </p>


```sql
%%sql
-- Select only the first_name column
-- Filter for results where sex is 'F', year is greater than 2015, and first_name ends in 'a'
-- Group by first_name and order by the total number of babies given that first_name
SELECT first_name
FROM baby_names
WHERE sex = 'F' AND year > 2015 AND first_name LIKE '%a'
GROUP BY first_name
ORDER BY SUM(num) DESC;
```

     * postgresql:///names
    19 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Olivia</td>
        </tr>
        <tr>
            <td>Emma</td>
        </tr>
        <tr>
            <td>Ava</td>
        </tr>
        <tr>
            <td>Sophia</td>
        </tr>
        <tr>
            <td>Isabella</td>
        </tr>
        <tr>
            <td>Mia</td>
        </tr>
        <tr>
            <td>Amelia</td>
        </tr>
        <tr>
            <td>Ella</td>
        </tr>
        <tr>
            <td>Sofia</td>
        </tr>
        <tr>
            <td>Camila</td>
        </tr>
        <tr>
            <td>Aria</td>
        </tr>
        <tr>
            <td>Victoria</td>
        </tr>
        <tr>
            <td>Layla</td>
        </tr>
        <tr>
            <td>Nora</td>
        </tr>
        <tr>
            <td>Mila</td>
        </tr>
        <tr>
            <td>Luna</td>
        </tr>
        <tr>
            <td>Stella</td>
        </tr>
        <tr>
            <td>Gianna</td>
        </tr>
        <tr>
            <td>Aurora</td>
        </tr>
    </tbody>
</table>





## 5. The Olivia expansion
<p>Based on the results in the previous task, Olivia is the most popular female name ending in 'A' since 2015. In the next step we will analyze when did the name Olivia become so popular?</p>
<p>The rise of the name Olivia is explored with the help of a window function.</p>


```sql
%%sql

-- Select year, first_name, num of Olivias in that year, and cumulative_olivias
-- Sum the cumulative babies who have been named Olivia up to that year; alias as cumulative_olivias
-- Filter so that only data for the name Olivia is returned.
-- Order by year from the earliest year to most recent

SELECT year, first_name, num,
    SUM(num) OVER (ORDER BY year) AS cumulative_olivias
FROM baby_names
WHERE first_name = 'Olivia'
ORDER BY year ASC
LIMIT 10;

```

     * postgresql:///names
    30 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>first_name</th>
            <th>num</th>
            <th>cumulative_olivias</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1991</td>
            <td>Olivia</td>
            <td>5601</td>
            <td>5601</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>Olivia</td>
            <td>5809</td>
            <td>11410</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>Olivia</td>
            <td>6340</td>
            <td>17750</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>Olivia</td>
            <td>6434</td>
            <td>24184</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>Olivia</td>
            <td>7624</td>
            <td>31808</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>Olivia</td>
            <td>8124</td>
            <td>39932</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>Olivia</td>
            <td>9477</td>
            <td>49409</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>Olivia</td>
            <td>10610</td>
            <td>60019</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>Olivia</td>
            <td>11255</td>
            <td>71274</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>Olivia</td>
            <td>12852</td>
            <td>84126</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>Olivia</td>
            <td>13977</td>
            <td>98103</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>Olivia</td>
            <td>14630</td>
            <td>112733</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Olivia</td>
            <td>16152</td>
            <td>128885</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Olivia</td>
            <td>16106</td>
            <td>144991</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Olivia</td>
            <td>15694</td>
            <td>160685</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>Olivia</td>
            <td>15501</td>
            <td>176186</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>Olivia</td>
            <td>16584</td>
            <td>192770</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>Olivia</td>
            <td>17084</td>
            <td>209854</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>Olivia</td>
            <td>17438</td>
            <td>227292</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>Olivia</td>
            <td>17029</td>
            <td>244321</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>Olivia</td>
            <td>17327</td>
            <td>261648</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>Olivia</td>
            <td>17320</td>
            <td>278968</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>Olivia</td>
            <td>18439</td>
            <td>297407</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>Olivia</td>
            <td>19823</td>
            <td>317230</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>Olivia</td>
            <td>19710</td>
            <td>336940</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>Olivia</td>
            <td>19380</td>
            <td>356320</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>Olivia</td>
            <td>18744</td>
            <td>375064</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>Olivia</td>
            <td>18011</td>
            <td>393075</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>Olivia</td>
            <td>18508</td>
            <td>411583</td>
        </tr>
        <tr>
            <td>2020</td>
            <td>Olivia</td>
            <td>17535</td>
            <td>429118</td>
        </tr>
    </tbody>
</table>





## 6. Many males with the same name
<p>In the next task, Let's take a look at traditionally male names. We saw in the first task that there are nine traditionally male names given to at least 5,000 babies every single year in our 101-year dataset! </p>
<p>In the next two tasks, we will build up to listing every year along with the most popular male name in that year. This presents a common problem: how do we find the greatest X in a group? Or, in the context of this problem, how do we find the male name given to the highest number of babies in a year? </p>
<p> Subquery can be used in the query.  first query is written that selects the <code>year</code> and the maximum <code>num</code> of babies given any single male name in that year. For example, in 1989, the male name given to the highest number of babies was given to 65,339 babies. This query is written in this task. </p>


```sql
%%sql

-- Select year and maximum number of babies given any one male name in that year, aliased as max_num
-- Filter the data to include only results where sex equals 'M'
SELECT year, MAX(num) as max_num
FROM baby_names
WHERE sex ='M'
GROUP BY year;
```

     * postgresql:///names
    101 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>max_num</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1970</td>
            <td>85291</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>34483</td>
        </tr>
        <tr>
            <td>1947</td>
            <td>94764</td>
        </tr>
        <tr>
            <td>1962</td>
            <td>85041</td>
        </tr>
        <tr>
            <td>1975</td>
            <td>68451</td>
        </tr>
        <tr>
            <td>1980</td>
            <td>68704</td>
        </tr>
        <tr>
            <td>1931</td>
            <td>60518</td>
        </tr>
        <tr>
            <td>1981</td>
            <td>68776</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>18266</td>
        </tr>
        <tr>
            <td>1972</td>
            <td>71401</td>
        </tr>
        <tr>
            <td>1956</td>
            <td>90665</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>24292</td>
        </tr>
        <tr>
            <td>1948</td>
            <td>88589</td>
        </tr>
        <tr>
            <td>1984</td>
            <td>67745</td>
        </tr>
        <tr>
            <td>1957</td>
            <td>92718</td>
        </tr>
        <tr>
            <td>1961</td>
            <td>86917</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>30579</td>
        </tr>
        <tr>
            <td>1925</td>
            <td>60897</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>54397</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>22603</td>
        </tr>
        <tr>
            <td>1958</td>
            <td>90564</td>
        </tr>
        <tr>
            <td>1971</td>
            <td>77599</td>
        </tr>
        <tr>
            <td>1985</td>
            <td>64924</td>
        </tr>
        <tr>
            <td>1926</td>
            <td>61130</td>
        </tr>
        <tr>
            <td>1988</td>
            <td>64150</td>
        </tr>
        <tr>
            <td>1929</td>
            <td>59804</td>
        </tr>
        <tr>
            <td>1963</td>
            <td>83778</td>
        </tr>
        <tr>
            <td>1928</td>
            <td>60703</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>29643</td>
        </tr>
        <tr>
            <td>1930</td>
            <td>62149</td>
        </tr>
        <tr>
            <td>1951</td>
            <td>87261</td>
        </tr>
        <tr>
            <td>1940</td>
            <td>62476</td>
        </tr>
        <tr>
            <td>1982</td>
            <td>68244</td>
        </tr>
        <tr>
            <td>1920</td>
            <td>56914</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>35367</td>
        </tr>
        <tr>
            <td>1952</td>
            <td>87063</td>
        </tr>
        <tr>
            <td>2020</td>
            <td>19659</td>
        </tr>
        <tr>
            <td>1946</td>
            <td>87439</td>
        </tr>
        <tr>
            <td>1968</td>
            <td>81995</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>38365</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>25837</td>
        </tr>
        <tr>
            <td>1923</td>
            <td>57469</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>21184</td>
        </tr>
        <tr>
            <td>1924</td>
            <td>60801</td>
        </tr>
        <tr>
            <td>1954</td>
            <td>88576</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>27886</td>
        </tr>
        <tr>
            <td>1938</td>
            <td>62269</td>
        </tr>
        <tr>
            <td>1942</td>
            <td>77174</td>
        </tr>
        <tr>
            <td>1966</td>
            <td>79990</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>36616</td>
        </tr>
        <tr>
            <td>1974</td>
            <td>67580</td>
        </tr>
        <tr>
            <td>1949</td>
            <td>86865</td>
        </tr>
        <tr>
            <td>1990</td>
            <td>65302</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>41399</td>
        </tr>
        <tr>
            <td>1973</td>
            <td>67842</td>
        </tr>
        <tr>
            <td>1927</td>
            <td>61671</td>
        </tr>
        <tr>
            <td>1941</td>
            <td>66743</td>
        </tr>
        <tr>
            <td>1977</td>
            <td>67609</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>32554</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>37549</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>19319</td>
        </tr>
        <tr>
            <td>1965</td>
            <td>81021</td>
        </tr>
        <tr>
            <td>1935</td>
            <td>56522</td>
        </tr>
        <tr>
            <td>1944</td>
            <td>76954</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>44472</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>19154</td>
        </tr>
        <tr>
            <td>1960</td>
            <td>85933</td>
        </tr>
        <tr>
            <td>1987</td>
            <td>63654</td>
        </tr>
        <tr>
            <td>1978</td>
            <td>67157</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>19924</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>24850</td>
        </tr>
        <tr>
            <td>1921</td>
            <td>58215</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>49554</td>
        </tr>
        <tr>
            <td>1964</td>
            <td>82642</td>
        </tr>
        <tr>
            <td>1943</td>
            <td>80274</td>
        </tr>
        <tr>
            <td>1937</td>
            <td>61842</td>
        </tr>
        <tr>
            <td>1986</td>
            <td>64224</td>
        </tr>
        <tr>
            <td>1953</td>
            <td>86247</td>
        </tr>
        <tr>
            <td>1959</td>
            <td>85224</td>
        </tr>
        <tr>
            <td>1976</td>
            <td>66947</td>
        </tr>
        <tr>
            <td>1989</td>
            <td>65399</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>19088</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>20378</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>20555</td>
        </tr>
        <tr>
            <td>1955</td>
            <td>88372</td>
        </tr>
        <tr>
            <td>1939</td>
            <td>59653</td>
        </tr>
        <tr>
            <td>1979</td>
            <td>67742</td>
        </tr>
        <tr>
            <td>1991</td>
            <td>60793</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>18824</td>
        </tr>
        <tr>
            <td>1969</td>
            <td>85201</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>19650</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>22139</td>
        </tr>
        <tr>
            <td>1932</td>
            <td>59265</td>
        </tr>
        <tr>
            <td>1967</td>
            <td>82440</td>
        </tr>
        <tr>
            <td>1922</td>
            <td>57280</td>
        </tr>
        <tr>
            <td>1933</td>
            <td>54223</td>
        </tr>
        <tr>
            <td>1934</td>
            <td>55834</td>
        </tr>
        <tr>
            <td>1936</td>
            <td>58499</td>
        </tr>
        <tr>
            <td>1945</td>
            <td>74460</td>
        </tr>
        <tr>
            <td>1950</td>
            <td>86229</td>
        </tr>
        <tr>
            <td>1983</td>
            <td>68010</td>
        </tr>
    </tbody>
</table>





## 7. Top male names over the years
<p>In the previous task, we found the maximum number of babies given any one male name in each year. Incredibly, the most popular name each year varied from being given to less than 20,000 babies to being given to more than 90,000! </p>
<p>In this task, we find out what that top male name is for each year in our dataset. </p>


```sql
%%sql

-- Select year, first_name given to the largest number of male babies, and num of babies given that name
-- Join baby_names to the code in the last task as a subquery
-- Order results by year descending

SELECT b.year, b.first_name, b.num
FROM baby_names AS b
INNER JOIN (
    SELECT year, MAX(num) as max_num
    FROM baby_names
    WHERE sex ='M'
    GROUP BY year) AS new_subquery
ON new_subquery.year = b.year AND new_subquery.max_num = b.num
ORDER BY year DESC;

```

     * postgresql:///names
    101 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>first_name</th>
            <th>num</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2020</td>
            <td>Liam</td>
            <td>19659</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>Liam</td>
            <td>20555</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>Liam</td>
            <td>19924</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>Liam</td>
            <td>18824</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>Noah</td>
            <td>19154</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>Noah</td>
            <td>19650</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>Noah</td>
            <td>19319</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>Noah</td>
            <td>18266</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>Jacob</td>
            <td>19088</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>Jacob</td>
            <td>20378</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>Jacob</td>
            <td>22139</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>Jacob</td>
            <td>21184</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>Jacob</td>
            <td>22603</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>Jacob</td>
            <td>24292</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>Jacob</td>
            <td>24850</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Jacob</td>
            <td>25837</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Jacob</td>
            <td>27886</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Jacob</td>
            <td>29643</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>Jacob</td>
            <td>30579</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>Jacob</td>
            <td>32554</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>Jacob</td>
            <td>34483</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>Jacob</td>
            <td>35367</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>Michael</td>
            <td>36616</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>Michael</td>
            <td>37549</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>Michael</td>
            <td>38365</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>Michael</td>
            <td>41399</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>Michael</td>
            <td>44472</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>Michael</td>
            <td>49554</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>Michael</td>
            <td>54397</td>
        </tr>
        <tr>
            <td>1991</td>
            <td>Michael</td>
            <td>60793</td>
        </tr>
        <tr>
            <td>1990</td>
            <td>Michael</td>
            <td>65302</td>
        </tr>
        <tr>
            <td>1989</td>
            <td>Michael</td>
            <td>65399</td>
        </tr>
        <tr>
            <td>1988</td>
            <td>Michael</td>
            <td>64150</td>
        </tr>
        <tr>
            <td>1987</td>
            <td>Michael</td>
            <td>63654</td>
        </tr>
        <tr>
            <td>1986</td>
            <td>Michael</td>
            <td>64224</td>
        </tr>
        <tr>
            <td>1985</td>
            <td>Michael</td>
            <td>64924</td>
        </tr>
        <tr>
            <td>1984</td>
            <td>Michael</td>
            <td>67745</td>
        </tr>
        <tr>
            <td>1983</td>
            <td>Michael</td>
            <td>68010</td>
        </tr>
        <tr>
            <td>1982</td>
            <td>Michael</td>
            <td>68244</td>
        </tr>
        <tr>
            <td>1981</td>
            <td>Michael</td>
            <td>68776</td>
        </tr>
        <tr>
            <td>1980</td>
            <td>Michael</td>
            <td>68704</td>
        </tr>
        <tr>
            <td>1979</td>
            <td>Michael</td>
            <td>67742</td>
        </tr>
        <tr>
            <td>1978</td>
            <td>Michael</td>
            <td>67157</td>
        </tr>
        <tr>
            <td>1977</td>
            <td>Michael</td>
            <td>67609</td>
        </tr>
        <tr>
            <td>1976</td>
            <td>Michael</td>
            <td>66947</td>
        </tr>
        <tr>
            <td>1975</td>
            <td>Michael</td>
            <td>68451</td>
        </tr>
        <tr>
            <td>1974</td>
            <td>Michael</td>
            <td>67580</td>
        </tr>
        <tr>
            <td>1973</td>
            <td>Michael</td>
            <td>67842</td>
        </tr>
        <tr>
            <td>1972</td>
            <td>Michael</td>
            <td>71401</td>
        </tr>
        <tr>
            <td>1971</td>
            <td>Michael</td>
            <td>77599</td>
        </tr>
        <tr>
            <td>1970</td>
            <td>Michael</td>
            <td>85291</td>
        </tr>
        <tr>
            <td>1969</td>
            <td>Michael</td>
            <td>85201</td>
        </tr>
        <tr>
            <td>1968</td>
            <td>Michael</td>
            <td>81995</td>
        </tr>
        <tr>
            <td>1967</td>
            <td>Michael</td>
            <td>82440</td>
        </tr>
        <tr>
            <td>1966</td>
            <td>Michael</td>
            <td>79990</td>
        </tr>
        <tr>
            <td>1965</td>
            <td>Michael</td>
            <td>81021</td>
        </tr>
        <tr>
            <td>1964</td>
            <td>Michael</td>
            <td>82642</td>
        </tr>
        <tr>
            <td>1963</td>
            <td>Michael</td>
            <td>83778</td>
        </tr>
        <tr>
            <td>1962</td>
            <td>Michael</td>
            <td>85041</td>
        </tr>
        <tr>
            <td>1961</td>
            <td>Michael</td>
            <td>86917</td>
        </tr>
        <tr>
            <td>1960</td>
            <td>David</td>
            <td>85933</td>
        </tr>
        <tr>
            <td>1959</td>
            <td>Michael</td>
            <td>85224</td>
        </tr>
        <tr>
            <td>1958</td>
            <td>Michael</td>
            <td>90564</td>
        </tr>
        <tr>
            <td>1957</td>
            <td>Michael</td>
            <td>92718</td>
        </tr>
        <tr>
            <td>1956</td>
            <td>Michael</td>
            <td>90665</td>
        </tr>
        <tr>
            <td>1955</td>
            <td>Michael</td>
            <td>88372</td>
        </tr>
        <tr>
            <td>1954</td>
            <td>Michael</td>
            <td>88576</td>
        </tr>
        <tr>
            <td>1953</td>
            <td>Robert</td>
            <td>86247</td>
        </tr>
        <tr>
            <td>1952</td>
            <td>James</td>
            <td>87063</td>
        </tr>
        <tr>
            <td>1951</td>
            <td>James</td>
            <td>87261</td>
        </tr>
        <tr>
            <td>1950</td>
            <td>James</td>
            <td>86229</td>
        </tr>
        <tr>
            <td>1949</td>
            <td>James</td>
            <td>86865</td>
        </tr>
        <tr>
            <td>1948</td>
            <td>James</td>
            <td>88589</td>
        </tr>
        <tr>
            <td>1947</td>
            <td>James</td>
            <td>94764</td>
        </tr>
        <tr>
            <td>1946</td>
            <td>James</td>
            <td>87439</td>
        </tr>
        <tr>
            <td>1945</td>
            <td>James</td>
            <td>74460</td>
        </tr>
        <tr>
            <td>1944</td>
            <td>James</td>
            <td>76954</td>
        </tr>
        <tr>
            <td>1943</td>
            <td>James</td>
            <td>80274</td>
        </tr>
        <tr>
            <td>1942</td>
            <td>James</td>
            <td>77174</td>
        </tr>
        <tr>
            <td>1941</td>
            <td>James</td>
            <td>66743</td>
        </tr>
        <tr>
            <td>1940</td>
            <td>James</td>
            <td>62476</td>
        </tr>
        <tr>
            <td>1939</td>
            <td>Robert</td>
            <td>59653</td>
        </tr>
        <tr>
            <td>1938</td>
            <td>Robert</td>
            <td>62269</td>
        </tr>
        <tr>
            <td>1937</td>
            <td>Robert</td>
            <td>61842</td>
        </tr>
        <tr>
            <td>1936</td>
            <td>Robert</td>
            <td>58499</td>
        </tr>
        <tr>
            <td>1935</td>
            <td>Robert</td>
            <td>56522</td>
        </tr>
        <tr>
            <td>1934</td>
            <td>Robert</td>
            <td>55834</td>
        </tr>
        <tr>
            <td>1933</td>
            <td>Robert</td>
            <td>54223</td>
        </tr>
        <tr>
            <td>1932</td>
            <td>Robert</td>
            <td>59265</td>
        </tr>
        <tr>
            <td>1931</td>
            <td>Robert</td>
            <td>60518</td>
        </tr>
        <tr>
            <td>1930</td>
            <td>Robert</td>
            <td>62149</td>
        </tr>
        <tr>
            <td>1929</td>
            <td>Robert</td>
            <td>59804</td>
        </tr>
        <tr>
            <td>1928</td>
            <td>Robert</td>
            <td>60703</td>
        </tr>
        <tr>
            <td>1927</td>
            <td>Robert</td>
            <td>61671</td>
        </tr>
        <tr>
            <td>1926</td>
            <td>Robert</td>
            <td>61130</td>
        </tr>
        <tr>
            <td>1925</td>
            <td>Robert</td>
            <td>60897</td>
        </tr>
        <tr>
            <td>1924</td>
            <td>Robert</td>
            <td>60801</td>
        </tr>
        <tr>
            <td>1923</td>
            <td>John</td>
            <td>57469</td>
        </tr>
        <tr>
            <td>1922</td>
            <td>John</td>
            <td>57280</td>
        </tr>
        <tr>
            <td>1921</td>
            <td>John</td>
            <td>58215</td>
        </tr>
        <tr>
            <td>1920</td>
            <td>John</td>
            <td>56914</td>
        </tr>
    </tbody>
</table>




## 8. The most years at number one
<p>Noah and Liam have ruled the roost in the last few years, but if we scroll down in the results, it looks like Michael and Jacob have also spent a good number of years as the top name! Which name has been number one for the largest number of years? Let's use a common table expression to find out. </p>


```sql
%%sql

-- Select first_name and a count of years it was the top name in the last task; alias as count_top_name
-- Use the code from the previous task as a common table expression
-- Group by first_name and order by count_top_name descending

WITH count_top_name_male AS (
    SELECT b.year, b.first_name, b.num
    FROM baby_names AS b
    INNER JOIN (
        SELECT year, MAX(num) as max_num
        FROM baby_names
        WHERE sex ='M'
        GROUP BY year) AS new_subquery
    ON new_subquery.year = b.year
    AND new_subquery.max_num = b.num
    ORDER BY year DESC
    )
SELECT first_name , COUNT(first_name) as count_top_name
FROM count_top_name_male
GROUP BY first_name
ORDER BY COUNT(first_name) DESC;
```

     * postgresql:///names
    8 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>count_top_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Michael</td>
            <td>44</td>
        </tr>
        <tr>
            <td>Robert</td>
            <td>17</td>
        </tr>
        <tr>
            <td>Jacob</td>
            <td>14</td>
        </tr>
        <tr>
            <td>James</td>
            <td>13</td>
        </tr>
        <tr>
            <td>Noah</td>
            <td>4</td>
        </tr>
        <tr>
            <td>John</td>
            <td>4</td>
        </tr>
        <tr>
            <td>Liam</td>
            <td>4</td>
        </tr>
        <tr>
            <td>David</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



