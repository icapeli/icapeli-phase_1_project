# Final Project : Evlauating the Movie Business for Microsoft's next big move!
The goal of this project is to analyze data from imdb to assist Microsoft, or any new player, in taking on the film industry. I use the **'tn.movie_budgets.csv.gz'** and **im.db** to analyze the budget and movie details for each film. Then, I will provide conclusions for the client to use in the future.


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




![image](https://user-images.githubusercontent.com/101752113/163199749-5137119c-d9cb-40ea-9901-4a7d9d5bb4da.png)



Next, I create a new column called 'Year' using Pandas DatetimeIndex class.


```python
# Creating a year column
df_1['Year'] = pd.DatetimeIndex(df_1['release_date']).year  

```

## Preparing and Cleaning

* First, we have to limit the year to 2012 and above. It's a little arbitrary but it's still a lot of data plus many of the relevant players were already around by that time. 
* I would like to create a 'foreign_gross' column that is the difference between the worldwide and domestic gross. Unfortunately, right now, the budget and both gross figures are strings and not integers.
* Lastly, I will create ROI% columns.

Limiting to 2012:


```python
#Limiting to past 10 years
df_1=df_1.loc[df_1['Year'] > 2011]
df_1
```




![image](https://user-images.githubusercontent.com/101752113/163200551-d08428bc-9900-413d-a834-046cd16bf777.png)



Then, I use a lambda function to replace the $ sign with an empty space:


```python
#going to make budget and gross columns into ints so must get rid of $ and commas
#getting rid of $ 
df_1['production_budget']=df_1['production_budget'].apply(lambda x: x.replace('$', ''))
df_1['domestic_gross']=df_1['domestic_gross'].apply(lambda x: x.replace('$', ''))
df_1['worldwide_gross']=df_1['worldwide_gross'].apply(lambda x: x.replace('$', ''))

```

Now, I employ more lambda functions to get rid of the commas (,) inside every monetary value:


```python
#getting rid of commas in numbers 
df_1['production_budget']=df_1['production_budget'].apply(lambda x: x.replace(',', ''))
df_1['domestic_gross']=df_1['domestic_gross'].apply(lambda x: x.replace(',', ''))
df_1['worldwide_gross']=df_1['worldwide_gross'].apply(lambda x: x.replace(',', ''))
df_1
```




![image](https://user-images.githubusercontent.com/101752113/163200853-e7ae5e96-7eec-40ef-9596-1fc8dd093c80.png)


The love affair with lambda functions continue as each column will be converted from a string into an integer:


```python
# converting into integers
df_1['production_budget']=df_1['production_budget'].apply(lambda x: int(x))
df_1['domestic_gross']=df_1['domestic_gross'].apply(lambda x:int(x))
df_1['worldwide_gross']=df_1['worldwide_gross'].apply(lambda x:int(x))

```

Now, all of our budget and gross numbers are integers !

Finally, we can substract 'worldwide_gross' from 'domestic_gross' to get the foreign_gross:


```python
# creating a foreign gross column
df_1 ['foreign_gross']= df_1['worldwide_gross']- df_1['domestic_gross']

```


![image](https://user-images.githubusercontent.com/101752113/163201120-8e149477-5932-44c2-b0ab-d6659107c8ff.png)





Lastly, I want to create a total profit column which is simply 'worldwide_gross' - 'production_budget' and also domestic, foreign, and worldwide_ROI respectively. These are easy ways to measure profitability. 


```python
# creating  total profit, worldwide ROI%, domestic ROI%, and foreign ROI% columns

df_1['total_profit']= df_1['worldwide_gross']- df_1['production_budget']
df_1['worldwide_ROI %']= (df_1['worldwide_gross'] / df_1['production_budget'])*100
df_1['domestic_ROI %']= (df_1['domestic_gross'] / df_1['production_budget'])*100
df_1['foreign_ROI %']= (df_1['foreign_gross'] / df_1['production_budget'])*100

```

## Opening up SQL File "im.db.zip"
* This is pretty simple. I will import zipfile.
* Import SQLite3.
* Then, I will make the table into pandas Dataframe by selecting pieces of relevant data.


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

Here, I am choosing the 'genres' and the 'runtime_minutes' columns. They will be analyzed. 'primary_title' could be useful when I merge with df_1.

The other columns are irrelevant for our purposes.

You can use them in another project though!



```python
# Selecting movie_basics table to access genres and runtimes
df_0=pd.read_sql("""
SELECT genres,
primary_title,
runtime_minutes
FROM movie_basics

;""", conn)
df_0
```




![image](https://user-images.githubusercontent.com/101752113/163201531-318952d1-8667-4d7a-a9e1-8d7377f4a66d.png)


Here we go. M&A Pandas style! I use an inner join where every common 'movie' from df_1 and 'primary_title' from df_0 come together:


```python
#joining dataframes 
by_genre_df=df_1.merge(df_0, how= 'inner', left_on='movie', right_on='primary_title')
by_genre_df
```

![image](https://user-images.githubusercontent.com/101752113/163201723-fbcd9ca0-8305-405d-8121-2b0cfeb87902.png)


Let's use df.drop to eliminate duplicates.


```python
# dropping duplicates
by_genre_df.drop_duplicates(subset='movie', keep='first', inplace=True, ignore_index=False)
by_genre_df
```




![image](https://user-images.githubusercontent.com/101752113/163201928-13d9c053-d292-4496-bef4-fb9376438036.png)


600+ duplicates were dropped!

1 last piece of business: Re-ordering the table column.I want year, genre, and runtime to be more prominent. Also, I can just drop primary_title but simply not including it.


```python
#re-ordering columns
columnsTitles = ['movie', 'Year', 'genres', 'runtime_minutes','production_budget', 'domestic_gross', 'foreign_gross','worldwide_gross', 'total_profit', 'domestic_ROI %', 'foreign_ROI %', 'worldwide_ROI %']


by_genre_df = by_genre_df.reindex(columns=columnsTitles)
by_genre_df
```




![image](https://user-images.githubusercontent.com/101752113/163202250-42551bbd-0dea-4001-9ce5-98f150186ddb.png)


Wait a minute! If I am going to use 'genres' and 'runtime_minutes' for analysis then I must use fillna to eliminate null values.
For 'genres', I simply replace null values with 'Undetermined'. For 'runtime_minutes', I replace them with the median of the 'runtime_minutes' column.


```python
# replacing null values in 'genres' and 'runtime_minutes'
by_genre_df['genres'].fillna('Undetermined', inplace=True)
by_genre_df['runtime_minutes'].fillna(by_genre_df['runtime_minutes'].median(), inplace=True)
```

## Analysis and Visualization
* Let's look at the data!
    * First, let's examine the correlation between budget and our various measures of return.
    * Then, we'll look at the genres and their relation to our measures of return.
    * Finally, we'll explore runtime and its relationship to our measures of return.

We have to import the relevant libraries and then I make a dictionary called correlations which contains keys called correlations and values that are the Pearson correlation for budget and various measures of return. Then we plot them into a bar graph. I know it may not be that dynamic but there are only 4 values here so a bar graph works for this.


```python
#importing libraries and using them to create a bar graph between production_budget and various return correlations

import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
correlations={'Budget-Profit Correlation':by_genre_df['production_budget'].corr(by_genre_df['total_profit']),
                   'Budget-Domestic Gross Correlation':by_genre_df['production_budget'].corr(by_genre_df['domestic_ROI %']),
                   'Budget-Foreign Gross Correlation':by_genre_df['production_budget'].corr(by_genre_df['foreign_ROI %']),
                   'Budget-Worldwide Gross Correlation': by_genre_df['production_budget'].corr(by_genre_df['worldwide_ROI %'])}
data= pd.DataFrame(correlations, index=[2])
data.plot(kind='bar')

```




   
![image](https://user-images.githubusercontent.com/101752113/163188981-9ebc27b3-b66c-42ab-aace-93e701f1ae91.png)

Production Budget and Total Profits have a fairly strong correlation. Otherwise, nothing else correlates. Therefore Recommendation  # 1 is **Go big, budget and profit correlate fairly well so splash the cash if you want big profits**.

We need to separate these films by genre if we are going to analyze them. So, let's use str.contains to make a list for each genre. Below is the result for the Adventure genre.


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





![image](https://user-images.githubusercontent.com/101752113/163202557-e815f1a5-0c16-4bb4-92bc-0e06e31120fa.png)

Again, I want to see how the budgets in these genres correlate with return. So we'll make bar graphs. I would have done a clustered bar graph but with so many genres the result would have been unintelligible.


```python
#Domestic ROI correlation  by genre
domestic_genre_cor= {'Adventure': Adventure['production_budget'].corr(Adventure['domestic_ROI %']), 
           'Action': Action['production_budget'].corr(Action['domestic_ROI %']),
           'Romance':Romance['production_budget'].corr(Romance['domestic_ROI %']),
           'Drama':Drama['production_budget'].corr(Drama['domestic_ROI %']),
           'Comedy':Comedy['production_budget'].corr(Comedy['domestic_ROI %']),
           'Horror':Horror['production_budget'].corr(Horror['domestic_ROI %']),
           'Sci_Fi':Sci_Fi['production_budget'].corr(Sci_Fi['domestic_ROI %']),
           'Thriller':Thriller['production_budget'].corr(Thriller['domestic_ROI %']),
           'Fantasy':Fantasy['production_budget'].corr(Fantasy['domestic_ROI %']),
           'Documentary': Documentary['production_budget'].corr(Documentary['domestic_ROI %']),
           'Undetermined':Undetermined['production_budget'].corr(Undetermined['domestic_ROI %'])}
Genre_Correlation =pd.DataFrame(domestic_genre_cor, index=[1])
Genre_Correlation.plot(kind='bar')
ax.set_ylim([-1,1])
```

![image](https://user-images.githubusercontent.com/101752113/163193236-f154059a-d2fc-48a4-a0e9-88bbdba7b1f0.png)

    



```python
#Foreign ROI correlation by genre
more_cor_1= {'Adventure': Adventure['production_budget'].corr(Adventure['foreign_ROI %']), 
           'Action': Action['production_budget'].corr(Action['foreign_ROI %']),
           'Romance':Romance['production_budget'].corr(Romance['foreign_ROI %']),
           'Drama':Drama['production_budget'].corr(Drama['foreign_ROI %']),
           'Comedy':Comedy['production_budget'].corr(Comedy['foreign_ROI %']),
           'Horror':Horror['production_budget'].corr(Horror['foreign_ROI %']),
           'Sci_Fi':Sci_Fi['production_budget'].corr(Sci_Fi['foreign_ROI %']),
           'Thriller':Thriller['production_budget'].corr(Thriller['foreign_ROI %']),
           'Fantasy':Fantasy['production_budget'].corr(Fantasy['foreign_ROI %']),
           'Documentary': Documentary['production_budget'].corr(Documentary['foreign_ROI %']),
           'Undetermined':Undetermined['production_budget'].corr(Undetermined['foreign_ROI %'])}
Genre_Corre =pd.DataFrame(more_cor_1, index=[1])
Genre_Corre.plot(kind='bar')
```


![image](https://user-images.githubusercontent.com/101752113/163195274-34dc03e2-76db-476a-ace5-c5922d9fc573.png) 

```python
# Worldwide ROI correlation by genre
more_cor_2= {'Adventure': Adventure['production_budget'].corr(Adventure['worldwide_ROI %']), 
           'Action': Action['production_budget'].corr(Action['worldwide_ROI %']),
           'Romance':Romance['production_budget'].corr(Romance['worldwide_ROI %']),
           'Drama':Drama['production_budget'].corr(Drama['worldwide_ROI %']),
           'Comedy':Comedy['production_budget'].corr(Comedy['worldwide_ROI %']),
           'Horror':Horror['production_budget'].corr(Horror['worldwide_ROI %']),
           'Sci_Fi':Sci_Fi['production_budget'].corr(Sci_Fi['worldwide_ROI %']),
           'Thriller':Thriller['production_budget'].corr(Thriller['worldwide_ROI %']),
           'Fantasy':Fantasy['production_budget'].corr(Fantasy['worldwide_ROI %']),
           'Documentary': Documentary['production_budget'].corr(Documentary['worldwide_ROI %']),
           'Undetermined':Undetermined['production_budget'].corr(Undetermined['worldwide_ROI %'])}
Genre_Corre =pd.DataFrame(more_cor_2, index=[1])
Genre_Corre.plot(kind='bar')
```
![image](https://user-images.githubusercontent.com/101752113/163192732-0d77cc85-3e34-4fee-99ea-55fc4c606eab.png)


Recommendation # 2 is **Stick with Action, Adventure, and Sci-Film because they have the best correlation with return although overall they are not at all strong. However, things are rosier in foreign markets.**

Now let's check out runtimes. **Run** will equal by_genre_df["runtime_minutes"]. Let's separate them into short films( up to 80 minutes), average_length_film (from 80 to 116), and finally long_films(above 116).


```python
#Creating new tables based on runtime
# median runtime is 103
run=by_genre_df["runtime_minutes"]
# Up to 80 minutes
short_film = by_genre_df[run <= 80]
# From 80+ to 116 minutes
average_length_film = by_genre_df[(run >80 )& (run <= 116) ]
# Over 116 minutes
long_film = by_genre_df[run > 116]
```

Now let's make some scatter plots!

Should be interesting!!


```python
# ROI vs Runtime Scatter Plot
fig, ax = plt.subplots(figsize=(20, 10))

ax.scatter(
    y=average_length_film['domestic_ROI %'],
    x=average_length_film['runtime_minutes'],
    alpha=.5,
    color="crimson",
    label="Average Length Film"
)
ax.scatter(
    y=short_film['domestic_ROI %'],
    x=short_film['runtime_minutes'],
    alpha=.5,
    color="lime",
    label="Short Film"
)
ax.scatter(
    y=long_film['domestic_ROI %'],
    x= long_film['runtime_minutes'],
    alpha=.5,
    color="gray",
    label="Long Film"
)
ax.set_xlabel("Runtime")
ax.set_ylabel("ROI ")
ax.set_title("ROI vs Runtime")
ax.set_ylim([0, 500])
ax.legend();
```


    
![image](https://user-images.githubusercontent.com/101752113/163193687-1541f61b-6cdf-4e1c-bc53-308924eed14d.png)    


So, ROI starts to pick up around 80 minutes. Therefore, here's Recommendation # 3: **Make movies between 80-150 minutes**. That's where the highest ROI films cluster.


```python
conn.close()
```
