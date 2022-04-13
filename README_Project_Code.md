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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>$425,000,000</td>
      <td>$760,507,625</td>
      <td>$2,776,345,279</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>$410,600,000</td>
      <td>$241,063,875</td>
      <td>$1,045,663,875</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>$350,000,000</td>
      <td>$42,762,350</td>
      <td>$149,762,350</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$459,005,868</td>
      <td>$1,403,013,963</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>$317,000,000</td>
      <td>$620,181,382</td>
      <td>$1,316,721,747</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>$7,000</td>
      <td>$0</td>
      <td>$0</td>
    </tr>
    <tr>
      <th>5778</th>
      <td>79</td>
      <td>Apr 2, 1999</td>
      <td>Following</td>
      <td>$6,000</td>
      <td>$48,482</td>
      <td>$240,495</td>
    </tr>
    <tr>
      <th>5779</th>
      <td>80</td>
      <td>Jul 13, 2005</td>
      <td>Return to the Land of Wonders</td>
      <td>$5,000</td>
      <td>$1,338</td>
      <td>$1,338</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>$1,400</td>
      <td>$0</td>
      <td>$0</td>
    </tr>
    <tr>
      <th>5781</th>
      <td>82</td>
      <td>Aug 5, 2005</td>
      <td>My Date With Drew</td>
      <td>$1,100</td>
      <td>$181,041</td>
      <td>$181,041</td>
    </tr>
  </tbody>
</table>
<p>5782 rows × 6 columns</p>
</div>



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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>317000000</td>
      <td>620181382</td>
      <td>1316721747</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Dec 18, 2015</td>
      <td>Star Wars Ep. VII: The Force Awakens</td>
      <td>306000000</td>
      <td>936662225</td>
      <td>2053311220</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>2048134200</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5761</th>
      <td>62</td>
      <td>Dec 31, 2014</td>
      <td>Stories of Our Lives</td>
      <td>15000</td>
      <td>0</td>
      <td>0</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>5771</th>
      <td>72</td>
      <td>May 19, 2015</td>
      <td>Family Motocross</td>
      <td>10000</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>5772</th>
      <td>73</td>
      <td>Jan 13, 2012</td>
      <td>Newlyweds</td>
      <td>9000</td>
      <td>4584</td>
      <td>4584</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
<p>1666 rows × 7 columns</p>
</div>



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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>425000000</td>
      <td>760507625</td>
      <td>2776345279</td>
      <td>2009</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>241063875</td>
      <td>1045663875</td>
      <td>2011</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>317000000</td>
      <td>620181382</td>
      <td>1316721747</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>5778</th>
      <td>79</td>
      <td>Apr 2, 1999</td>
      <td>Following</td>
      <td>6000</td>
      <td>48482</td>
      <td>240495</td>
      <td>1999</td>
    </tr>
    <tr>
      <th>5779</th>
      <td>80</td>
      <td>Jul 13, 2005</td>
      <td>Return to the Land of Wonders</td>
      <td>5000</td>
      <td>1338</td>
      <td>1338</td>
      <td>2005</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>5781</th>
      <td>82</td>
      <td>Aug 5, 2005</td>
      <td>My Date With Drew</td>
      <td>1100</td>
      <td>181041</td>
      <td>181041</td>
      <td>2005</td>
    </tr>
  </tbody>
</table>
<p>5782 rows × 7 columns</p>
</div>



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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>Year</th>
      <th>foreign_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>2019</td>
      <td>107000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>2015</td>
      <td>944008095</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>317000000</td>
      <td>620181382</td>
      <td>1316721747</td>
      <td>2017</td>
      <td>696540365</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Dec 18, 2015</td>
      <td>Star Wars Ep. VII: The Force Awakens</td>
      <td>306000000</td>
      <td>936662225</td>
      <td>2053311220</td>
      <td>2015</td>
      <td>1116648995</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>2048134200</td>
      <td>2018</td>
      <td>1369318718</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>5761</th>
      <td>62</td>
      <td>Dec 31, 2014</td>
      <td>Stories of Our Lives</td>
      <td>15000</td>
      <td>0</td>
      <td>0</td>
      <td>2014</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5771</th>
      <td>72</td>
      <td>May 19, 2015</td>
      <td>Family Motocross</td>
      <td>10000</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5772</th>
      <td>73</td>
      <td>Jan 13, 2012</td>
      <td>Newlyweds</td>
      <td>9000</td>
      <td>4584</td>
      <td>4584</td>
      <td>2012</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>2018</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>1666 rows × 8 columns</p>
</div>



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
      <th>genres</th>
      <th>primary_title</th>
      <th>runtime_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Action,Crime,Drama</td>
      <td>Sunghursh</td>
      <td>175.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Biography,Drama</td>
      <td>One Day Before the Rainy Season</td>
      <td>114.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Drama</td>
      <td>The Other Side of the Wind</td>
      <td>122.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Comedy,Drama</td>
      <td>Sabse Bada Sukh</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Comedy,Drama,Fantasy</td>
      <td>The Wandering Soap Opera</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>146139</th>
      <td>Drama</td>
      <td>Kuambil Lagi Hatiku</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>146140</th>
      <td>Documentary</td>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146141</th>
      <td>Comedy</td>
      <td>Dankyavar Danka</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146142</th>
      <td>None</td>
      <td>6 Gunn</td>
      <td>116.0</td>
    </tr>
    <tr>
      <th>146143</th>
      <td>Documentary</td>
      <td>Chico Albuquerque - Revelações</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>146144 rows × 3 columns</p>
</div>



Here we go. M&A Pandas style! I use an inner join where every common 'movie' from df_1 and 'primary_title' from df_0 come together:


```python
#joining dataframes 
by_genre_df=df_1.merge(df_0, how= 'inner', left_on='movie', right_on='primary_title')
by_genre_df
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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>Year</th>
      <th>foreign_gross</th>
      <th>total_profit</th>
      <th>worldwide_ROI %</th>
      <th>domestic_ROI %</th>
      <th>foreign_ROI %</th>
      <th>genres</th>
      <th>primary_title</th>
      <th>runtime_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>2019</td>
      <td>107000000</td>
      <td>-200237650</td>
      <td>42.789243</td>
      <td>12.217814</td>
      <td>30.571429</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Dark Phoenix</td>
      <td>113.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>2015</td>
      <td>944008095</td>
      <td>1072413963</td>
      <td>424.384139</td>
      <td>138.840250</td>
      <td>285.543888</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Avengers: Age of Ultron</td>
      <td>141.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>2048134200</td>
      <td>2018</td>
      <td>1369318718</td>
      <td>1748134200</td>
      <td>682.711400</td>
      <td>226.271827</td>
      <td>456.439573</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Avengers: Infinity War</td>
      <td>149.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>Nov 17, 2017</td>
      <td>Justice League</td>
      <td>300000000</td>
      <td>229024295</td>
      <td>655945209</td>
      <td>2017</td>
      <td>426920914</td>
      <td>355945209</td>
      <td>218.648403</td>
      <td>76.341432</td>
      <td>142.306971</td>
      <td>Action,Adventure,Fantasy</td>
      <td>Justice League</td>
      <td>120.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10</td>
      <td>Nov 6, 2015</td>
      <td>Spectre</td>
      <td>300000000</td>
      <td>200074175</td>
      <td>879620923</td>
      <td>2015</td>
      <td>679546748</td>
      <td>579620923</td>
      <td>293.206974</td>
      <td>66.691392</td>
      <td>226.515583</td>
      <td>Action,Adventure,Thriller</td>
      <td>Spectre</td>
      <td>148.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2053</th>
      <td>57</td>
      <td>Apr 21, 2015</td>
      <td>The Front Man</td>
      <td>20000</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
      <td>-20000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Biography,Comedy,Documentary</td>
      <td>The Front Man</td>
      <td>73.0</td>
    </tr>
    <tr>
      <th>2054</th>
      <td>62</td>
      <td>Dec 31, 2014</td>
      <td>Stories of Our Lives</td>
      <td>15000</td>
      <td>0</td>
      <td>0</td>
      <td>2014</td>
      <td>0</td>
      <td>-15000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Drama</td>
      <td>Stories of Our Lives</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>2055</th>
      <td>73</td>
      <td>Jan 13, 2012</td>
      <td>Newlyweds</td>
      <td>9000</td>
      <td>4584</td>
      <td>4584</td>
      <td>2012</td>
      <td>0</td>
      <td>-4416</td>
      <td>50.933333</td>
      <td>50.933333</td>
      <td>0.000000</td>
      <td>Comedy,Drama</td>
      <td>Newlyweds</td>
      <td>95.0</td>
    </tr>
    <tr>
      <th>2056</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>2018</td>
      <td>0</td>
      <td>-7000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Horror,Sci-Fi,Thriller</td>
      <td>Red 11</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>2057</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
      <td>-1400</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Drama,Horror,Thriller</td>
      <td>A Plague So Pleasant</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
<p>2058 rows × 15 columns</p>
</div>



Let's use df.drop to eliminate duplicates.


```python
# dropping duplicates
by_genre_df.drop_duplicates(subset='movie', keep='first', inplace=True, ignore_index=False)
by_genre_df
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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>Year</th>
      <th>foreign_gross</th>
      <th>total_profit</th>
      <th>worldwide_ROI %</th>
      <th>domestic_ROI %</th>
      <th>foreign_ROI %</th>
      <th>genres</th>
      <th>primary_title</th>
      <th>runtime_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>2019</td>
      <td>107000000</td>
      <td>-200237650</td>
      <td>42.789243</td>
      <td>12.217814</td>
      <td>30.571429</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Dark Phoenix</td>
      <td>113.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>2015</td>
      <td>944008095</td>
      <td>1072413963</td>
      <td>424.384139</td>
      <td>138.840250</td>
      <td>285.543888</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Avengers: Age of Ultron</td>
      <td>141.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>2048134200</td>
      <td>2018</td>
      <td>1369318718</td>
      <td>1748134200</td>
      <td>682.711400</td>
      <td>226.271827</td>
      <td>456.439573</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>Avengers: Infinity War</td>
      <td>149.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9</td>
      <td>Nov 17, 2017</td>
      <td>Justice League</td>
      <td>300000000</td>
      <td>229024295</td>
      <td>655945209</td>
      <td>2017</td>
      <td>426920914</td>
      <td>355945209</td>
      <td>218.648403</td>
      <td>76.341432</td>
      <td>142.306971</td>
      <td>Action,Adventure,Fantasy</td>
      <td>Justice League</td>
      <td>120.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10</td>
      <td>Nov 6, 2015</td>
      <td>Spectre</td>
      <td>300000000</td>
      <td>200074175</td>
      <td>879620923</td>
      <td>2015</td>
      <td>679546748</td>
      <td>579620923</td>
      <td>293.206974</td>
      <td>66.691392</td>
      <td>226.515583</td>
      <td>Action,Adventure,Thriller</td>
      <td>Spectre</td>
      <td>148.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2053</th>
      <td>57</td>
      <td>Apr 21, 2015</td>
      <td>The Front Man</td>
      <td>20000</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
      <td>-20000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Biography,Comedy,Documentary</td>
      <td>The Front Man</td>
      <td>73.0</td>
    </tr>
    <tr>
      <th>2054</th>
      <td>62</td>
      <td>Dec 31, 2014</td>
      <td>Stories of Our Lives</td>
      <td>15000</td>
      <td>0</td>
      <td>0</td>
      <td>2014</td>
      <td>0</td>
      <td>-15000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Drama</td>
      <td>Stories of Our Lives</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>2055</th>
      <td>73</td>
      <td>Jan 13, 2012</td>
      <td>Newlyweds</td>
      <td>9000</td>
      <td>4584</td>
      <td>4584</td>
      <td>2012</td>
      <td>0</td>
      <td>-4416</td>
      <td>50.933333</td>
      <td>50.933333</td>
      <td>0.000000</td>
      <td>Comedy,Drama</td>
      <td>Newlyweds</td>
      <td>95.0</td>
    </tr>
    <tr>
      <th>2056</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>2018</td>
      <td>0</td>
      <td>-7000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Horror,Sci-Fi,Thriller</td>
      <td>Red 11</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>2057</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>2015</td>
      <td>0</td>
      <td>-1400</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>Drama,Horror,Thriller</td>
      <td>A Plague So Pleasant</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
<p>1416 rows × 15 columns</p>
</div>



600+ duplicates were dropped!

1 last piece of business: Re-ordering the table column.I want year, genre, and runtime to be more prominent. Also, I can just drop primary_title but simply not including it.


```python
#re-ordering columns
columnsTitles = ['movie', 'Year', 'genres', 'runtime_minutes','production_budget', 'domestic_gross', 'foreign_gross','worldwide_gross', 'total_profit', 'domestic_ROI %', 'foreign_ROI %', 'worldwide_ROI %']


by_genre_df = by_genre_df.reindex(columns=columnsTitles)
by_genre_df
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
      <th>movie</th>
      <th>Year</th>
      <th>genres</th>
      <th>runtime_minutes</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>worldwide_gross</th>
      <th>total_profit</th>
      <th>domestic_ROI %</th>
      <th>foreign_ROI %</th>
      <th>worldwide_ROI %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Dark Phoenix</td>
      <td>2019</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>113.0</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>107000000</td>
      <td>149762350</td>
      <td>-200237650</td>
      <td>12.217814</td>
      <td>30.571429</td>
      <td>42.789243</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Avengers: Age of Ultron</td>
      <td>2015</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>141.0</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>944008095</td>
      <td>1403013963</td>
      <td>1072413963</td>
      <td>138.840250</td>
      <td>285.543888</td>
      <td>424.384139</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Avengers: Infinity War</td>
      <td>2018</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>149.0</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>1369318718</td>
      <td>2048134200</td>
      <td>1748134200</td>
      <td>226.271827</td>
      <td>456.439573</td>
      <td>682.711400</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Justice League</td>
      <td>2017</td>
      <td>Action,Adventure,Fantasy</td>
      <td>120.0</td>
      <td>300000000</td>
      <td>229024295</td>
      <td>426920914</td>
      <td>655945209</td>
      <td>355945209</td>
      <td>76.341432</td>
      <td>142.306971</td>
      <td>218.648403</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Spectre</td>
      <td>2015</td>
      <td>Action,Adventure,Thriller</td>
      <td>148.0</td>
      <td>300000000</td>
      <td>200074175</td>
      <td>679546748</td>
      <td>879620923</td>
      <td>579620923</td>
      <td>66.691392</td>
      <td>226.515583</td>
      <td>293.206974</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2053</th>
      <td>The Front Man</td>
      <td>2015</td>
      <td>Biography,Comedy,Documentary</td>
      <td>73.0</td>
      <td>20000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-20000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2054</th>
      <td>Stories of Our Lives</td>
      <td>2014</td>
      <td>Drama</td>
      <td>60.0</td>
      <td>15000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-15000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2055</th>
      <td>Newlyweds</td>
      <td>2012</td>
      <td>Comedy,Drama</td>
      <td>95.0</td>
      <td>9000</td>
      <td>4584</td>
      <td>0</td>
      <td>4584</td>
      <td>-4416</td>
      <td>50.933333</td>
      <td>0.000000</td>
      <td>50.933333</td>
    </tr>
    <tr>
      <th>2056</th>
      <td>Red 11</td>
      <td>2018</td>
      <td>Horror,Sci-Fi,Thriller</td>
      <td>77.0</td>
      <td>7000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-7000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2057</th>
      <td>A Plague So Pleasant</td>
      <td>2015</td>
      <td>Drama,Horror,Thriller</td>
      <td>76.0</td>
      <td>1400</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-1400</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>1416 rows × 12 columns</p>
</div>



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
      <th>movie</th>
      <th>Year</th>
      <th>genres</th>
      <th>runtime_minutes</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>worldwide_gross</th>
      <th>total_profit</th>
      <th>domestic_ROI %</th>
      <th>foreign_ROI %</th>
      <th>worldwide_ROI %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Dark Phoenix</td>
      <td>2019</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>113.0</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>107000000</td>
      <td>149762350</td>
      <td>-200237650</td>
      <td>12.217814</td>
      <td>30.571429</td>
      <td>42.789243</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Avengers: Age of Ultron</td>
      <td>2015</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>141.0</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>944008095</td>
      <td>1403013963</td>
      <td>1072413963</td>
      <td>138.840250</td>
      <td>285.543888</td>
      <td>424.384139</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Avengers: Infinity War</td>
      <td>2018</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>149.0</td>
      <td>300000000</td>
      <td>678815482</td>
      <td>1369318718</td>
      <td>2048134200</td>
      <td>1748134200</td>
      <td>226.271827</td>
      <td>456.439573</td>
      <td>682.711400</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Justice League</td>
      <td>2017</td>
      <td>Action,Adventure,Fantasy</td>
      <td>120.0</td>
      <td>300000000</td>
      <td>229024295</td>
      <td>426920914</td>
      <td>655945209</td>
      <td>355945209</td>
      <td>76.341432</td>
      <td>142.306971</td>
      <td>218.648403</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Spectre</td>
      <td>2015</td>
      <td>Action,Adventure,Thriller</td>
      <td>148.0</td>
      <td>300000000</td>
      <td>200074175</td>
      <td>679546748</td>
      <td>879620923</td>
      <td>579620923</td>
      <td>66.691392</td>
      <td>226.515583</td>
      <td>293.206974</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1868</th>
      <td>Lowriders</td>
      <td>2017</td>
      <td>Adventure,Crime,Drama</td>
      <td>98.0</td>
      <td>916000</td>
      <td>6179955</td>
      <td>8466</td>
      <td>6188421</td>
      <td>5272421</td>
      <td>674.667576</td>
      <td>0.924236</td>
      <td>675.591812</td>
    </tr>
    <tr>
      <th>1927</th>
      <td>Detention of the Dead</td>
      <td>2013</td>
      <td>Action,Adventure,Comedy</td>
      <td>87.0</td>
      <td>500000</td>
      <td>1778</td>
      <td>0</td>
      <td>1778</td>
      <td>-498222</td>
      <td>0.355600</td>
      <td>0.000000</td>
      <td>0.355600</td>
    </tr>
    <tr>
      <th>1950</th>
      <td>Rodeo Girl</td>
      <td>2015</td>
      <td>Adventure,Family</td>
      <td>108.0</td>
      <td>500000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1978</th>
      <td>The Love Letter</td>
      <td>2015</td>
      <td>Adventure,Documentary</td>
      <td>12.0</td>
      <td>250000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-250000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1979</th>
      <td>Western Religion</td>
      <td>2015</td>
      <td>Adventure,Drama,Fantasy</td>
      <td>105.0</td>
      <td>250000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-250000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>302 rows × 12 columns</p>
</div>



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

![image](https://user-images.githubusercontent.com/101752113/163193137-e789ada6-f5a5-4f50-905d-33e870ce8ba0.png)


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
