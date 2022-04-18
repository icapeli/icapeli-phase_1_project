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

* First, I have to limit the year to 2012 and above. It's a little arbitrary but it's still a lot of data plus many of the relevant players were already around by that time. 
* I would like to create a 'foreign_gross' column that is the difference between the worldwide and domestic gross. Unfortunately, right now, the budget and both gross figures are strings and not integers.
* Lastly, I will create ROI% columns.


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
      <td>$350,000,000</td>
      <td>$42,762,350</td>
      <td>$149,762,350</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$459,005,868</td>
      <td>$1,403,013,963</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>$317,000,000</td>
      <td>$620,181,382</td>
      <td>$1,316,721,747</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Dec 18, 2015</td>
      <td>Star Wars Ep. VII: The Force Awakens</td>
      <td>$306,000,000</td>
      <td>$936,662,225</td>
      <td>$2,053,311,220</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>6</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>$300,000,000</td>
      <td>$678,815,482</td>
      <td>$2,048,134,200</td>
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
      <td>$15,000</td>
      <td>$0</td>
      <td>$0</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>5771</th>
      <td>72</td>
      <td>May 19, 2015</td>
      <td>Family Motocross</td>
      <td>$10,000</td>
      <td>$0</td>
      <td>$0</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>5772</th>
      <td>73</td>
      <td>Jan 13, 2012</td>
      <td>Newlyweds</td>
      <td>$9,000</td>
      <td>$4,584</td>
      <td>$4,584</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>78</td>
      <td>Dec 31, 2018</td>
      <td>Red 11</td>
      <td>$7,000</td>
      <td>$0</td>
      <td>$0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>5780</th>
      <td>81</td>
      <td>Sep 29, 2015</td>
      <td>A Plague So Pleasant</td>
      <td>$1,400</td>
      <td>$0</td>
      <td>$0</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
<p>1666 rows × 7 columns</p>
</div>



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

The love affair with lambda functions continue as each column will be converted from a string into an integer:


```python
df_1 = df_1.assign(production_budget= (df_1['production_budget'].apply(lambda x: int(x))))
df_1 = df_1.assign(domestic_gross= (df_1['domestic_gross'].apply(lambda x: int(x))))
df_1 = df_1.assign(worldwide_gross= (df_1['worldwide_gross'].apply(lambda x: int(x))))
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



Now, all of our budget and gross numbers are integers !


Finally, I can substract 'worldwide_gross' from 'domestic_gross' to get the foreign_gross:


```python
df_1 ['foreign_gross']= df_1['worldwide_gross']- df_1['domestic_gross']

```

Lastly, I want to create a total profit column which is simply 'worldwide_gross' - 'production_budget' and also domestic, foreign, and worldwide_ROI respectively. These are easy ways to measure profitability. 


```python
# creating  total profit, worldwide ROI%, domestic ROI%, and foreign ROI% columns

df_1['total_profit']= df_1['worldwide_gross']- df_1['production_budget']
df_1['worldwide_ROI %']= (df_1['worldwide_gross'] / df_1['production_budget'])*100
df_1['domestic_ROI %']= (df_1['domestic_gross'] / df_1['production_budget'])*100
df_1['foreign_ROI %']= (df_1['foreign_gross'] / df_1['production_budget'])*100
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
      <th>foreign_gross</th>
      <th>total_profit</th>
      <th>worldwide_ROI %</th>
      <th>domestic_ROI %</th>
      <th>foreign_ROI %</th>
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
      <td>-200237650</td>
      <td>42.789243</td>
      <td>12.217814</td>
      <td>30.571429</td>
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
      <td>1072413963</td>
      <td>424.384139</td>
      <td>138.840250</td>
      <td>285.543888</td>
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
      <td>999721747</td>
      <td>415.369636</td>
      <td>195.640815</td>
      <td>219.728822</td>
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
      <td>1747311220</td>
      <td>671.016739</td>
      <td>306.098766</td>
      <td>364.917972</td>
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
      <td>1748134200</td>
      <td>682.711400</td>
      <td>226.271827</td>
      <td>456.439573</td>
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
      <th>5761</th>
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
      <td>-10000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
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
      <td>-4416</td>
      <td>50.933333</td>
      <td>50.933333</td>
      <td>0.000000</td>
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
      <td>-7000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
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
      <td>-1400</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>1666 rows × 12 columns</p>
</div>



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

    Table Name : [('movie_basics',), ('directors',), ('known_for',), ('movie_akas',), ('movie_ratings',), ('persons',), ('principals',), ('writers',)]
    

Here, I am choosing the 'genres','primary_title', movie_id, and 'runtime_minutes' columns. They will be analyzed. 'primary_title' could be useful when I merge with df_1.

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
      <th>movie_id</th>
      <th>runtime_minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Action,Crime,Drama</td>
      <td>Sunghursh</td>
      <td>tt0063540</td>
      <td>175.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Biography,Drama</td>
      <td>One Day Before the Rainy Season</td>
      <td>tt0066787</td>
      <td>114.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Drama</td>
      <td>The Other Side of the Wind</td>
      <td>tt0069049</td>
      <td>122.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Comedy,Drama</td>
      <td>Sabse Bada Sukh</td>
      <td>tt0069204</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Comedy,Drama,Fantasy</td>
      <td>The Wandering Soap Opera</td>
      <td>tt0100275</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>146139</th>
      <td>Drama</td>
      <td>Kuambil Lagi Hatiku</td>
      <td>tt9916538</td>
      <td>123.0</td>
    </tr>
    <tr>
      <th>146140</th>
      <td>Documentary</td>
      <td>Rodolpho Teóphilo - O Legado de um Pioneiro</td>
      <td>tt9916622</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146141</th>
      <td>Comedy</td>
      <td>Dankyavar Danka</td>
      <td>tt9916706</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146142</th>
      <td>None</td>
      <td>6 Gunn</td>
      <td>tt9916730</td>
      <td>116.0</td>
    </tr>
    <tr>
      <th>146143</th>
      <td>Documentary</td>
      <td>Chico Albuquerque - Revelações</td>
      <td>tt9916754</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>146144 rows × 4 columns</p>
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
      <th>movie_id</th>
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
      <td>tt6565702</td>
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
      <td>tt2395427</td>
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
      <td>tt4154756</td>
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
      <td>tt0974015</td>
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
      <td>tt2379713</td>
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
      <td>tt2357398</td>
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
      <td>tt3973612</td>
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
      <td>tt1880418</td>
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
      <td>tt7837402</td>
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
      <td>tt2107644</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
<p>2058 rows × 16 columns</p>
</div>



Let's use df.drop to eliminate duplicates by finding duplicate  any 'movie_id' and then deleting all but the 1st one.


```python
# dropping duplicates
by_genre_df.drop_duplicates(subset='movie_id', keep='first', inplace=True, ignore_index=False)
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
      <th>movie_id</th>
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
      <td>tt6565702</td>
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
      <td>tt2395427</td>
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
      <td>tt4154756</td>
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
      <td>tt0974015</td>
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
      <td>tt2379713</td>
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
      <td>tt2357398</td>
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
      <td>tt3973612</td>
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
      <td>tt1880418</td>
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
      <td>tt7837402</td>
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
      <td>tt2107644</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
<p>2054 rows × 16 columns</p>
</div>



600+ duplicates were dropped!

1 last piece of business: Re-ordering the table column.I want year, genre, and runtime to be more prominent. Also, I can just drop primary_title but simply not including it.


600+ duplicates were dropped!

1 last piece of business: Re-ordering the table column.I want year, genre, and runtime to be more prominent. Also, I can just drop primary_title but simply not including it.



```python
#re-ordering columns
columnsTitles = ['movie', 'movie_id', 'Year', 'genres', 'runtime_minutes','production_budget', 'domestic_gross', 'foreign_gross','worldwide_gross', 'total_profit', 'domestic_ROI %', 'foreign_ROI %', 'worldwide_ROI %']


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
      <th>movie_id</th>
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
      <td>tt6565702</td>
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
      <td>tt2395427</td>
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
      <td>tt4154756</td>
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
      <td>tt0974015</td>
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
      <td>tt2379713</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>2053</th>
      <td>The Front Man</td>
      <td>tt2357398</td>
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
      <td>tt3973612</td>
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
      <td>tt1880418</td>
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
      <td>tt7837402</td>
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
      <td>tt2107644</td>
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
<p>2054 rows × 13 columns</p>
</div>



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
    * Finally, we'll find the top 5 directors by runtime.


I will import the relevant libraries.


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
      <th>movie_id</th>
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
      <td>tt6565702</td>
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
      <td>tt2395427</td>
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
      <td>tt4154756</td>
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
      <td>tt0974015</td>
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
      <td>tt2379713</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>1950</th>
      <td>Rodeo Girl</td>
      <td>tt4062896</td>
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
      <td>tt1950401</td>
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
      <td>tt3210710</td>
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
    <tr>
      <th>2013</th>
      <td>Closure</td>
      <td>tt2405862</td>
      <td>2015</td>
      <td>Adventure,Biography,Documentary</td>
      <td>76.0</td>
      <td>100000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-100000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2048</th>
      <td>Ten</td>
      <td>tt2496400</td>
      <td>2015</td>
      <td>Adventure,Horror,Mystery</td>
      <td>83.0</td>
      <td>25000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-25000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>340 rows × 13 columns</p>
</div>



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




    Text(0, 0.5, 'ROI')




    
![png](phase_1_project_files/phase_1_project_40_1.png)
    


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
* Finally, we'll graph the DataFrame


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




    Text(0, 0.5, 'ROI')




    
![png](phase_1_project_files/phase_1_project_48_1.png)
    


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
      <th>movie_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0369610</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0369610</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0369610</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0369610</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0369610</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>331698</th>
      <td>tt9827784</td>
    </tr>
    <tr>
      <th>331699</th>
      <td>tt9827784</td>
    </tr>
    <tr>
      <th>331700</th>
      <td>tt9880178</td>
    </tr>
    <tr>
      <th>331701</th>
      <td>tt9880178</td>
    </tr>
    <tr>
      <th>331702</th>
      <td>tt9880178</td>
    </tr>
  </tbody>
</table>
<p>331703 rows × 1 columns</p>
</div>




```python
df_directors=pd.read_sql("""
SELECT *
FROM directors

;""", conn)
df_directors
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
      <th>movie_id</th>
      <th>person_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0285252</td>
      <td>nm0899854</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0462036</td>
      <td>nm1940585</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0835418</td>
      <td>nm0151540</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0835418</td>
      <td>nm0151540</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0878654</td>
      <td>nm0089502</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>291169</th>
      <td>tt8999974</td>
      <td>nm10122357</td>
    </tr>
    <tr>
      <th>291170</th>
      <td>tt9001390</td>
      <td>nm6711477</td>
    </tr>
    <tr>
      <th>291171</th>
      <td>tt9001494</td>
      <td>nm10123242</td>
    </tr>
    <tr>
      <th>291172</th>
      <td>tt9001494</td>
      <td>nm10123248</td>
    </tr>
    <tr>
      <th>291173</th>
      <td>tt9004986</td>
      <td>nm4993825</td>
    </tr>
  </tbody>
</table>
<p>291174 rows × 2 columns</p>
</div>



Now let's merge these 2 columns!


```python
directors_titles= df_akas.merge(df_directors, how='inner')
directors_titles
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
      <th>movie_id</th>
      <th>person_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>838575</th>
      <td>tt9827784</td>
      <td>nm0794048</td>
    </tr>
    <tr>
      <th>838576</th>
      <td>tt9827784</td>
      <td>nm0794048</td>
    </tr>
    <tr>
      <th>838577</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
    </tr>
    <tr>
      <th>838578</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
    </tr>
    <tr>
      <th>838579</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
    </tr>
  </tbody>
</table>
<p>838580 rows × 2 columns</p>
</div>



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
      <th>person_id</th>
      <th>primary_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>nm0061671</td>
      <td>Mary Ellen Bauder</td>
    </tr>
    <tr>
      <th>1</th>
      <td>nm0061865</td>
      <td>Joseph Bauer</td>
    </tr>
    <tr>
      <th>2</th>
      <td>nm0062070</td>
      <td>Bruce Baum</td>
    </tr>
    <tr>
      <th>3</th>
      <td>nm0062195</td>
      <td>Axel Baumann</td>
    </tr>
    <tr>
      <th>4</th>
      <td>nm0062798</td>
      <td>Pete Baxter</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>606643</th>
      <td>nm9990381</td>
      <td>Susan Grobes</td>
    </tr>
    <tr>
      <th>606644</th>
      <td>nm9990690</td>
      <td>Joo Yeon So</td>
    </tr>
    <tr>
      <th>606645</th>
      <td>nm9991320</td>
      <td>Madeline Smith</td>
    </tr>
    <tr>
      <th>606646</th>
      <td>nm9991786</td>
      <td>Michelle Modigliani</td>
    </tr>
    <tr>
      <th>606647</th>
      <td>nm9993380</td>
      <td>Pegasus Envoyé</td>
    </tr>
  </tbody>
</table>
<p>606648 rows × 2 columns</p>
</div>



Okay, let's merge these 2 tables!


```python
director_list_df=directors_titles.merge(df_persons, how= 'inner')
director_list_df
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
      <th>movie_id</th>
      <th>person_id</th>
      <th>primary_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>838563</th>
      <td>tt9755806</td>
      <td>nm1382072</td>
      <td>Tommy Wiseau</td>
    </tr>
    <tr>
      <th>838564</th>
      <td>tt9755806</td>
      <td>nm1382072</td>
      <td>Tommy Wiseau</td>
    </tr>
    <tr>
      <th>838565</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
      <td>Orencio Carvajal</td>
    </tr>
    <tr>
      <th>838566</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
      <td>Orencio Carvajal</td>
    </tr>
    <tr>
      <th>838567</th>
      <td>tt9880178</td>
      <td>nm7461083</td>
      <td>Orencio Carvajal</td>
    </tr>
  </tbody>
</table>
<p>838568 rows × 3 columns</p>
</div>



Ok, now I need to assign those directors to their respective films. To do that,I am going to merge 'director_list_df' with 'by_genre_df' on their shared 'movie_id' column. It's important to merge on 'movie_id' because it is unique to a film. Then, I will drop duplucates.


```python
Director_movie_df= director_list_df.merge(by_genre_df, how='inner', left_on='movie_id', right_on='movie_id')
Director_movie_df.drop_duplicates(subset='movie', keep='first', inplace=True, ignore_index=False)
Director_movie_df
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
      <th>movie_id</th>
      <th>person_id</th>
      <th>primary_name</th>
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
      <td>tt0369610</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
      <td>Jurassic World</td>
      <td>2015</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>124.0</td>
      <td>215000000</td>
      <td>652270625</td>
      <td>996584239</td>
      <td>1648854864</td>
      <td>1433854864</td>
      <td>303.381686</td>
      <td>463.527553</td>
      <td>766.909239</td>
    </tr>
    <tr>
      <th>240</th>
      <td>tt1862079</td>
      <td>nm1119880</td>
      <td>Colin Trevorrow</td>
      <td>Safety Not Guaranteed</td>
      <td>2012</td>
      <td>Comedy,Drama,Romance</td>
      <td>86.0</td>
      <td>750000</td>
      <td>4010957</td>
      <td>411361</td>
      <td>4422318</td>
      <td>3672318</td>
      <td>534.794267</td>
      <td>54.848133</td>
      <td>589.642400</td>
    </tr>
    <tr>
      <th>253</th>
      <td>tt0401729</td>
      <td>nm0004056</td>
      <td>Andrew Stanton</td>
      <td>John Carter</td>
      <td>2012</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>132.0</td>
      <td>275000000</td>
      <td>73058679</td>
      <td>209719421</td>
      <td>282778100</td>
      <td>7778100</td>
      <td>26.566792</td>
      <td>76.261608</td>
      <td>102.828400</td>
    </tr>
    <tr>
      <th>389</th>
      <td>tt2277860</td>
      <td>nm0004056</td>
      <td>Andrew Stanton</td>
      <td>Finding Dory</td>
      <td>2016</td>
      <td>Adventure,Animation,Comedy</td>
      <td>97.0</td>
      <td>200000000</td>
      <td>486295561</td>
      <td>534919632</td>
      <td>1021215193</td>
      <td>821215193</td>
      <td>243.147781</td>
      <td>267.459816</td>
      <td>510.607596</td>
    </tr>
    <tr>
      <th>749</th>
      <td>tt1077368</td>
      <td>nm0000318</td>
      <td>Tim Burton</td>
      <td>Dark Shadows</td>
      <td>2012</td>
      <td>Comedy,Fantasy,Horror</td>
      <td>113.0</td>
      <td>150000000</td>
      <td>79727149</td>
      <td>158475519</td>
      <td>238202668</td>
      <td>88202668</td>
      <td>53.151433</td>
      <td>105.650346</td>
      <td>158.801779</td>
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
      <th>110747</th>
      <td>tt5462602</td>
      <td>nm0795290</td>
      <td>Michael Showalter</td>
      <td>The Big Sick</td>
      <td>2017</td>
      <td>Comedy,Drama,Romance</td>
      <td>120.0</td>
      <td>5000000</td>
      <td>42872467</td>
      <td>11100393</td>
      <td>53972860</td>
      <td>48972860</td>
      <td>857.449340</td>
      <td>222.007860</td>
      <td>1079.457200</td>
    </tr>
    <tr>
      <th>110792</th>
      <td>tt4761916</td>
      <td>nm0839812</td>
      <td>Stephen Susco</td>
      <td>Unfriended: Dark Web</td>
      <td>2018</td>
      <td>Crime,Horror,Mystery</td>
      <td>92.0</td>
      <td>1000000</td>
      <td>8866745</td>
      <td>7567843</td>
      <td>16434588</td>
      <td>15434588</td>
      <td>886.674500</td>
      <td>756.784300</td>
      <td>1643.458800</td>
    </tr>
    <tr>
      <th>110832</th>
      <td>tt3702088</td>
      <td>nm0483999</td>
      <td>Beth LaMure</td>
      <td>Daisy Winters</td>
      <td>2017</td>
      <td>Drama</td>
      <td>90.0</td>
      <td>4500000</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>-4500000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>110837</th>
      <td>tt4550098</td>
      <td>nm1053530</td>
      <td>Tom Ford</td>
      <td>Nocturnal Animals</td>
      <td>2016</td>
      <td>Drama,Thriller</td>
      <td>116.0</td>
      <td>22500000</td>
      <td>10663357</td>
      <td>21735324</td>
      <td>32398681</td>
      <td>9898681</td>
      <td>47.392698</td>
      <td>96.601440</td>
      <td>143.994138</td>
    </tr>
    <tr>
      <th>110911</th>
      <td>tt5649108</td>
      <td>nm8083973</td>
      <td>Cory Finley</td>
      <td>Thoroughbreds</td>
      <td>2018</td>
      <td>Comedy,Crime,Drama</td>
      <td>92.0</td>
      <td>5000000</td>
      <td>3072605</td>
      <td>114697</td>
      <td>3187302</td>
      <td>-1812698</td>
      <td>61.452100</td>
      <td>2.293940</td>
      <td>63.746040</td>
    </tr>
  </tbody>
</table>
<p>1414 rows × 15 columns</p>
</div>



 Nice table. Next, I want to limit my DataFrame where the 'production_budget' is greater than $5 mil. Anything below $ 5mil. is considered a low budget film according to:
 
https://www.studiobinder.com/blog/production-budget/

 While there is nothing wrong with low budget films, it's best to filter out the lower budget films.


```python
budget= Director_movie_df.loc[Director_movie_df['production_budget'] > 2000000]

```

Next up:
* I will create a new a new dictionary called director total. The dictionary 
* Then, I will use .aggregate to add the 'worldwide_ROI %' for each director. 
* Then I will show the top 10 directors.


```python
director_total = { 'primary_name': 'first', 'worldwide_ROI %': 'sum'}
dir_cumulative = budget.groupby(budget['primary_name']).aggregate(director_total)
dir_cumulative.sort_values(by=['worldwide_ROI %']).tail(10)
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
      <th>primary_name</th>
      <th>worldwide_ROI %</th>
    </tr>
    <tr>
      <th>primary_name</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Michael Tiddes</th>
      <td>Michael Tiddes</td>
      <td>3369.335345</td>
    </tr>
    <tr>
      <th>Damien Chazelle</th>
      <td>Damien Chazelle</td>
      <td>3487.975432</td>
    </tr>
    <tr>
      <th>Scott Derrickson</th>
      <td>Scott Derrickson</td>
      <td>3627.391121</td>
    </tr>
    <tr>
      <th>Tim Story</th>
      <td>Tim Story</td>
      <td>3694.568541</td>
    </tr>
    <tr>
      <th>John R. Leonetti</th>
      <td>John R. Leonetti</td>
      <td>4147.381772</td>
    </tr>
    <tr>
      <th>Pierre Coffin</th>
      <td>Pierre Coffin</td>
      <td>4230.838901</td>
    </tr>
    <tr>
      <th>Jordan Peele</th>
      <td>Jordan Peele</td>
      <td>5107.359020</td>
    </tr>
    <tr>
      <th>Christopher Landon</th>
      <td>Christopher Landon</td>
      <td>5135.106813</td>
    </tr>
    <tr>
      <th>James DeMonaco</th>
      <td>James DeMonaco</td>
      <td>5466.643092</td>
    </tr>
    <tr>
      <th>M. Night Shyamalan</th>
      <td>M. Night Shyamalan</td>
      <td>7746.313721</td>
    </tr>
  </tbody>
</table>
</div>



Now, let's make the ROI%s into integers.


```python
dir_cumulative['worldwide_ROI %']=dir_cumulative['worldwide_ROI %'].apply(lambda x: int(x))

Actual_dir_total=dir_cumulative['worldwide_ROI %']
Actual_dir_total
```




    primary_name
    Adam Green          848
    Adam McKay          819
    Adam Robitel       1678
    Adam Shankman       436
    Adam Vermillion      23
                       ... 
    Zack Snyder         862
    Zackary Adler         0
    Zal Batmanglij       46
    Zeljko Mirkovic      11
    Zhi-yu Ma          2717
    Name: worldwide_ROI %, Length: 837, dtype: int64



Then, I will make the above list into a dictionary.


```python
Dir_Total_dict=Actual_dir_total.to_dict()
Dir_Total_dict
```




    {'Adam Green': 848,
     'Adam McKay': 819,
     'Adam Robitel': 1678,
     'Adam Shankman': 436,
     'Adam Vermillion': 23,
     'Adam Wingard': 389,
     'Akiva Schaffer': 122,
     'Alan Taylor': 429,
     'Alastair Siddons': 488,
     'Albert Hughes': 195,
     'Alejandro Amenábar': 79,
     'Alejandro G. Iñárritu': 968,
     'Alejandro Monteverde': 88,
     'Alejo Rébora': 54,
     'Aleksander Bach': 234,
     'Alex Garland': 373,
     'Alex Kendrick': 2465,
     'Alex Kurtzman': 289,
     'Alex Proyas': 99,
     'Alexander Payne': 286,
     'Alfonso Cuarón': 630,
     'Alfonso Gomez-Rejon': 115,
     'Ali Abbas Zafar': 663,
     'Allen Hughes': 98,
     'Amanda Boyle': 10,
     'Analeine Cal y Mayor': 0,
     'Andrea Arnold': 108,
     'Andrea Di Stefano': 23,
     'Andrew Adamson': 112,
     'Andrew Cohn': 355,
     'Andrew Dominik': 261,
     'Andrew Erwin': 1333,
     'Andrew Haigh': 30,
     'Andrew Hyatt': 510,
     'Andrew Mogel': 25,
     'Andrew Niccol': 128,
     'Andrew Stanton': 613,
     'Andy Fickman': 766,
     'Andy Muschietti': 1992,
     'Ang Lee': 517,
     'Angelina Jolie': 270,
     'Angelo Pizzo': 11,
     'Anna Boden': 641,
     'Anna Foerster': 232,
     'Anne Fletcher': 233,
     'Anne Wild': 353,
     'Anthony Hemingway': 86,
     "Anthony O'Brien": 0,
     'Anthony Russo': 1558,
     'Anthony Silverston': 139,
     'Antoine Fuqua': 1339,
     'Antoinette Beumer': 406,
     'Anton Corbijn': 240,
     'Antonio Simoncini': 0,
     'Ari Aster': 701,
     'Ari Sandel': 266,
     'Arie Posin': 28,
     'Ariel Schulman': 3209,
     'Ariel Vromen': 36,
     'Asger Leth': 118,
     'Ash Brannon': 40,
     'Ashish R. Mohan': 262,
     'Ava DuVernay': 463,
     'Ayan Mukherjee': 612,
     'Babak Najafi': 397,
     'Baltasar Kormákur': 1116,
     'Barrett Esposito': 0,
     'Barry Jenkins': 165,
     'Barry Levinson': 22,
     'Barry Sonnenfeld': 304,
     'Baz Luhrmann': 184,
     'Ben Affleck': 543,
     'Ben Falcone': 753,
     'Ben Hernandez Bray': 8,
     'Ben Stassen': 184,
     'Ben Stiller': 317,
     'Ben Wheatley': 37,
     'Ben Younger': 41,
     'Benedek Fliegauf': 0,
     'Benni Diez': 0,
     'Benny Boom': 121,
     'Benson Lee': 83,
     'Beth LaMure': 0,
     'Bethany Ashton Wolf': 467,
     'Bill Boyce': 271,
     'Bill Condon': 23,
     'Bill Holderman': 911,
     'Bill Muir': 21,
     'Bille Woodruff': 553,
     'Billy Ray': 163,
     'Bo Yurko': 59,
     'Boaz Yakin': 344,
     'Boots Riley': 571,
     'Boyapati Srinu': 169,
     'Brad Anderson': 545,
     'Brad Bird': 742,
     'Brad Furman': 245,
     'Brad Peyton': 1361,
     'Brandon Cronenberg': 3,
     'Breck Eisner': 163,
     'Brenda Chapman': 299,
     'Brent Ryan Green': 0,
     'Brett Ratner': 243,
     'Brian A. Miller': 118,
     'Brian Fee': 219,
     'Brian Helgeland': 314,
     'Brian Klugman': 272,
     'Brian Percival': 400,
     'Brian Robbins': 51,
     'Brian Taylor': 261,
     'Brian Trenchard-Smith': 0,
     'Brook Susan Parker': 8,
     'Bruce Macdonald': 0,
     'Bryan Buckley': 17,
     'Bryan Singer': 2407,
     'Burr Steers': 59,
     'Byron Howard': 679,
     'Cal Brunker': 143,
     'Cameron Crowe': 68,
     'Camille Delamarre': 579,
     'Carl Rinsch': 86,
     'Carlos Saldanha': 640,
     'Catherine Hardwicke': 102,
     'Chan-wook Park': 100,
     'Charles Ferguson': 0,
     'Charles Martin Smith': 160,
     'Charles Stone III': 258,
     'Charles-Olivier Michaud': 27,
     'Charlie Bean': 175,
     'Chris Butler': 180,
     'Chris Columbus': 271,
     'Chris Evans': 16,
     'Chris McKay': 388,
     'Chris Renaud': 1324,
     'Chris Rock': 216,
     'Chris Sanders': 424,
     'Chris Wedge': 312,
     'Christian Gudegast': 267,
     'Christian Rivers': 85,
     'Christian Sesma': 0,
     'Christian Sønderby Jepsen': 11,
     'Christophe Gans': 786,
     'Christophe Lourdelet': 845,
     'Christopher Landon': 5135,
     'Christopher M. Bessette': 0,
     'Christopher McQuarrie': 362,
     'Christopher Nolan': 1131,
     'Chuck Konzelman': 301,
     'Chuck Russell': 1,
     'Ciarán Foy': 541,
     'Claudia Llosa': 2,
     'Clay Kaytis': 482,
     'Clint Eastwood': 1691,
     'Cody Cameron': 351,
     'Colin Trevorrow': 766,
     'Conor Allyn': 0,
     'Cory Finley': 63,
     'Courtney Solomon': 65,
     'Craig Gillespie': 716,
     'Cyrus Nowrasteh': 43,
     'D.J. Caruso': 429,
     'Damien Chazelle': 3487,
     'Dan Bradley': 74,
     'Dan Fogelman': 84,
     'Dan Garcia': 0,
     'Dan Gilroy': 651,
     'Dan Mazer': 913,
     'Dan Perri': 0,
     'Dan Scanlon': 371,
     'Dan Trachtenberg': 2165,
     'Daniel Barnz': 30,
     'Daniel Espinosa': 435,
     'Daniel Petrie Jr.': 0,
     'Daniel Scheinert': 194,
     'Daniel Stamm': 1,
     'Danny Boyle': 259,
     'Danny Perez': 0,
     'Darin Scott': 0,
     'Darrell Roodt': 0,
     'Dave Green': 506,
     'David Ayer': 1125,
     'David Bowers': 512,
     'David E. Talbert': 519,
     'David F. Sandberg': 2462,
     'David Fincher': 604,
     'David Frankel': 680,
     'David G. Evans': 132,
     'David Gelb': 767,
     'David Gordon Green': 2607,
     'David Hackl': 0,
     'David Hayter': 0,
     'David Koepp': 140,
     'David L. Hunt': 22,
     'David Lam': 29,
     'David Leitch': 1297,
     'David Lowery': 21,
     'David M. Rosenthal': 334,
     'David Mackenzie': 313,
     'David Michôd': 37,
     'David O. Russell': 1939,
     'David Scheinmann': 25,
     'David Soren': 545,
     'David Twohy': 249,
     'David Wain': 77,
     'David Yates': 965,
     'Dean DeBlois': 826,
     'Dean Devlin': 220,
     'Dean Israelite': 393,
     'Dean Parisot': 168,
     'Dean Wright': 83,
     'Denis Villeneuve': 1100,
     'Dennis Dugan': 308,
     'Dennis Gansel': 274,
     'Denzel Washington': 267,
     'Deon Taylor': 450,
     'Derek Cianfrance': 424,
     'Derek Hallquist': 92,
     'Desiree Lim': 77,
     'Dexter Fletcher': 460,
     'Diederik Van Rooijen': 713,
     'Dilip Panda': 123,
     'Dmitriy Dyachenko': 361,
     'Dominic Burns': 0,
     'Don Hall': 820,
     'Don Scardino': 85,
     'Doug Ellin': 154,
     'Doug Liman': 629,
     'Douglas Aarniokoski': 0,
     'Drew Goddard': 326,
     'Drew Pearce': 85,
     'Duane Journey': 0,
     'Duke Johnson': 71,
     'Duncan Jones': 265,
     'Dustin Hoffman': 510,
     'Dylan Brown': 115,
     'Ed Gass-Donnelly': 636,
     'Edgar Wright': 908,
     'Edward Zwick': 266,
     'Eli Roth': 161,
     'Elizabeth Banks': 991,
     'Enrique Begné': 248,
     'Eric Dean Hordes': 162,
     'Eric Lavaine': 2,
     'Eric Wareheim': 7,
     'Eric Williams': 135,
     'Ericson Core': 126,
     'Erika Cohn': 152,
     'Ernie Barbarash': 0,
     'Espen Sandberg': 342,
     'Etan Cohen': 366,
     'Ethan Coen': 591,
     'Eugene Jarecki': 0,
     'F. Gary Gray': 1218,
     'F. Javier Gutiérrez': 331,
     'Farhad Safinia': 20,
     'Farren Blackburn': 113,
     'Fede Alvarez': 575,
     'Fien Troch': 296,
     'Francis Ford Coppola': 18,
     'Francis Lawrence': 1895,
     'Frank Coraci': 458,
     'Frédéric Auburtin': 0,
     'Gareth Edwards': 855,
     'Gary Fleder': 234,
     'Gary Ross': 893,
     'Gary Shore': 314,
     'Gavin Hood': 388,
     "Gavin O'Connor": 397,
     'Genndy Tartakovsky': 1843,
     'George Clooney': 226,
     'George Mendeluk': 2,
     'George Miller': 246,
     'George Nolfi': 23,
     'George Tillman Jr.': 340,
     'Gerard McMurray': 1050,
     'Gil Kenan': 273,
     'Gina Prince-Bythewood': 208,
     'Glenn Ciano': 0,
     'Glenn Ficarra': 330,
     'Gore Verbinski': 94,
     'Graham Annable': 186,
     'Greg Berlanti': 655,
     'Greg McLean': 508,
     'Greg Tiernan': 743,
     'Gregori J. Martin': 2976,
     'Gregory Jacobs': 852,
     'Gregory Plotkin': 1109,
     'Greta Gerwig': 786,
     'Guillermo del Toro': 1356,
     'Gurinder Chadha': 0,
     'Gus Van Sant': 82,
     'Guy Ritchie': 420,
     'Hadrian Hooks': 379,
     'Hank Braxtan': 0,
     'Hans Petter Moland': 104,
     'Harald Zwart': 126,
     'Harmony Korine': 622,
     'Henry Hobson': 22,
     'Hitoshi Matsumoto': 0,
     'Isaac Florentine': 0,
     'Ivan Reitman': 119,
     'J Blakeson': 292,
     'J.A. Bayona': 1300,
     'J.C. Chandor': 44,
     'J.J. Abrams': 245,
     'Jackie Earle Haley': 5,
     'Jaco Booyens': 0,
     'Jacques Audiard': 120,
     'Jake Kasdan': 1386,
     'Jake Schreier': 909,
     'Jake Szymanski': 229,
     'James Bobin': 307,
     'James D.R. Hickox': 0,
     'James DeMonaco': 5466,
     'James Foley': 1368,
     'James Franco': 288,
     'James Gray': 104,
     'James Gunn': 453,
     'James Kent': 66,
     'James Manera': 17,
     'James Mangold': 362,
     'James Marsh': 822,
     'James Mather': 169,
     'James McTeigue': 974,
     'James Ponsoldt': 276,
     'James Wan': 3106,
     'James Watkins': 859,
     'Jane Lipsitz': 272,
     'Jason Bateman': 78,
     'Jason Moore': 682,
     'Jason Reitman': 78,
     'Jaume Collet-Serra': 1747,
     'Jay Roach': 110,
     'Jean-Marc Vallée': 1561,
     'Jean-Pierre Jeunet': 1,
     'Jee-woon Kim': 161,
     'Jeff Newitt': 247,
     'Jeff Nichols': 42,
     'Jeff Stenzel': 0,
     'Jeff Wadlow': 225,
     'Jeffrey Blitz': 92,
     'Jeffrey Nachmanoff': 0,
     'Jennifer Yuh Nelson': 482,
     'Jeremy Garelick': 348,
     'Jeremy Saulnier': 76,
     'Jeremy Sims': 38,
     'Jessie Nelson': 234,
     'Jeta Amata': 0,
     'Jim Jarmusch': 214,
     'Jim Wilson': 10,
     'Jimmy Hayward': 200,
     'Jin-ho Hur': 43,
     'Jocelyn Moorhouse': 202,
     'Jodie Foster': 346,
     'Joe Cacaci': 137,
     'Joe Carnahan': 324,
     'Joe Cornish': 48,
     'Joe Johnston': 128,
     'Joe Wright': 748,
     'Johannes Roberts': 857,
     'John Carney': 860,
     'John Crowley': 564,
     'John Erick Dowdle': 1693,
     'John Francis Daley': 647,
     'John Hamburg': 309,
     'John Herzfeld': 0,
     'John Hillcoat': 338,
     'John Krasinski': 1967,
     'John Lee Hancock': 328,
     'John Luessenhop': 238,
     'John Madden': 2293,
     'John Moore': 330,
     'John R. Leonetti': 4147,
     'John Stalberg Jr.': 2,
     'John Stephenson': 27,
     'John Stevenson': 148,
     'John Wells': 386,
     'John Woo': 81,
     'Jon Cassar': 307,
     'Jon Favreau': 550,
     'Jon Gunn': 621,
     'Jon Hurwitz': 473,
     'Jon M. Chu': 1474,
     'Jon Stewart': 31,
     'Jon Turteltaub': 700,
     'Jon Watts': 505,
     'Jon Wright': 8,
     'Jonathan Dayton': 73,
     'Jonathan Demme': 228,
     'Jonathan Glazer': 54,
     'Jonathan Jakubowicz': 25,
     'Jonathan Levine': 702,
     'Jonathan Liebesman': 591,
     'Jonathan Newman': 0,
     'Jonathan Silverman': 1,
     'Jonathan Teplitzky': 208,
     'Joon-ho Bong': 220,
     'Jordan Peele': 5107,
     'Jordan Roberts': 0,
     'Jordan Vogt-Roberts': 303,
     'Jorma Taccone': 47,
     'Joseph Gordon-Levitt': 750,
     'Joseph Kosinski': 304,
     'Josh Boone': 2559,
     'Josh Gordon': 202,
     'Josh Schwartz': 79,
     'Josh Trank': 1170,
     'Joshua Michael Stern': 241,
     'Joshua Moore': 146,
     'Joss Whedon': 1099,
     'José Celestino Campusano': 315,
     'José Padilha': 202,
     'Juan José Campanella': 55,
     'Juan Solanas': 52,
     'Judd Apatow': 403,
     'Julian Gilbey': 11,
     'Julie Anne Robinson': 86,
     'June Daguiso': 18,
     'Justin Baldoni': 1150,
     'Justin Chadwick': 112,
     'Justin Lin': 181,
     'Justin Tipping': 1,
     'Justin Zackham': 149,
     'Jérôme Salle': 11,
     'K.W. Jones': 37,
     'Kabir Khan': 943,
     'Kabir Sadanand': 0,
     'Karen Oganesyan': 22,
     'Karey Kirkpatrick': 267,
     'Karyn Kusama': 40,
     'Kasi Lemmons': 41,
     'Kate Barker-Froyland': 7,
     'Kathryn Bigelow': 256,
     'Kay Cannon': 447,
     'Keith Parmer': 0,
     'Kelly Asbury': 384,
     'Kelly Fremon Craig': 212,
     'Kelly Weaver': 477,
     'Ken Kwapis': 527,
     'Ken Marino': 625,
     'Ken Scott': 358,
     'Kenneth Branagh': 1410,
     'Kenneth Lonergan': 914,
     'Kevin Carraway': 0,
     'Kevin Connolly': 60,
     'Kevin Kölsch': 521,
     'Kevin Smith': 63,
     'Khavn': 322,
     'Khurram H. Alavi': 2,
     'Kim Farrant': 1,
     'Kim Nguyen': 0,
     'Kimberly Peirce': 274,
     'Kirk Jones': 747,
     'Kuku Surendran': 474,
     'Kyle Lawrence': 484,
     'Larry Brand': 387,
     'Larry Charles': 277,
     'Lasse Hallström': 428,
     'Lawrence Kasanoff': 0,
     'Lawrence Kasdan': 10,
     'Lawrence Sher': 84,
     'Lee Daniels': 622,
     'Lee Toland Krieger': 229,
     'Lee Unkrich': 456,
     'Leigh Whannell': 551,
     'Len Wiseman': 169,
     'Lenny Abrahamson': 278,
     'Leos Carax': 105,
     'Leslye Headland': 419,
     'Levan Akin': 225,
     'Lilly Wachowski': 229,
     'Lisa Azuelos': 94,
     'Liv Ullmann': 15,
     'Lorene Scafaria': 290,
     'Louis Leterrier': 539,
     'Luc Besson': 1433,
     'Luca Guadagnino': 35,
     'Lucia Aniello': 234,
     'Luis Prieto': 165,
     'Lynne Ramsay': 153,
     'M. Night Shyamalan': 7746,
     'Mabrouk El Mechri': 126,
     'Maja Friis': 322,
     'Malcolm D. Lee': 1203,
     'Marc Bennett': 0,
     'Marc Forster': 543,
     'Marc Silverstein': 286,
     'Marc Webb': 1227,
     'Marcel Walz': 5,
     'Marcio Garcia': 1,
     'Marcus Dreeke': 0,
     'Marcus Dunstan': 88,
     'Marielle Heller': 117,
     'Mario Van Peebles': 4,
     'Mark Duplass': 47,
     'Mark Neveldine': 114,
     'Mark Osborne': 131,
     'Mark Tonderai': 639,
     'Martin McDonagh': 1579,
     'Martin Rutley': 0,
     'Martin Scorsese': 389,
     'Mathew Cullen': 5,
     'Matt Bettinelli-Olpin': 516,
     'Matt Brown': 123,
     'Matt Kendrick': 28,
     'Matt Piedmont': 140,
     'Matt Reeves': 740,
     'Matthew Vaughn': 823,
     'Maïwenn': 255,
     'McG': 380,
     'Mel Gibson': 422,
     'Menetie T. Ejeye': 195,
     'Michael Anderson': 0,
     'Michael Apted': 41,
     'Michael Bay': 1116,
     'Michael Berry': 2,
     'Michael Budd': 219,
     'Michael Carney': 98,
     'Michael Cuesta': 241,
     'Michael D. Akers': 91,
     'Michael Dougherty': 587,
     'Michael Gracey': 460,
     'Michael Grandage': 42,
     'Michael Haneke': 379,
     'Michael Hoffman': 157,
     'Michael Mann': 28,
     'Michael Moore': 133,
     'Michael Polish': 96,
     'Michael Radford': 1,
     'Michael Showalter': 1079,
     'Michael Sucsy': 658,
     'Michael Tiddes': 3369,
     'Michael Winnick': 0,
     'Michael Winterbottom': 37,
     'Michael Worth': 2,
     'Michaël R. Roskam': 151,
     'Miguel Arteta': 362,
     'Mikael Håfström': 179,
     'Mike Binder': 244,
     'Mike Flanagan': 1791,
     'Mike Leigh': 179,
     'Mike Mitchell': 695,
     'Mike Thurmeier': 1309,
     'Mimi Leder': 190,
     'Mira Nair': 70,
     'Mora Stephens': 0,
     'Morten Tyldum': 1793,
     'Nancy Meyers': 492,
     'Nat Faxon': 537,
     'Nate Parker': 168,
     'Neil Burger': 642,
     'Neil Jones': 231,
     'Neil Marshall': 81,
     'Neill Blomkamp': 452,
     'Nicholas Hytner': 705,
     'Nicholas Jarecki': 298,
     'Nicholas McCarthy': 329,
     'Nicholas Stoller': 2283,
     'Nick Cassavetes': 487,
     'Nick Love': 177,
     'Nick Park': 90,
     'Nicolai Fuglsig': 203,
     'Nicolas Winding Refn': 141,
     'Nicole Holofcener': 320,
     'Nicole Kassell': 8,
     'Niels Arden Oplev': 291,
     'Niki Caro': 268,
     'Nikolaj Arcel': 189,
     'Nima Nourizadeh': 966,
     'Nimród Antal': 50,
     'Nisha Ganatra': 6,
     'Nitesh Tiwari': 3101,
     'Noah Baumbach': 550,
     'Noam Murro': 300,
     'Ole Bornedal': 592,
     'Oleg Stepchenko': 131,
     'Oliver Stone': 254,
     'Olivier Dahan': 83,
     'Olivier Megaton': 1522,
     'Oren Peli': 0,
     'Paolo Sorrentino': 184,
     'Patricia Riggen': 681,
     'Patrick Hughes': 209,
     'Patty Jenkins': 547,
     'Paul China': 0,
     'Paul Feig': 1181,
     'Paul Greengrass': 747,
     'Paul King': 470,
     'Paul McGuigan': 77,
     'Paul Schrader': 112,
     'Paul Thomas Anderson': 208,
     'Paul W.S. Anderson': 1291,
     'Paul Weitz': 144,
     'Pawel Pawlikowski': 588,
     'Pete Travis': 92,
     'Peter Atencio': 137,
     'Peter Berg': 917,
     'Peter Chelsom': 54,
     'Peter Cousens': 6,
     'Peter Farrelly': 2283,
     'Peter Hedges': 138,
     'Peter Jackson': 1169,
     'Peter Landesman': 16,
     'Peter Lepeniotis': 408,
     'Peter Ramsey': 211,
     'Peter Segal': 577,
     'Peter Sohn': 178,
     'Peter Sollett': 24,
     'Peter Spierig': 2292,
     'Peter Swanson': 241,
     'Peyton Reed': 878,
     'Phil Lord': 1908,
     'Phillip Noyce': 266,
     'Phyllida Lloyd': 825,
     'Pierre Coffin': 4230,
     'Pierre Morel': 253,
     'Quentin Tarantino': 688,
     'R.J. Cutler': 712,
     'RZA': 146,
     'Rachel Lambert': 33,
     'Radu Mihaileanu': 2,
     'Rafa Lara': 1,
     'Raja Gosnell': 316,
     'Ralph Fiennes': 21,
     'Rawson Marshall Thurber': 1401,
     'Reed Morano': 0,
     'Reginald Hudlin': 84,
     'Renny Harlin': 84,
     'Rian Johnson': 568,
     'Ric Roman Waugh': 386,
     'Rich Moore': 300,
     'Richard Curtis': 744,
     'Richard LaGravenese': 120,
     'Richard Lanni': 14,
     'Richard Linklater': 2375,
     'Richard Raymond': 14,
     'Richard Shepard': 26,
     'Richie Keen': 161,
     'Rick Famuyiwa': 259,
     'Ridley Scott': 1735,
     'Rita Merson': 0,
     'Roar Uthaug': 303,
     'Rob Cohen': 1513,
     'Rob Greenberg': 711,
     'Rob Hawk': 0,
     'Rob Letterman': 273,
     'Rob Marshall': 262,
     'Rob Minkoff': 186,
     'Rob Reiner': 112,
     'Rob Tari': 1271,
     'Rob Thomas': 58,
     'Robbie Pickering': 0,
     'Robert Eggers': 1155,
     'Robert Fontaine': 0,
     'Robert Lorenz': 79,
     'Robert Luketic': 40,
     'Robert Rodriguez': 390,
     'Robert Schwentke': 60,
     'Robert Stromberg': 421,
     'Robert Zemeckis': 659,
     'Robin Bissell': 102,
     'Rodrigo Cortés': 82,
     'Roger Donaldson': 232,
     'Roger Spottiswoode': 217,
     'Rohit Jugraj': 303,
     'Roland Emmerich': 369,
     'Roland Joffé': 0,
     'Ron Fricke': 149,
     'Ron Howard': 491,
     'Ron Shelton': 30,
     'Ross Venokur': 15,
     'Rowan Joffe': 88,
     'Ruairi Robinson': 2,
     'Ruba Nadda': 0,
     'Ruben Fleischer': 909,
     'Rupert Sanders': 388,
     'Rupert Wyatt': 35,
     'Russell Crowe': 138,
     'Ry Russo-Young': 378,
     'Ryan Coogler': 1143,
     'S.S. Rajamouli': 945,
     'Sacha Bennett': 0,
     'Salim Akil': 246,
     'Sam Mendes': 848,
     'Sam Miller': 411,
     'Sam Raimi': 245,
     'Sam Taylor-Johnson': 1427,
     'Sanne Vogel': 987,
     'Sarah Gavron': 243,
     'Sarik Andreasyan': 23,
     'Scott Cooper': 256,
     'Scott Derrickson': 3627,
     'Scott Foley': 0,
     'Scott Frank': 221,
     'Scott Hicks': 386,
     'Scott Moore': 1361,
     'Scott Sanders': 0,
     'Scott Speer': 501,
     'Scott Stewart': 791,
     'Scott Walker': 29,
     'Scott Waugh': 981,
     'Sean Anders': 615,
     'Sergei Bodrov': 114,
     'Seth Gordon': 794,
     'Seth MacFarlane': 1431,
     'Shan Serafin': 407,
     'Shana Feste': 173,
     'Shane Black': 908,
     'Shannon Keith': 0,
     'Shawn Ku': 52,
     'Shawn Levy': 440,
     'Shekar': 798,
     'Shintaro Shimosawa': 21,
     'Simon Curtis': 532,
     'Simon J. Smith': 277,
     'Simon Kinberg': 42,
     'Simon Verhoeven': 114,
     'Simon West': 377,
     'Sion Sono': 275,
     'Slawomir Fabicki': 143,
     'Spike Jonze': 209,
     'Spike Lee': 620,
     'Stacie Passon': 143,
     'Stacy Title': 421,
     'Stefan Ruzowitzky': 27,
     'Stefano Sollima': 216,
     'Stella Meghie': 616,
     'Stephan Elliott': 114,
     'Stephen Chbosky': 1777,
     'Stephen Frears': 1017,
     'Steve Carr': 274,
     'Steve Martino': 252,
     'Steve McQueen': 1089,
     'Steve Pink': 479,
     'Steven Caple Jr.': 427,
     'Steven Knight': 45,
     'Steven Quale': 300,
     'Steven S. DeKnight': 187,
     'Steven Soderbergh': 2969,
     'Steven Spielberg': 1715,
     'Stewart Hendler': 45,
     'Stiles White': 2066,
     'Stuart Beattie': 114,
     'Sudz Sutherland': 248,
     'Susanne Bier': 0,
     'Sylvain White': 517,
     'Szabolcs Hajdu': 154,
     'Taedong Park': 3,
     'Taika Waititi': 1424,
     'Tarik Freitekh': 402,
     'Tarsem Singh': 204,
     'Tate Taylor': 111,
     'Tatsuya Nagamine': 1444,
     'Taylor Hackford': 149,
     'Thea Sharrock': 1041,
     'Theodore Melfi': 1348,
     'Thomas Carter': 200,
     'Thomas Imbach': 186,
     'Thor Freudenthal': 223,
     'Tim Burton': 843,
     'Tim Kirkby': 26,
     'Tim Miller': 1381,
     'Tim Story': 3694,
     'Timothy Fielding': 175,
     'Timothy Hines': 0,
     'Timothy Reckart': 313,
     'Timur Bekmambetov': 300,
     'Tina Gordon': 84,
     'Todd Graff': 113,
     'Todd Haynes': 363,
     'Todd Phillips': 172,
     'Todd Strauss-Schulson': 0,
     'Tom Donahue': 49,
     'Tom Ford': 143,
     'Tom Gormican': 663,
     'Tom McCarthy': 460,
     'Tom McGrath': 923,
     'Tom Tykwer': 33,
     'Tom Walsh': 0,
     'Tomas Alfredson': 123,
     'Tommy Lee Jones': 51,
     'Tommy Wirkola': 429,
     'Tony Gilroy': 224,
     'Tony Leondis': 433,
     'Tony Mahony': 341,
     'Travis Knight': 597,
     'Trevor Wall': 169,
     'Trey Edward Shults': 394,
     'Trish Sie': 412,
     'Troy Murray': 1246,
     'Tyler Perry': 233,
     'V.K. Prakash': 51,
     'Valeri Milev': 0,
     'Vanja Kovacevic': 1204,
     'Vic Armstrong': 129,
     'Vicky Jewson': 0,
     'Victor Teran': 0,
     'Victor Vu': 292,
     'Vidhu Vinod Chopra': 0,
     'Vijay Krishna Acharya': 48,
     "Vince D'Amato": 1226,
     'Viorel Sergovici': 1179,
     'Wally Pfister': 103,
     'Walt Becker': 277,
     'Walter Hill': 41,
     'Warren P. Sonoda': 5,
     'Wash Westmoreland': 833,
     'Wayne Blair': 226,
     'Wayne Thornley': 172,
     'Werner Herzog': 4,
     'Wes Anderson': 959,
     'Wes Ball': 1533,
     'Whit Stillman': 44,
     'Will Canon': 155,
     'Will Gluck': 909,
     'William Brent Bell': 682,
     'William Eubank': 66,
     'William Friedkin': 46,
     'William H. Macy': 11,
     'William Winckler': 1,
     'Wojciech Staron': 137,
     'Woody Allen': 917,
     'Xavier Dolan': 357,
     'Xavier Gens': 5,
     'Xavier Giannoli': 62,
     'Xiao Feng': 0,
     'Yann Demange': 86,
     'Yash Chopra': 63,
     'Yimou Zhang': 222,
     'Yorgos Lanthimos': 1027,
     'Youssef Delara': 14,
     'Zach Braff': 437,
     'Zack Snyder': 862,
     'Zackary Adler': 0,
     'Zal Batmanglij': 46,
     'Zeljko Mirkovic': 11,
     'Zhi-yu Ma': 2717}



Now:
* Let's sort the list in reverse order using sorted, itemgetter, and reverse=True.
* Then let's make that sorted dictionary into a DataFrame
* Next, I will rename the columns 'Director' and 'ROI' because they naturally come out as '0' and '1'.
* Lastly, let's take a look at the DataFrame.


```python
Dir_ROI= sorted(Dir_Total_dict.items(), key=itemgetter(1), reverse=True)[:5]
high_return_directors= pd.DataFrame(Dir_ROI)
high_return_directors.rename( columns={0 :'Director', 1:'ROI'}, inplace=True )
high_return_directors
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
      <th>Director</th>
      <th>ROI</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M. Night Shyamalan</td>
      <td>7746</td>
    </tr>
    <tr>
      <th>1</th>
      <td>James DeMonaco</td>
      <td>5466</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Christopher Landon</td>
      <td>5135</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Jordan Peele</td>
      <td>5107</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Pierre Coffin</td>
      <td>4230</td>
    </tr>
  </tbody>
</table>
</div>



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




    Text(0, 0.5, 'ROI')




    
![png](phase_1_project_files/phase_1_project_73_1.png)
    


Recommendation # 3: **If at all possible, hire M. Night Shyamalan, James DeMonaco, Christopher Landon, Jordan Peele, or Pierre Coffin because they have the highest ROI%**.


```python
conn.close()

```


```python

```
