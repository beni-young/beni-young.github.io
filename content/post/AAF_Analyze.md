---
date: 2024-10-24T00:47:08-04:00
description: "Analyzing the Black Directors throughout the years - Part III"
tags:
title: "Exploratory Data Analysis on Black Directors - The Analysis"
#weight: 1
cover:
  image: "images/th-1813448526.jpeg"
draft: false
---

```python
import pandas as pd
import re
import imdb
import time

ia = imdb.Cinemagoer()
df_director_films = pd.read_csv('Director_Movies_Clean.csv', encoding= 'utf-8-sig')
df_director_names = pd.read_csv('director_names.csv', encoding='unicode_escape')
kinds = {}
```


```python
df_director_films
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
      <th>Movie_ID</th>
      <th>Title</th>
      <th>Year</th>
      <th>Person_ID</th>
      <th>Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3501074</td>
      <td>Madam Secretary (2015-2017)</td>
      <td>0</td>
      <td>151</td>
      <td>Morgan Freeman</td>
    </tr>
    <tr>
      <th>1</th>
      <td>106464</td>
      <td>Bopha!</td>
      <td>1993</td>
      <td>151</td>
      <td>Morgan Freeman</td>
    </tr>
    <tr>
      <th>2</th>
      <td>995854</td>
      <td>A Journal for Jordan</td>
      <td>2021</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2671706</td>
      <td>Fences</td>
      <td>2016</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>4</th>
      <td>413573</td>
      <td>Grey's Anatomy</td>
      <td>2016</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6606</th>
      <td>8571700</td>
      <td>Remembrance: A Portrait Study</td>
      <td>1967</td>
      <td>9913311</td>
      <td>Edward Owens</td>
    </tr>
    <tr>
      <th>6607</th>
      <td>12127402</td>
      <td>Autrefois J'ai aime une femme</td>
      <td>1966</td>
      <td>9913311</td>
      <td>Edward Owens</td>
    </tr>
    <tr>
      <th>6608</th>
      <td>12666216</td>
      <td>Sisters of the Screen - African Women in Cinema</td>
      <td>1966</td>
      <td>11720380</td>
      <td>Beti Ellerson</td>
    </tr>
    <tr>
      <th>6609</th>
      <td>12666216</td>
      <td>Sisters of the Screen - African Women in Cinema</td>
      <td>2002</td>
      <td>11720380</td>
      <td>Beti Ellerson</td>
    </tr>
    <tr>
      <th>6610</th>
      <td>10038432</td>
      <td>A Luta Continua</td>
      <td>1972</td>
      <td>10555174</td>
      <td>Robert Van Lierop</td>
    </tr>
  </tbody>
</table>
<p>6611 rows × 5 columns</p>
</div>



Removed all Duplicates from Dataframe  


```python
df_movies = df_director_films.drop_duplicates(subset=None, keep='first', inplace=False, ignore_index=False)

```


```python
df_movies
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
      <th>Movie_ID</th>
      <th>Title</th>
      <th>Year</th>
      <th>Person_ID</th>
      <th>Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3501074</td>
      <td>Madam Secretary (2015-2017)</td>
      <td>0</td>
      <td>151</td>
      <td>Morgan Freeman</td>
    </tr>
    <tr>
      <th>1</th>
      <td>106464</td>
      <td>Bopha!</td>
      <td>1993</td>
      <td>151</td>
      <td>Morgan Freeman</td>
    </tr>
    <tr>
      <th>2</th>
      <td>995854</td>
      <td>A Journal for Jordan</td>
      <td>2021</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2671706</td>
      <td>Fences</td>
      <td>2016</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>4</th>
      <td>413573</td>
      <td>Grey's Anatomy</td>
      <td>2016</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6606</th>
      <td>8571700</td>
      <td>Remembrance: A Portrait Study</td>
      <td>1967</td>
      <td>9913311</td>
      <td>Edward Owens</td>
    </tr>
    <tr>
      <th>6607</th>
      <td>12127402</td>
      <td>Autrefois J'ai aime une femme</td>
      <td>1966</td>
      <td>9913311</td>
      <td>Edward Owens</td>
    </tr>
    <tr>
      <th>6608</th>
      <td>12666216</td>
      <td>Sisters of the Screen - African Women in Cinema</td>
      <td>1966</td>
      <td>11720380</td>
      <td>Beti Ellerson</td>
    </tr>
    <tr>
      <th>6609</th>
      <td>12666216</td>
      <td>Sisters of the Screen - African Women in Cinema</td>
      <td>2002</td>
      <td>11720380</td>
      <td>Beti Ellerson</td>
    </tr>
    <tr>
      <th>6610</th>
      <td>10038432</td>
      <td>A Luta Continua</td>
      <td>1972</td>
      <td>10555174</td>
      <td>Robert Van Lierop</td>
    </tr>
  </tbody>
</table>
<p>6316 rows × 5 columns</p>
</div>



Remove all the movies with no year (possibly television shows)


```python
df_movies = df_movies[df_movies.Year != 0]
```


```python
df_movies.describe()
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
      <th>Movie_ID</th>
      <th>Year</th>
      <th>Person_ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>5.699000e+03</td>
      <td>5699.000000</td>
      <td>5.699000e+03</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>4.781896e+06</td>
      <td>1998.656431</td>
      <td>9.356992e+05</td>
    </tr>
    <tr>
      <th>std</th>
      <td>4.761429e+06</td>
      <td>19.859445</td>
      <td>1.235733e+06</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.187000e+04</td>
      <td>1919.000000</td>
      <td>1.510000e+02</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>3.851770e+05</td>
      <td>1991.000000</td>
      <td>2.288530e+05</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>3.223890e+06</td>
      <td>2004.000000</td>
      <td>6.085750e+05</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>8.135978e+06</td>
      <td>2014.000000</td>
      <td>9.373060e+05</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.107769e+07</td>
      <td>2027.000000</td>
      <td>1.172038e+07</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_movies.loc[df_movies['Year'] == 2021]
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
      <th>Movie_ID</th>
      <th>Title</th>
      <th>Year</th>
      <th>Person_ID</th>
      <th>Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>995854</td>
      <td>A Journal for Jordan</td>
      <td>2021</td>
      <td>243</td>
      <td>Denzel Washington</td>
    </tr>
    <tr>
      <th>19</th>
      <td>14324128</td>
      <td>NYC Epicenters 9/11-2021ÃÂÃÂ½</td>
      <td>2021</td>
      <td>490</td>
      <td>Spike Lee</td>
    </tr>
    <tr>
      <th>185</th>
      <td>13935770</td>
      <td>Chris Rock Total Blackout: The Tamborine Exten...</td>
      <td>2021</td>
      <td>1674</td>
      <td>Chris Rock</td>
    </tr>
    <tr>
      <th>267</th>
      <td>10608454</td>
      <td>Harlem</td>
      <td>2021</td>
      <td>2700</td>
      <td>Malcolm D. Lee</td>
    </tr>
    <tr>
      <th>268</th>
      <td>3554046</td>
      <td>Space Jam: A New Legacy</td>
      <td>2021</td>
      <td>2700</td>
      <td>Malcolm D. Lee</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6487</th>
      <td>12223626</td>
      <td>Black Seeds: The History of Africans in America</td>
      <td>2021</td>
      <td>5679045</td>
      <td>Bayer Mack</td>
    </tr>
    <tr>
      <th>6529</th>
      <td>8537542</td>
      <td>Cold Blood</td>
      <td>2021</td>
      <td>6462306</td>
      <td>Taylor Ri'chard</td>
    </tr>
    <tr>
      <th>6541</th>
      <td>13814666</td>
      <td>Pursued</td>
      <td>2021</td>
      <td>7280958</td>
      <td>Fathia Absie</td>
    </tr>
    <tr>
      <th>6552</th>
      <td>15596914</td>
      <td>Voodoo</td>
      <td>2021</td>
      <td>7385237</td>
      <td>Phillip Youmans</td>
    </tr>
    <tr>
      <th>6604</th>
      <td>11753636</td>
      <td>Private Imaginings and Narrative Facts</td>
      <td>2021</td>
      <td>9913311</td>
      <td>Edward Owens</td>
    </tr>
  </tbody>
</table>
<p>173 rows × 5 columns</p>
</div>




```python
df_movies.plot(x = 'Title', y='Year', kind = 'scatter')
```

```python
import matplotlib.pyplot as plt
import seaborn as sns

sns.histplot(df_movies.Year, kde=True)
plt.show()
```


```python
df_movies['Year'].describe()
```




    count    5699.000000
    mean     1998.656431
    std        19.859445
    min      1919.000000
    25%      1991.000000
    50%      2004.000000
    75%      2014.000000
    max      2027.000000
    Name: Year, dtype: float64




```python
def df_director_id(name: str):
    for director in df_movies.itertuples():
        if director.Name == name:
            return (str(director.Person_ID).zfill(7))
```


```python
def movie_format(movie_id):
    if movie_id == None:
        return None
    
    if len(movie_id) > 1:
        return ', '.join(map(str, movie_id))
    else:
        return ' '.join(map(str, movie_id))


def movie_metascore(movie_id):
    return movie_id['metascore']


def movie_budget(movie_id):
    box_off = movie_id.get('box office', None)
    if box_off == None:
        return None
    else:
        budget = re.search('[0-9,]*\d', str(movie_id['box office']))
            
    return  budget.group(0)
    
#tstmovie = ia.get_movie('13972246', info=['main', 'critic_reviews', 'vote details', 'plot'])
#print(tstmovie['box office'])
#budget = re.search('[0-9,]*\d', '$1') #str(tstmovie['box office']))
#print(budget)
#print(movie_budget(tstmovie))
# print(movie_metascore(tstmovie))
```


```python
df_movies['Type'] = ""
df_movies['Genres'] = ""
df_movies['Metascore'] = ""
df_movies['Budget'] = ""
df_movies['Votes'] = ""
df_movies['Rating'] = ""
df_movies['Runtime'] = ""
df_movies
```


```python
df_movies = df_movies.reset_index()
```


```python
df_movies.head(20)
```


```python
#x = 0
for i in range (6049, 6316): #df_movies.itertuples():
    if i % 150 == 0:
        print(f"{df_movies['Movie_ID'][i]} and  {df_movies['Title'][i]}") 
        time.sleep(180) # 3 minutes
    movie = ia.get_movie(df_movies['Movie_ID'][i], info=['main', 'critic_reviews', 'vote details', 'plot']) #ia.get_movie(df_movies['Movie_ID'][i])
    df_movies.loc[i, 'Type']        = movie.get('kind', None)
    df_movies.loc[i, 'Runtime']     = movie_format(movie.get('runtime', None)) #movie.get('runtime', None)
    df_movies.loc[i, 'Rating']      = movie.get('rating', None)
    df_movies.loc[i, 'Votes']       = movie.get('votes', None)
    df_movies.loc[i, 'Genres']      = movie_format(movie.get('genres', None)) #str(movie.get('genres', None))
    df_movies.loc[i, 'Metascore']   = movie.get('metascore', None)
    df_movies.loc[i, 'Budget']      = movie_budget(movie)
    #x +=1

    #df_movies

    # if x == 10:
    #     break
#df_movies.head(10)        
```

    9847438 and  Football Town: Cleveland Ohio
    6340186 and  Film 13



```python
df_movies.to_csv("Director_Movies_Analyze.csv", encoding='utf-8-sig')
```


```python
for Movie_ID in df_movies.columns[1:]:
    print(df_movies[Movie_ID])
```


```python
spike_lee = ia.get_person('0000490')
sidney_poitier = ia.get_person('0001627')
tvshow = ia.get_movie('209093', info='critic_reviews')

```


```python
print(f"{spike_lee['birth date']}  {spike_lee['height']} {spike_lee['birth info']} ")
print(f"{sidney_poitier['birth date']}  {sidney_poitier['height']} {sidney_poitier['birth info']} ")
```

    1957-03-20  5' 7" (1.7 m) {'birth place': 'Atlanta, Georgia, USA'} 
    1927-02-20  6' 2½" (1.89 m) {'birth place': 'Miami, Florida, USA'} 



```python
tvshow.infoset2keys

```




    {'critic_reviews': []}




```python
mv = ia.get_movie('790770', info=['main', 'critic_reviews', 'vote details', 'plot'])
#metascore = ia.get_movie_critic_reviews('7349662')
```


```python
def person_height_ft(person_id):
    person_height = person_id.get('height', None)
    print(person_height)
    if person_height == None:
        return None
    else:
        meter = re.search('[0-9\']+\s[0-9]+.?', str(person_height))
        height = re.search('\([0-9]+\.?[0-9]*', str(person_height))
        height = str(height.group(0)).replace('(','')
    return  meter.group(0)

tstperson = ia.get_person('0002700')
#str(director.Person_ID).zfill(7)
#print(tstmovie['box office'])
#budget = re.search('[0-9,]*\d', '$1') #str(tstmovie['box office']))
#print(budget)
birth_list = tstperson.get('birth info', None)
#city, state, country = str(birth_list['birth place']).split(", ")

#city, state, country = birth_list.split(',')
#print(tstperson['mini biography'])
#print(tstperson.get('height', None))
print(birth_list)
#print(f"{city} adfsfd {state} aerrod {country}")
#tstperson['birth info']
# print(movie_metascore(tstmovie))
```

    {'birth place': 'USA'}



```python
tstperson.infoset2keys
```




    {'main': ['birth info', 'headshot', 'filmography', 'imdbID', 'name'],
     'biography': ['headshot',
      'birth name',
      'height',
      'mini biography',
      'trade mark',
      'trivia',
      'quotes',
      'birth date',
      'birth notes']}




```python
df_director_names = pd.read_csv("director_names_analyze.csv", index_col=[0], encoding="utf-8-sig")
```


```python
for i in range (67, 311): #df_director_names.itertuples():
    if df_director_names['Person_ID'][i] == 0:
        continue
    if i % 100 == 0:
        print(f"{df_director_names['Person_ID'][i]} and  {df_director_names['Name'][i]}") 
        time.sleep(180) # 3 minutes
    director = ia.get_person(str(df_director_names['Person_ID'][i]).zfill(7)) 

    person_height = director.get('height', "")
    print(person_height)
    if person_height == "":
        height_m = height_ft = None
    else:    
        height_ft = re.search('[0-9\']*\s[0-9]*.?', str(person_height))
        height_ft = height_ft.group(0)
        height_m = re.search('\([0-9]+\.?[0-9]*', str(person_height))
        height_m = str(height_m.group(0)).replace('(','')
    
    birth_list = tstperson.get('birth info', "")
    if birth_list == "":
        birth_city = birth_state = birth_country = None
    else:
        birth_city, birth_state, birth_country = str(birth_list['birth place']).split(", ")
    
    df_director_names.loc[i, 'Birth Date']  = director.get('birth date', None)
    df_director_names.loc[i, 'Height ft']   = height_ft
    df_director_names.loc[i, 'Height m']    = height_m
    df_director_names.loc[i, 'Birth City']       = birth_city
    df_director_names.loc[i, 'Birth State']      =  birth_state
    df_director_names.loc[i, 'Birth Country']   =  birth_country
    df_director_names.loc[i, 'Headshot']   =  director.get('headshot', None)
    try:
        df_director_names.loc[i, 'Biography']   =  director.get('biography', None)
    except:
        df_director_names.loc[i, 'Biography'] = "Error"
        print("test")

```


```python
df_director_names['Birth Date'] = ""
df_director_names['Height ft'] = ""
df_director_names['Height m'] = ""
df_director_names['Birth City'] = ""
df_director_names['Birth State'] = ""
df_director_names['Birth Country'] = ""
df_director_names['Headshot'] = ""
df_director_names['Biography'] = ""
df_director_names
```


```python
df_director_names.to_csv("director_names_analyze.csv", encoding='utf-8-sig')
```


```python
for i in range (0, 311): #df_director_names.itertuples():
    if df_director_names['Person_ID'][i] == 0:
        continue
    # if i % 100 == 0:
    #     print(f"{df_director_names['Person_ID'][i]} and  {df_director_names['Name'][i]}") 
    #     time.sleep(180) # 3 minutes
    director = ia.get_person(str(df_director_names['Person_ID'][i]).zfill(7)) 

    # person_height = director.get('height', "")
    # print(person_height)
    # if person_height == "":
    #     height_m = height_ft = None
    # else:    
    #     height_ft = re.search('[0-9\']*\s[0-9]*.?', str(person_height))
    #     height_ft = height_ft.group(0)
    #     height_m = re.search('\([0-9]+\.?[0-9]*', str(person_height))
    #     height_m = str(height_m.group(0)).replace('(','')
    
    birth_list = director.get('birth info', None)
    if birth_list is None:
        birth_city = birth_state = birth_country = None    
    elif len((birth_list['birth place']).split(", ")) == 4:
        birth_city = None
        birth_city, city, birth_state, birth_country = str(birth_list['birth place']).split(", ") 
    elif len((birth_list['birth place']).split(", ")) == 2:
        birth_city = None
        birth_state, birth_country = str(birth_list['birth place']).split(", ")
    elif len((birth_list['birth place']).split(", ")) == 1:
        birth_city = birth_state = None
        birth_country = str(birth_list['birth place'])
    else:
        birth_city, birth_state, birth_country = str(birth_list['birth place']).split(", ")
        
            #birth_city = birth_state = birth_country = "Error"
    
    # df_director_names.loc[i, 'Birth Date']  = director.get('birth date', None)
    # df_director_names.loc[i, 'Height ft']   = height_ft
    # df_director_names.loc[i, 'Height m']    = height_m
    df_director_names.loc[i, 'Birth City']       = birth_city
    df_director_names.loc[i, 'Birth State']      =  birth_state
    df_director_names.loc[i, 'Birth Country']   =  birth_country
    # df_director_names.loc[i, 'Headshot']   =  director.get('headshot', None)
    # try:
    #     df_director_names.loc[i, 'Biography']   =  director.get('biography', None)
    # except:
    #     df_director_names.loc[i, 'Biography'] = "Error"
    #     print("test")
```
