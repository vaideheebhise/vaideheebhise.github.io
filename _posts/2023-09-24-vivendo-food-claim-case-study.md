<p>In this project i have worked on a fast food chain in Brazil named vivendo with over 200 outlets. Customers often claim compensation from the company for food poisoning. The legal team processes these claims. The legal team has offices in four locations. The legal team wants to improve how long it takes to reply to customers and close claims.
The analysis is provided for the legal team as team wants to improve the time it takes to reply to customers and close the claims. I used pgadmin for validation and cleaning of data. Tableau is used for the data visualization purpose.Here i listed each task performed in the question, answer format.</p>

### Skills : Tableau, SQL, Excel, Data cleaning, Data Exploration


## Task 1

1. For every column in the data:
a. State whether the values match the description given in the table above.
b. State the number of missing values in the column.
c. Describe what you did to make values match the description if they did not
match.

**Solution:**

The given data set contains  **8 columns and 2000 rows**. I checked for missing values and spelling errors for each column. Following describes validation steps taken for cleaning each column. 

**claim_id** :  Column contains nominal values. There are 2000 distinct and 2000 unique values in the column. Column matches with the description. There are no missing values, so no changes were made to this column.

**time_to_close:** Column contains discrete values. The time-to-close column range from 76 to 518 days and all values are positive. There are 256 distinct values and 55 unique values. There are no missing records, so no changes are made to this column.

**claim_amount:** Column contains continuous values. There are 2000 distinct values and  2000 unique values. There are no missing values so no changes were made to this column. All initial claims are listed in the claim_amount column and are made in Brazilian equivalent currency. The column is in text data type which includes the currency symbol, I eliminated the currency signs from every record in the column. In the given dataset 1792 values are rounded to 2 decimal point and 208 values are rounded to 1 decimal. All values are finally rounded to 2 decimal places. Claim amount ranges between 1637.94(MIN) to 76106.80(MAX) in Brazilian equivalent currency. 

**amount_paid:** Column contains continuous values. Column contains a list of all final claim amount that have been made, but the values are not in brazilian currency, so I converted the data to brazilian currency with 2 decimal place precision. There are 1964 distinct values, 1962 unique values and 36 missing values. All null values are replaced with overall median(20105.69). Amount paid ranges between 1516.72(MIN) and 52498.75(MAX) in Brazilian equivalent currency.

**Location:** Column contains nominal values. The column matches with given description.There are no null values in the column.There are total 4 distinct values for location which are RECIFE, SAO LUIS, NATAL, FORTALEZA. Each location has the following number of claims (RECIFE = 885), (SAO LUIS = 517), (NATAL = 287), (FORTALEZA = 311).

**individuals_on_claim:** Column contains discrete values. Number of individual on the claims are listed in this column, and the range is 1 to 15 individuals per claim, with minimum being 1 person. There are 15 distinct values and no missing value, so no changes were made to this column

**linked_cases:** Column contains nominal values. It states weather this claims linked with other claims/cases. Values of column are TRUE, FALSE, NA which is not as described. There are 26 missing values and all missing values are replaced with FALSE.


**cause:** Column contains nominal values. This column contains causes of food poisioning which should be from 3 distinct values ('vegetable', 'meat', ‘unknown') . However Column contains total 2000 values out of which 1970 values are one of the ('vegetable', 'meat', ‘unknown') and remaining 30 values are from "VEGETABLES" (16 values), " Meat” (14 values).  Most of the data is in lower case so, I convered "Meat" to "meat" and "VEGETABLES" to "vegetable". There are No missing values found in the column.


## Task 2


2.Create a visualization that shows the number of claims in each location. Use the visualization to:

a. State which category of the variable location has the most observations
b. Explain whether the observations are balanced across categories of the
variable location.

**Solution:**

<iframe seamless frameborder="0" src="https://public.tableau.com/views/foodclaims_time_to_closevslocationplot/claimlocation?:embed=yes&:display_count=yes&:showVizHome=no" width = '1000' height = '650' scrolling='yes' ></iframe> 

There are total 4 locations mentioned in the data and 2000 total claims. Based on the visualization below the number of claims are spread in unbalanced manner across the four locations and RECIFE shows the maximum count and NATAL shows the minimum count.
The location with the highest claims is RECIFE with 885 claims. SAO LUIS has a record of 517 claims, FORTALEZA accounted for 311 claims, while NATAL with the lowest number of claims accounted for 287 claims.

Also, from the bar plot we can account that observations are not balanced across the category of variable location when considering number of claims at each location.This allosws us to compare magnitude of number of claims per location.
There is  large differences in the number of claims between RACIFE and the other locations which conclude that the observations are not balanced.
Further to support our imbalance statement we can calculate percentage ratio of number of clams each location and total observations. Percentage of  RACIFE is 44.25% (885/2000*100) which is almost half of to entire claims. Also, FORTALEZA, SAO LUIS and NATAL shows percentage as 15.55, 25.85, and 14.35 respectively.



## Task 3

3.Describe the distribution of time to close for all claims. Your answer must include a
visualization that shows the distribution.

**Solution:**

As we know legal team wants to improve on time to close each claim, this distribution will help them to get good insights into time to close the claims.
Based on the visualization below we can see that 90% (1806) claims are closed within 200 days and we have 2% (59) claims  which take more than 250 days to close and most of them are coming from RECIFE and SAO LUIS, which also have a higher number of claims to handle. Legal team can use these insights and look at balancing out the load across all location which may potentially help reduce this right skewed data. 



<iframe seamless frameborder="0" src="https://public.tableau.com/views/foodclaims_new/Sheet7?:embed=yes&:display_count=yes&:showVizHome=no" width = '1000' height = '650' scrolling='yes' ></iframe>



## Task 4

4.Describe the relationship between time to close and location. Your answer must include a visualization to demonstrate the relationship.

**Solution:**

SAO LUIS have the highest average time (187.5) to close due to the outliers for some cliams, and  SAO LUIS also has the highest time taken to close a claim is 518 days and minimum time is 84 days and is the second highest in terms of total number of claims(517).


RECIFE have average time to close 184.6 and maximum time to close is 427 days and minimum time to close the case is 82 days and highest number of claims(885).
FORTALEZA have average time to close 185.3 and maximum time to close is 453 days and minimum time to close the case is 76 days. 
NATAL have average time to close 185.9 and maximum time to close is 361 days and minimum time to close the case is 93 days. 

From this we can say that, the efficiency of how claims are resolved between the different locations is unbalanced and there are a few claims (outliers) which take longer time to close which needs to be further understood.

<iframe seamless frameborder="0" src="https://public.tableau.com/views/foodclaims_time_to_closevslocationplot/time_to_close_vs_location?:embed=yes&:display_count=yes&:showVizHome=no" width = '500' height = '300' scrolling='yes' ></iframe>


<iframe seamless frameborder="0" src="https://public.tableau.com/views/foodclaims_time_to_closevslocationplot/Sheet5?:embed=yes&:display_count=yes&:showVizHome=no" width = '1000' height = '650' scrolling='yes' ></iframe>


