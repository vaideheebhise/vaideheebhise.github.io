<p>
In this tutorial I share how to import csv data files into a locally running
postgresql on a docker container using python libraries pandas, sqlalchemy
</p>


## How to import a CSV file to Postgresql database?
To start with, you will need to download a csv data file in your laptop. We wiil be importing that data into postgresql database which is running in a docker container. At the end we will be executing some simple sql queries on that database.

To start a locally running postgresql container run the below command: 

```
docker run --name test-db -e POSTGRES_PASSWORD=my_secret_password -d -p 5432:5432 postgres
```

Let's import pandas library since it have all required functions.

```
    import pandas as pd
```
Store your downloaded csv file in some folder and get the filepath. Now we wll read the the  csv datafile into dataframe from the stored file path

```
df = pd.read_csv(r'FILEPATH/artists.csv')
```

Now we have the csv data in dataframe, lets verify that. 

```

df.head()
Out[62]: 
   Artist ID             Name Nationality Gender  Birth Year  Death Year
0          1   Robert Arneson    American   Male      1930.0      1992.0
1          2   Doroteo Arnaiz     Spanish   Male      1936.0         NaN
2          3      Bill Arnold    American   Male      1941.0         NaN
3          4  Charles Arnoldi    American   Male      1946.0         NaN
4          5      Per Arnoldi      Danish   Male      1941.0         NaN
```



 The next task will be establishing a connection to connect with postgres database and insert the data in dataframe to a postgresql table. For that purpose we are creating an engine using create_engine funtion from sqlalchemy.

Lets import the ```create_engine``` function.

```
  	from sqlalchemy import create_engine
```

After importing the function we will be creating an connection by providing variables as database type, username, password and database name to the function
```
engine = create_engine('postgresql://username:add_your_db_password@localhost:5432/database_name')'
```

Then we will be importing the datframe to postgresql database table using ```to_sql``` function, where we need to provid Table name and the engine as parameters/variables.

```
	 df.to_sql('artworks', engine)
```

Now we have all our data inside artworks table. We have sucessfully imported an csv datafile to posgresql database. Now we will be querying our data for verification purpose.


Write any sql query inside read_sql function withinn code block with connection engine as another parameter. Then execute the query.

```
pd.read_sql("""select * from artworks limit 5""", engine)
```

The query above select all columns from the artworks table and limits the output to 5 rows.

```
   index  Artwork ID                                              Title ArtistID           Name    Date                                             Medium                                         Dimensions Acquisition Date                       Credit Catalogue                  Department    Classification   Object Number Diameter (cm) Circumference (cm)  Height (cm) Length (cm)  Width (cm) Depth (cm) Weight (kg) Duration (s)
0  31047       32387                             A PYTHAGOREAN NOTEBOOK     2906  Alfred Jensen  (1965)  20 lithographs, 19 printed in color and 1 prin...  page: 22 5/8 x 16 7/16" (57.5 x 41.8 cm); Prin...       1969-12-04  Gift of Kleiner, Bell & Co.         N  Prints & Illustrated Books  Illustrated Book  1699.1967.1-20          None               None         57.5        None        41.8       None        None         None
1  31048       32388  In-text plate (folio 11) from A PYTHAGOREAN NO...     2906  Alfred Jensen    1965                       Lithograph, printed in color  irreg. composition  22 1/2 x 16 1/2" (57.1 x 4...       1969-12-04  Gift of Kleiner, Bell & Co.         N  Prints & Illustrated Books  Illustrated Book    1699.1967.10          None               None         57.1        None        42.0       None        None         None
2  31049       32389  In-text plate (folio 12) from A PYTHAGOREAN NO...     2906  Alfred Jensen    1965                       Lithograph, printed in color  irreg composition  16 1/8 x 22 9/16" (41 x 57....       1969-12-04  Gift of Kleiner, Bell & Co.         N  Prints & Illustrated Books  Illustrated Book    1699.1967.11          None               None         41.0        None        57.3       None        None         None
3  31050       32390  In-text plate (folio 13) from A PYTHAGOREAN NO...     2906  Alfred Jensen    1965                       Lithograph, printed in color  irreg composition  15 9/16 x 20 11/16" (39.6 x...       1969-12-04  Gift of Kleiner, Bell & Co.         N  Prints & Illustrated Books  Illustrated Book    1699.1967.12          None               None         39.6        None        52.5       None        None         None
4  31051       32391  In-text plate (folio 14) from A PYTHAGOREAN NO...     2906  Alfred Jensen    1965                       Lithograph, printed in black  irreg composition  16 1/8 x 21 3/4" (41 x 55.2...       1969-12-04  Gift of Kleiner, Bell & Co.         N  Prints & Illustrated Books  Illustrated Book    1699.1967.13          None               None         41.0        None        55.2       None        None         None

```

This way you can easily import data into postgresql using pandas from a csv file. There is additional data manipulation which can be done,  like changing column names, cleaning data, but that's for another tutorial. 



