# Final Project : Evlauating the Movie Business for Microsoft's next big move!
The goal of this project is to analyze data from imdb to assist Microsoft, or any new player, in taking on the film industry. I use the **'tn.movie_budgets.csv.gz'** and **im.db** to analyze the budget and movie details for each film. Then, I will provide recommendations for the client to use in the future.

## Opening the file
The 1st thing to do is to open the file. Below, I will import the relevant libraries and created a Pandas Dataframe called **df_1**.



```python
#import file, reading and showing it

import csv
import gzip
import pandas as pd

g=gzip.open('tn.movie_budgets.csv.gz')
file_content=g.read()

df_1 = pd.read_csv('tn.movie_budgets.csv.gz')
df_1

```




![image](https://user-images.githubusercontent.com/101752113/163874744-e3a4c243-4ec9-41df-874e-33613aa39b3d.png)



Next, I create a new column called 'Year' using Pandas DatetimeIndex class.



```python
# Creating a year column
df_1['Year'] = pd.DatetimeIndex(df_1['release_date']).year  

```

## Preparing and Cleaning

* First, I have to limit the year to 2012 and above. It's a little arbitrary but it's still a lot of data plus many of the relevant players were already around by that time. 
* I would like to create a 'foreign_gross' column that is the difference between the worldwide and domestic gross. Unfortunately, right now, the budget and both gross figures are strings and not integers.
* Lastly, I will create ROI% columns.


```python
#Limiting to past 10 years
df_1=df_1.loc[df_1['Year'] > 2011]
df_1
```




![image](https://user-images.githubusercontent.com/101752113/163875045-b1ea4b81-27e1-41ad-b129-898c24701446.png)


Then, I use a lambda function to replace the $ sign with an empty space:



```python
df_1 = df_1.assign(production_budget= (df_1['production_budget'].apply(lambda x: x.replace('$', ''))))
df_1 = df_1.assign(domestic_gross= (df_1['domestic_gross'].apply(lambda x: x.replace('$', ''))))
df_1 = df_1.assign(worldwide_gross= (df_1['worldwide_gross'].apply(lambda x: x.replace('$', ''))))


```

Now, I employ more lambda functions to get rid of the commas (,) inside every monetary value:



```python
df_1 = df_1.assign(production_budget= (df_1['production_budget'].apply(lambda x: x.replace(',', ''))))
df_1 = df_1.assign(domestic_gross= (df_1['domestic_gross'].apply(lambda x: x.replace(',', ''))))
df_1 = df_1.assign(worldwide_gross= (df_1['worldwide_gross'].apply(lambda x: x.replace(',', ''))))
```

The love affair with lambda functions continues as each column will be converted from a string into an integer:


```python
df_1 = df_1.assign(production_budget= (df_1['production_budget'].apply(lambda x: int(x))))
df_1 = df_1.assign(domestic_gross= (df_1['domestic_gross'].apply(lambda x: int(x))))
df_1 = df_1.assign(worldwide_gross= (df_1['worldwide_gross'].apply(lambda x: int(x))))
df_1
```




![image](https://user-images.githubusercontent.com/101752113/163875187-7155e7db-80d8-4976-a4a3-f0889b4d36f5.png)



Now, all of our budget and gross numbers are integers !


Finally, I can substract 'worldwide_gross' from 'domestic_gross' to get the foreign_gross:


```python
df_1 ['foreign_gross']= df_1['worldwide_gross']- df_1['domestic_gross']

```

Lastly, I want to create a total profit column which is simply 'worldwide_gross' - 'production_budget' . I will also create domestic, foreign, and worldwide_ROI columns  respectively. These are easy ways to measure profitability. 


```python
# creating  total profit, worldwide ROI%, domestic ROI%, and foreign ROI% columns

df_1['total_profit']= df_1['worldwide_gross']- df_1['production_budget']
df_1['worldwide_ROI %']= (df_1['worldwide_gross'] / df_1['production_budget'])*100
df_1['domestic_ROI %']= (df_1['domestic_gross'] / df_1['production_budget'])*100
df_1['foreign_ROI %']= (df_1['foreign_gross'] / df_1['production_budget'])*100
df_1
```




![image](https://user-images.githubusercontent.com/101752113/163875377-23927747-346e-42ad-aa16-73cd54a484c0.png)

## Opening up SQL File "im.db.zip"
* This is pretty simple. I will import zipfile.
* Import SQLite3.
* Then, I will make the table into a Pandas Dataframe by selecting pieces of relevant data:


```python
# import zipfile
import zipfile
with zipfile.ZipFile("im.db.zip","r") as zip_ref:
    zip_ref.extractall(None)
```


```python
# opening sqlite3 to access database
import sqlite3
import sqlalchemy 

try:
    conn = sqlite3.connect("im.db")    
except Exception as e:
    print(e)

cursor = conn.cursor()
cursor.execute("SELECT name FROM sqlite_master WHERE type='table';")
print(f"Table Name : {cursor.fetchall()}")

df = pd.read_sql_query('SELECT * FROM known_for', conn)
```

    Table Name : [('movie_basics',), ('directors',), ('known_for',), ('movie_akas',), ('movie_ratings',), ('persons',), ('principals',), ('writers',)]
    

Here, I am choosing the 'genres' ,'primary_title', movie_id, and 'runtime_minutes' columns. They will be analyzed. 'primary_title' could be useful when I merge with df_1.

The other columns are irrelevant for our purposes.

Maybbe I'll need them later though.


```python
# Selecting movie_basics table to access genres and runtimes
df_0=pd.read_sql("""
SELECT genres,
primary_title,
movie_id,
runtime_minutes
FROM movie_basics

;""", conn)
df_0
```




![image](https://user-images.githubusercontent.com/101752113/163875607-2f0670e0-3eb0-4f95-8f75-a6154f132ea6.png)



Here we go. M&A Pandas style! I use an inner merge where every common 'movie' from df_1 and 'primary_title' from df_0 come together:


```python
#joining dataframes 
by_genre_df=df_1.merge(df_0, how= 'inner', left_on='movie', right_on='primary_title')
by_genre_df
```




![image](https://user-images.githubusercontent.com/101752113/163875766-00f2313f-a974-48ab-aceb-e2cc2de57187.png)



Let's use df.drop to eliminate duplicates by finding duplicate  any 'movie_id' and then deleting all but the 1st one.


```python
# dropping duplicates
by_genre_df.drop_duplicates(subset='movie_id', keep='first', inplace=True, ignore_index=False)
by_genre_df
```




![image](https://user-images.githubusercontent.com/101752113/163875933-8aaecd82-6422-4d2a-aa33-0a06922a6ecb.png)


600+ duplicates were dropped!

1 last piece of business: Re-ordering the table column.I want year, genre, and runtime to be more prominent. Also, I can just drop primary_title but simply not including it.






```python
#re-ordering columns
columnsTitles = ['movie', 'movie_id', 'Year', 'genres', 'runtime_minutes','production_budget', 'domestic_gross', 'foreign_gross','worldwide_gross', 'total_profit', 'domestic_ROI %', 'foreign_ROI %', 'worldwide_ROI %']


by_genre_df = by_genre_df.reindex(columns=columnsTitles)
by_genre_df
```


![image](https://user-images.githubusercontent.com/101752113/163876124-28440328-035f-4954-bd70-40d16575e19d.png)



Wait a minute! If I am going to use 'genres' and 'runtime_minutes' for analysis then I must use fillna to eliminate null values. For 'genres', I simply replace null values with 'Undetermined'. For 'runtime_minutes', I replace them with the median of the 'runtime_minutes' column.


```python
# replacing null values in 'genres' and 'runtime_minutes'
by_genre_df['genres'].fillna('Undetermined', inplace=True)
by_genre_df['runtime_minutes'].fillna(by_genre_df['runtime_minutes'].median(), inplace=True)
```

## Analysis and Visualization
* Let's look at the data!
    * First, let's examine the genres with highest ROI.
    * Then, we'll examine ROI by runtime.
    * Finally, we'll find the top 5 directors by ROI.


I will import the relevant libraries:


```python
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
```

I need to separate these films by genre if I am going to analyze them. So, let's use str.contains to make a list for each genre. Below is the result for the Adventure genre.


```python
# Isolating genres

Adventure=by_genre_df[by_genre_df.genres.str.contains('Adventure')]
Action=by_genre_df[by_genre_df.genres.str.contains('Action')]
Romance=by_genre_df[by_genre_df.genres.str.contains('Romance')]
Drama=by_genre_df[by_genre_df.genres.str.contains('Drama')]
Comedy=by_genre_df[by_genre_df.genres.str.contains('Comedy')]
Horror=by_genre_df[by_genre_df.genres.str.contains('Horror')]
Sci_Fi=by_genre_df[by_genre_df.genres.str.contains('Sci-Fi')]
Thriller=by_genre_df[by_genre_df.genres.str.contains('Thriller')]
Fantasy=by_genre_df[by_genre_df.genres.str.contains('Fantasy')]
Undetermined=by_genre_df[by_genre_df.genres.str.contains('Undetermined')]
Documentary=by_genre_df[by_genre_df.genres.str.contains('Documentary')]
Adventure
```


![image](https://user-images.githubusercontent.com/101752113/163876307-522cf65f-d03e-4a4f-9afe-a9642e46f358.png)




Let's find the median value for each and graph the top 5. For this, I have to use **itemgetter**.


```python
from operator import itemgetter, attrgetter

domestic_genre_cor= {'Adventure': Adventure['worldwide_ROI %'].median(),
           'Action': Action['worldwide_ROI %'].median(),
           'Romance':Romance['worldwide_ROI %'].median(),
           'Drama':Drama['worldwide_ROI %'].median(),
           'Comedy':Comedy['worldwide_ROI %'].median(),
           'Horror':Horror['worldwide_ROI %'].median(),
           'Sci_Fi':Sci_Fi['worldwide_ROI %'].median(),
           'Thriller':Thriller['worldwide_ROI %'].median(),
           'Fantasy':Fantasy['worldwide_ROI %'].median(),
           'Documentary': Documentary['worldwide_ROI %'].median(),
           'Undetermined':Undetermined['worldwide_ROI %'].median()}
n=sorted(domestic_genre_cor.items(), key=itemgetter(1))[5:]
top_5_genres_df= pd.DataFrame(n)
top_5_genres_df.rename( columns={0 :'Genre', 1:'ROI'}, inplace=True )
```

Now let's use a bar graph to illustrate the 5 highest genres by ROI.


```python
fig, ax1 = plt.subplots(ncols=1, figsize=(12,6))
ax1.bar(
    x=top_5_genres_df['Genre'],
    height=top_5_genres_df['ROI']
)
ax1.set_title("ROI by Genre")
ax1.set_xlabel("Genre")
ax1.set_ylabel("ROI")
```








    
![image](https://user-images.githubusercontent.com/101752113/163901013-277f6b97-18d3-456a-b086-bb0d97ce5687.png)

Hmmm...this leads to Recommendation #1: **Make Adventure, Sci-Fi, Comedy, Fantasy, Action, and Romance Films because they make the most money**.

Now let's examine runtimes:

Here's the description for runtimes:

* count    1416.000000
* mean      105.326977
* std        19.035017
* min        11.000000
* 25%        93.000000
* 50%       103.000000
* 75%       116.000000
* max       180.000000

I am going to use the 25th percentile as the 'short_film' threshold, the 25-75th percentiles for the 'average_length_film' threshold, and the 75th percentile as the 'long_film' threshold.


```python
#Creating new tables based on runtime
# median runtime is 103
run=by_genre_df["runtime_minutes"] 
# Up to 80 minutes
short_film = by_genre_df[run <= by_genre_df['runtime_minutes'].quantile(.25)]
# From 80+ to 116 minutes
average_length_film = by_genre_df[(run >90 )& (run <= 115) ]
# Over 116 minutes
long_film = by_genre_df[run >by_genre_df['runtime_minutes'].quantile(.75)]
```

Now let's define the median for each length. I will use the median as the average.


```python
long_film = by_genre_df[run >by_genre_df['runtime_minutes'].quantile(.75)]
long_film_ROI=long_film['worldwide_ROI %'].median()
average_length_film_ROI=average_length_film['worldwide_ROI %'].median()
short_film_ROI=short_film['worldwide_ROI %'].median()
average_length_film_ROI
```




    144.15465335664334



Now let's make a bar graph! 
* First, let's make a dictionary called 'film_length_ROIs' that includes all the median ROIs. 
* Then, let's sort and order. 
* Then, we'll make a DataFrame. 
* Finally, we'll graph the DataFrame.


```python
film_length_ROIs={'Short Films':short_film_ROI,
                 'Medium Length Films': average_length_film_ROI,
                 'Long Films':long_film_ROI}
ROIs_by_length=sorted(film_length_ROIs.items(), key=itemgetter(1))
Length_ROIs= pd.DataFrame(ROIs_by_length)

ROIS= pd.DataFrame(ROIs_by_length)
ROIS.rename( columns={0 :'Duration', 1:'ROI'}, inplace=True)
fig, ax1 = plt.subplots(ncols=1, figsize=(12,6))
ax1.bar(
    x=ROIS['Duration'],
    height=ROIS['ROI']
)
ax1.set_title("Duration by ROI")
ax1.set_xlabel("Duration")
ax1.set_ylabel("ROI")
```

![image](https://user-images.githubusercontent.com/101752113/163876637-0f973738-76c8-42f3-a6ac-32923610dfa0.png)

    
    


Recommendation #2: **Make long films. Their median ROI is almost 250%**. Medium length films aren't too bad either at almost 150%.

Now, I want to look at directors by ROI. In order to do that, I must open up a a few new databases.

Let's start by opening up the 'directors' and then the 'movie_akas' table.


```python
df_akas=pd.read_sql("""
SELECT movie_id
FROM movie_akas

;""", conn)
df_akas
```




![image](https://user-images.githubusercontent.com/101752113/163876703-b6d7775d-e685-4b1c-bb86-6924d142959b.png)



```python
df_directors=pd.read_sql("""
SELECT *
FROM directors

;""", conn)
df_directors
```




![image](https://user-images.githubusercontent.com/101752113/163876856-59812d92-f5b9-4cb0-b02c-07f7b4961285.png)



Now let's merge these 2 tables!


```python
directors_titles= df_akas.merge(df_directors, how='inner')
directors_titles
```




![image](https://user-images.githubusercontent.com/101752113/163877048-ce4c1830-7da1-431f-9f8f-0d3a77c90f45.png)




I need the names of the directors. To get the names, I will have to open the 'persons' table and then merge 'persons' with 
'directors'.


```python
df_persons=pd.read_sql("""
SELECT person_id,
primary_name
FROM persons

;""", conn)
df_persons
```




![image](https://user-images.githubusercontent.com/101752113/163877160-53b06bd5-6a00-42c3-b5e5-b10a0c1d755c.png)




Okay, let's merge these 2 tables!


```python
director_list_df=directors_titles.merge(df_persons, how= 'inner')
director_list_df
```


![image](https://user-images.githubusercontent.com/101752113/163877287-3ca661b5-8aa6-40d5-8529-af8ff1f7ddb8.png)



Ok, now I need to assign those directors to their respective films. To do that, I am going to merge 'director_list_df' with 'by_genre_df' on their shared 'movie_id' column. It's important to merge on 'movie_id' because it is unique to a film. Then, I will drop duplicates.


```python
Director_movie_df= director_list_df.merge(by_genre_df, how='inner', left_on='movie_id', right_on='movie_id')
Director_movie_df.drop_duplicates(subset='movie', keep='first', inplace=True, ignore_index=False)
Director_movie_df
```




![image](https://user-images.githubusercontent.com/101752113/163877390-71bf08fe-a100-4bc0-b047-c25f4342de60.png)




Nice table. Next, I want to limit my DataFrame to films where the 'production_budget' is greater than $5 mil. A budget less than 5mil. is considered a low budget film according to:
 
https://www.studiobinder.com/blog/production-budget/

While there is nothing wrong with low budget films, it's best to filter out the lower budget films.

```python
budget= Director_movie_df.loc[Director_movie_df['production_budget'] >= 5000000]

```

Next up:
* I will create a new a new dictionary called director_total.
* Then, I will use .aggregate to add the 'worldwide_ROI %' for each director. 
* Then I will show the top 10 directors.


```python
director_total = { 'primary_name': 'first', 'worldwide_ROI %': 'sum'}
dir_cumulative = budget.groupby(budget['primary_name']).aggregate(director_total)
dir_cumulative.sort_values(by=['worldwide_ROI %']).tail(10)
```




![image](https://user-images.githubusercontent.com/101752113/163879453-8d6a6060-e665-40e3-be35-de97437441ea.png)



Now, let's make the ROI%s into integers.


```python
dir_cumulative['worldwide_ROI %']=dir_cumulative['worldwide_ROI %'].apply(lambda x: int(x))

Actual_dir_total=dir_cumulative['worldwide_ROI %']
Actual_dir_total
```



![image](https://user-images.githubusercontent.com/101752113/163879716-b04d88fd-ef00-4436-bcbc-a7b17cd32630.png)


Then, I will make the above list into a dictionary.


```python
Dir_Total_dict=Actual_dir_total.to_dict()
```


Now:
* Let's sort the dictionary in reverse order using sorted, itemgetter, and reverse=True.
* Then let's make that sorted dictionary into a DataFrame.
* Next, I will rename the columns 'Director' and 'ROI' because they naturally come out as '0' and '1'.
* Lastly, let's take a look at the DataFrame.


```python
Dir_ROI= sorted(Dir_Total_dict.items(), key=itemgetter(1), reverse=True)[:5]
high_return_directors= pd.DataFrame(Dir_ROI)
high_return_directors.rename( columns={0 :'Director', 1:'ROI'}, inplace=True )
high_return_directors
```



![image](https://user-images.githubusercontent.com/101752113/163880357-36163308-b215-4cf9-9730-b67ef7ec17e0.png)

I'll finish it off by graphing the top 5 directors:


```python
fig, ax1 = plt.subplots(ncols=1, figsize=(12,6))
ax1.bar(
    x=high_return_directors['Director'],
    height=high_return_directors['ROI']
)
ax1.set_title("Top 5 Directors by ROI")
ax1.set_xlabel("Director")
ax1.set_ylabel("ROI")
```

![image](https://user-images.githubusercontent.com/101752113/163880411-51985ed7-a69f-4729-98fa-829def16e036.png)


Recommendation # 3: **If at all possible, hire M. Night Shyamalan, James DeMonaco, Christopher Landon, Jordan Peele, or Pierre Coffin because they have the highest ROI%**.


```python
conn.close()

```


```python

```
