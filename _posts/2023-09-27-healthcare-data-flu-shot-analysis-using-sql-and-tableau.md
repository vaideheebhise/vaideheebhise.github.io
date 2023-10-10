<p>In this project i have performed data analysis and visualization for healthcare system data of flu shots using SQL and Tableau.  Data is downloaded from synthea which is an open source platform for healthcare data. Using this data I will be summaryzing the flu shots analysis in 2022. I have designed and developed a flu shots dashboard which shows : </p> 

-  Total % of patients getting flu shots stratified by
	-  Age
	-  Race
	-  County
	-  Overall
-  Running Total of flu shots over the course of 2022
-  Total number of flu shots given in 2022
-  A list of patients that show whether or not they recieved the flu shots

**Data analysis and validation in SQL**

I have created 4 tables in psql databse which are conditions, immunizations, patients, encounter. All downloaded data is in .txt format and imported in these 4 tables respectively.
The data was analysed, cleaned and validated using SQL in pgadmin. Following queries are written to get the final data file for the visualization purpose considering our objective.


- Immunization table (CTE) will give me the information about flu vaccines from discription column, we are looking for the data of 2022 so we will be applying conndition accordingly.

```
WITH flu_shot_2022 AS
(
SELECT patient, min(date) AS earliest_flu_shot_2022 
FROM Immunizations 
WHERE code  = '5302'
     AND date BETWEEN '2022-01-01 00:00' and '2022-12-31 23:59'
GROUP BY patient
), 
```

- The following CTE is giving us the active patients from 2020 to 2022 and is making sure the flu shot is given to patients with age >= 6.

``` 
active_patients AS
(
	SELECT  DISTINCT patient, extract(year from age('2022-12-31',p.birthdate)) as age
	FROM encounters AS enc
	JOIN patients AS p
	On enc.patient = p.id
	WHERE start BETWEEN '2020-01-01 00:00' AND '2022-12-31 23:59'
	AND p.deathdate is NULL
	AND extract(year from age('2022-12-31',p.birthdate)) >= 6
)
```

This final query, uses the above CTEs and produces the final data output which will be the input data required for visualization.

```
SELECT p.id,
	   p.first,
	   p.last,
	   p.birthdate,
	   p.race,
	   p.county, 
	   p.ethnicity,
	   extract(year from age('2022-12-31',p.birthdate)) as age,
	   flu.earliest_flu_shot_2022,
	   flu.patient,
	   case when flu.patient IS NOT NULL THEN 1
	   ELSE 0
	   END AS flu_shot_2022
FROM patients AS p
LEFT JOIN flu_shot_2022 AS flu
ON p.id = flu.patient
WHERE 1=1
	AND p.id IN (SELECT patient FROM active_patients );
	  
```

The data is then downloaded in csv format from the above query result table and given as input file to tableau for further visualization.

**Data Visualization:**

<iframe seamless frameborder="0" src="https://public.tableau.com/views/SummaryHealthcareSystemFlushots2022-final1/Dashboard1?:embed=yes&:display_count=yes&:showVizHome=no" width = '1200' height = '800' scrolling='yes' ></iframe> 

The dashboard gives us insight into summary of healthcare system for flu shots for Massachusetts in 2022. Each sheet of dashboard gives us the following insights.

1. **Flu shots by Age**:
This is the data plotted in 5 bins considering age of the patients. Each bar represent the percentage of flu shots recieved by patients in the different age groups.

2. **Flu Shots by Race**:
This plot represnt the flu shots recieved by patients considering race as factor. The plot gives the percentage of flu shots given to asian, black, hawaiian, native, other and white patiemts.

3. **Flu shots % by county**:
The map plot gives the actual percentage of flu shots recieved in different counties in Massachusetts.

4. **Flu shots list**:
This lists all the patients, along with their age, and highlights whether the patient received a flu shot or not. `1` indicates the patient received the flut shot and `0` indicates the patient has not yet received the flu shot.

5. **Running Sum of flu shots 2022**:
This plot gives the area curve plot for running sum of number of patients who recied the flu shot every month.The plot shows trend  more patiennts have taken the flu shots at the end of 2022 during the winter. The plot shows the curve between number of patients and earliest shot taken that month of 2022. 