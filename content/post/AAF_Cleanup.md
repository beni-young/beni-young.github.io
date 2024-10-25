---
date: 2024-10-24T16:47:08-04:00
description: "Analyzing the Black Directors throughout the years - Part II"
tags:
title: "Exploratory Data Analysis on Black Directors - The Clean Up"
#weight: 1
cover:
  image: "/images/th-1062852672.jpeg"
draft: false
---

It is time to do a little clean up with the information collected from the Scraper. Another pass through using Director Film Dataframe to see if there are new movies to add and to add IMDB id to director film list


```python
import pandas as pd
from imdb import Cinemagoer
import re
import os.path

ia = Cinemagoer()
file = 'missing_directors.csv'
check_missing_directors_file = os.path.isfile(file)
df_director_films = pd.read_csv('imdb_movies_directors.csv', encoding= 'unicode_escape')
df_director_names = pd.read_csv('director_names.csv', encoding='unicode_escape')




for name in df_director_names.itertuples(): 
    for director in df_director_films.itertuples(): 
        if name.Name == director.Name:
            df_director_names.at[name.Index, 'Person_ID'] = int(director.Person_ID)




df_director_names['Person_ID'] = df_director_names['Person_ID'].fillna(0)
df_director_names = df_director_names.astype({"Person_ID": int})
df_director_names = df_director_names.drop("Unnamed: 0", axis=1)
df_director_names.to_csv("director_names_imdb_person_id.csv")
df_director_names
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
      <th>Name</th>
      <th>wiki_link</th>
      <th>Person_ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abdisalam Aato</td>
      <td>https://en.wikipedia.org/wiki/Abdisalam_Aato</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Gay Abel-Bey</td>
      <td>https://en.wikipedia.org/wiki/Gay_Abel-Bey</td>
      <td>8498</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fathia Absie</td>
      <td>https://en.wikipedia.org/wiki/Fathia_Absie</td>
      <td>7280958</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Anita W. Addison</td>
      <td>https://en.wikipedia.org/wiki/Anita_W._Addison</td>
      <td>11697</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Omowale Akintunde</td>
      <td>https://en.wikipedia.org/wiki/Omowale_Akintunde</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>313</th>
      <td>Tricia Woodgett</td>
      <td>https://en.wikipedia.org/wiki/Tricia_Woodgett</td>
      <td>0</td>
    </tr>
    <tr>
      <th>314</th>
      <td>Bille Woodruff</td>
      <td>https://en.wikipedia.org/wiki/Bille_Woodruff</td>
      <td>937306</td>
    </tr>
    <tr>
      <th>315</th>
      <td>Fronza Woods</td>
      <td>https://en.wikipedia.org/wiki/Fronza_Woods</td>
      <td>0</td>
    </tr>
    <tr>
      <th>316</th>
      <td>Tanya Wright</td>
      <td>https://en.wikipedia.org/wiki/Tanya_Wright</td>
      <td>942859</td>
    </tr>
    <tr>
      <th>317</th>
      <td>Phillip Youmans</td>
      <td>https://en.wikipedia.org/wiki/Phillip_Youmans</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>318 rows × 3 columns</p>
</div>




```python
#Currently as of 6/23/2023 getting biography information like birth dates is not working
#https://github.com/cinemagoer/cinemagoer/issues/462
person = ia.get_person('0000332', info=['biography'])
print(person.infoset2keys)
```

    {'biography': []}


Checking for directors that were not found automatically in IMDB  
Creating a list of missing directors and then manually seeing if they have a imdb id to use to add to imdb_movies_directors.csv


```python
def imdb_search (name):
    imdb_people_search = []
    people = ia.search_person(name)
    for person in people:            
        if person['name'] == name:
            imdb_people_search.append([person.personID, person['name']])
    
    if len(imdb_people_search) > 1 or not len(imdb_people_search):
        imdb_people_search = 0

    return imdb_people_search

# if there isn't already a missing_directors.csv then create one
# else take the missing_directors.csv and check to see if any directors can 
# be added to the director_names_imdb_person_id.csv
# also their movies added to imdb_movies_diretors to the end of that list
if not check_missing_directors_file:
    missing_directors_df = df_director_names.loc[df_director_names['Person_ID'] == 0]
    missing_directors_df.to_csv("missing_directors.csv")
    missing_directors_df
else:
    missing_directors_df = pd.read_csv(file, encoding= 'unicode_escape')

missing = ia.search_person(missing_directors_df.loc[7]['Name'])
print(missing)

madeline = ia.get_person('7507414')
print(madeline)

if madeline.has_key('director'):
    for movies in madeline['filmography']['director']:
        year = 0000 if 'year' not in movies else int(movies['year']) 
        print(movies['title'], year)
```

```python
   [<Person id:1919862[http] name:_Mya Baker_>, 
    <Person id:7983695[http] name:_Mya Baker_>, 
    <Person id:1263939[http] name:_Danielle Panabaker_>, 
    <Person id:0659048[http] name:_Kay Panabaker_>, 
    <Person id:6803655[http] name:_Julia Maren Baker_>,
    <Person id:5208385[http] name:_Brianna Baker_>,
    <Person id:1019674[http] name:_Sala Baker_>,
    <Person id:1128564[http] name:_Kitana Baker_>,
    <Person id:10453571[http] name:_Malia Baker_>, 
    <Person id:0000711[http] name:_Rick Baker_>, 
    <Person id:6966400[http] name:_Stella Baker_>, 
    <Person id:12140549[http] name:_Maya Baker_>, 
    <Person id:4124843[http] name:_Mia Baker_>, 
    <Person id:11325477[http] name:_Eva Brooke Baker_>,
    <Person id:9698063[http] name:_Ekaterina Baker_>, 
    <Person id:1179722[http] name:_Anita Baker_>, 
    <Person id:0851963[http] name:_Andrea Baker_>,
    <Person id:0672060[http] name:_D.A. Pennebaker_>,
    <Person id:4080841[http] name:_Lucy May Barker_>,
    <Person id:2531646[http] name:_Shauna Baker_>]

    Madeline Anderson
    Being Me 1975
    I Am Somebody 1970
    A Tribute to Malcolm X 1967
    Integration Report I 1960
```


```python
def update_directors_name(tmpID, tmpName):
    for name in df_director_names.itertuples(): 
             if name.Name == tmpName:                
                missing_directors_df.at[name.Index, 'Person_ID'] = int(tmpID)

for n in range(0, len(missing_directors_df.index)):
    imdb_person = imdb_search(missing_directors_df['Name'][n])
    if imdb_person == 0:
        continue
    else:
        person = ia.get_person(imdb_person[0][0])
        print(person)
        if person.has_key('director'):
            for movies in person['filmography']['director']:                
                update_directors_name(imdb_person[0][0], imdb_person[0][1])                            

```


```python
missing_directors_df.to_csv("missing_directors.csv")
```


```python
def wiki_imdb(imdb_id, wiki_name):
    imdb_person = [imdb_id, wiki_name]
    person = ia.get_person(imdb_person[0])
    if person.has_key('director'):
        for movies in person['filmography']['director']:
            year = 0000 if 'year' not in movies else int(movies['year'])            
            df_director_films.loc[len(df_director_films.index)] = [movies.movieID, movies['title'], year, imdb_person[0], imdb_person[1]]
            update_directors_name(imdb_person[0], imdb_person[1])            
```


```python
wiki_imdb('10555174', 'Robert Van Lierop')

df_director_films.to_csv("Director_Movies_Clean.csv", index=False)
```


```python
from bs4 import BeautifulSoup
import requests

imdb_re = 'https://www.imdb.com/name/'
total = 0
for i in range(0, len(test_df.index)):   
    wiki_url = "https://en.wikipedia.org" + test_df['wiki_link'][i]   
    print(wiki_url)  
    table_check = False

    try:
        result = requests.get(wiki_url)
        result.raise_for_status()
        doc = BeautifulSoup(result.text, "html.parser")
    except Exception as e:
        print(e)


    imdb_id = doc.find_all(rel="nofollow")

    for imdb in imdb_id:        
        if re.search(imdb_re, imdb.get('href')):            
            # print(imdb)
            print(imdb.text)
            id = re.search('\d+', imdb.get("href"))
            print(id.group())
            wiki_imdb(id.group(), imdb.text)
            table_check = True            
    if not table_check:        
        total += 1
        print(f"Total: {total} - There wasn't a match for {wiki_url}")

```


```python
df_director_films.describe()
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
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>6611.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1797.361972</td>
    </tr>
    <tr>
      <th>std</th>
      <td>601.922601</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1977.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2001.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2013.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2027.000000</td>
    </tr>
  </tbody>
</table>
</div>


