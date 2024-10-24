---
date: 2024-10-24T16:47:08-04:00
description: "Analyzing the Black Directors throughout the years - Part I"
tags:
title: "Exploratory Data Analysis on Black Directors - The Scraper"
#weight: 1
cover:
  image: "images/th-3440962630.jpeg"
---
I was wondering if there has been a progression of Black movies being made since the question why is the oscar so white. I wondered what it would look like to see the black films being made throughout the years. It was hard to say what qualifies as a black movies as there are a significant amount of movies made that targetted a black audience but wasn't necessarily created by black artist. So I thought if the movies was directed by a black director then it qualifies as a black movies and wanted to see the growth of black directors.

I needed to find a way to get black film directors into a database to do some analysis, so I figured the best way to get a significant amount of information was throught wikipedia and IMDB.

This is where it starts by creating a scraper that is going to be used to find African American Film Directors from combining information from Wikipedia and IMDB



```python
from bs4 import BeautifulSoup
from imdb import Cinemagoer
import requests
import pandas as pd
import re
import time
```

## This creates the Director's list from both wikipedia pages
It is going through each hyperlink on the wikipedia website and adds them to a list. It is broken up into two variables, to make sure to get all the directors from A-Z:\
director_links\
director_links2 


```python
url = "https://en.wikipedia.org/wiki/Category:African-American_film_directors"
url2 = "https://en.wikipedia.org/w/index.php?title=Category:African-American_film_directors&from=P"
movie_list = set()
director_films = set()
testing_list = []
names = []

# this will take out names that are organizations and take out the added identifiers at the end of names
name_exclude = ['Black women filmmakers', 'Pioneers of African-American Cinema']
name_check = ['film director', 'filmmaker', 'director', 'writer', 'actor', 'musician']

# parsing out the <a> tags 
def director_page(url):
    try:
        result = requests.get(url)
        result.raise_for_status()
        doc = BeautifulSoup(result.text, "html.parser")
        
    except Exception as e:
        print(e)
        
    return doc.find(class_="mw-category mw-category-columns").find_all('a')


director_links = director_page(url)
director_links2 = director_page(url2)
```

### Creates a list of (Director Names, Wikipedia Link)



```python
def Directors(names, object):    
    for director in object:
        if any(y in director.string for y in name_exclude):
            director.string = ''
        elif any(x in director.string for x in name_check):
            director.string  = director.string.rsplit('(', 1)[0]

        if director.string or director.string in names: 
            #         LIST:    NAME                       WIKI_LINK
            names.append([str(director.string.rstrip()), "https://en.wikipedia.org" + director['href']])        
    return names 

names = Directors(names, director_links)
names = Directors(names, director_links2)
```

### Creates the Dataframe with Name and Wiki_Link Columns



```python
df = pd.DataFrame(names, columns=['Name', 'wiki_link'])
df
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Abdisalam Aato</td>
      <td>https://en.wikipedia.org/wiki/Abdisalam_Aato</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Gay Abel-Bey</td>
      <td>https://en.wikipedia.org/wiki/Gay_Abel-Bey</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fathia Absie</td>
      <td>https://en.wikipedia.org/wiki/Fathia_Absie</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Anita W. Addison</td>
      <td>https://en.wikipedia.org/wiki/Anita_W._Addison</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Omowale Akintunde</td>
      <td>https://en.wikipedia.org/wiki/Omowale_Akintunde</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>313</th>
      <td>Tricia Woodgett</td>
      <td>https://en.wikipedia.org/wiki/Tricia_Woodgett</td>
    </tr>
    <tr>
      <th>314</th>
      <td>Bille Woodruff</td>
      <td>https://en.wikipedia.org/wiki/Bille_Woodruff</td>
    </tr>
    <tr>
      <th>315</th>
      <td>Fronza Woods</td>
      <td>https://en.wikipedia.org/wiki/Fronza_Woods</td>
    </tr>
    <tr>
      <th>316</th>
      <td>Tanya Wright</td>
      <td>https://en.wikipedia.org/wiki/Tanya_Wright</td>
    </tr>
    <tr>
      <th>317</th>
      <td>Phillip Youmans</td>
      <td>https://en.wikipedia.org/wiki/Phillip_Youmans</td>
    </tr>
  </tbody>
</table>
<p>318 rows × 2 columns</p>
</div>



### Saves the Dataframe as a csv file



```python
df.to_csv("director_names.csv")
print(len(names))
```

    318


### This is set up for getting Filmography from Director's Wikipedia page
* It first opens up each wikipedia page of the director and checks to see what type of Filmography section or if it has one at all.
* Then depending on what type it finds. It will get the list of movies and the year it was released.
* In the end, it will create a list with the "Movie, Year, Wikipedia" which will be used to identify a director on IMDB


```python
director_films = set()
test = []
mxm = []

#The range is performed in set of 50 to avoid timeout issues and to test if the data is pulled
#the total range is 311 which is the len(names)
for i in range(0, len(names)-1): 
#for i in range(0, 300): 

    #add a wait time of 5 seconds so there isn't a timeout
    if i % 50 == 0:
        time.sleep(5)

    wiki_url = "https://en.wikipedia.org" + str(df.loc[i].at['wiki_link'])      
    table_check = False

    try:
        result = requests.get(wiki_url)
        result.raise_for_status()
        doc = BeautifulSoup(result.text, "html.parser")
    except Exception as e:
        print(e)

    if doc.find(id="Filmography") and doc.find(class_="wikitable"):
        if doc.find(id="Filmography").parent.find_next("th").find_next("th").text.rstrip() == "Title" or \
           doc.find(id="Filmography").parent.find_next("th").find_next("th").text.rstrip() == "Film": 
            film_list = doc.find(id="Filmography").parent.find_next("th").find_all_next("tr", limit=6)        
            table_check = True                  
        else:
            film_list = doc.find(id="Filmography").parent.find_all_next("li", limit=6)
    elif doc.find(id="Films") and doc.find(class_="wikitable"):
        if doc.find(id="Films").parent.find_next("th").find_next("th").text.rstrip() == "Film": 
            film_list = doc.find(id="Films").parent.find_next("th").find_all_next("tr", limit=6)        
            table_check = True
        else:
            film_list = doc.find(id="Films").parent.find_all_next("li", limit=6)
    elif doc.find(id="Filmography"):
        film_list = doc.find(id="Filmography").parent.find_all_next("li", limit=6)
    elif doc.find(id="Films_2"):
        film_list = doc.find(id="Films_2").parent.find_all_next("li", limit=6)
    elif (doc.find(id="Films") or doc.find(id="Film")) and doc.find(class_="mw-headline"):
        if doc.find(id="Films"):
            film_list = doc.find(id="Films").parent.find_all_next("li", limit=6)            
        else:
            film_list = doc.find(id="Film").parent.find_all_next("li", limit=6)
    elif (doc.find(id="Selected_filmography") or doc.find(id="Select_Filmography")) and doc.find(class_="mw-headline"):
        if doc.find(id="Selected_filmography"):
            film_list = doc.find(id="Selected_filmography").parent.find_all_next("li", limit=6)
        else:
            film_list = doc.find(id="Select_Filmography").parent.find_all_next("li", limit=6)
    elif doc.find(id="Filmography_as_director") and doc.find(class_="mw-headline"):
        film_list = doc.find(id="Filmography_as_director").parent.find_all_next("li", limit=6)
    else:
        #print("Couldn't find one for ", wiki_url)
        continue

    for film in film_list:
        
        #Filmography Section has a Film Table: Year Movie and table check is used since it changes the format
        if table_check == True:            
            if re.search('\d\d\d\d', film.text):
                s = film.text.rsplit('\n\n')
                if len(s) >= 2:
                    t = [s[1].strip(), s[0].lstrip(), wiki_url]
                else:
                    s[0] = s[0].strip()
                    s[0] = re.sub('\[\d+\]',"", s[0])
                    s = s[0].split('\n')                         
                    t = [s[1].strip(), s[0].strip(), wiki_url]                         
                if re.search('\n', t[1]):
                    t = t[1].split('\n')
                    t = [t[1], t[0], wiki_url]                
                director_films.add(tuple(t))                     
        #Filmography Section has Number Movie (year) format
        elif film.text[:1].isdigit() and re.search('\(\d\d\d\d\)', film.text):
            film = film.text.rsplit(r')', 1)[0]
            film = film.replace('(', ' ', 1)
            director_films.add((film[:-6].strip(), film[-4:].strip(), wiki_url))            
        #Filmography Section has Movie (year) format
        elif  ')' in film:
            film = film.text.rsplit(r' ', 1)[0]
            film = film.replace('(', ' ', 1)
            director_films.add((film[:-6].strip(), film[-4:].strip(), wiki_url))            
        #Filmography Section has Year Movie format
        elif film.text[:1].isdigit():
            s = film.text.rsplit('(', 1)[0]
            s = s.split(":")            
            if len(s) >= 2:           
                director_films.add((s[1].strip(), s[0].strip(), wiki_url))
            else:
                continue #print( "WILL TRY TO FIX LATER OR IGNORE: ", film.text)
        #Filmography Section has Movie year format
        else:
            bird = film.text.split(' (', 1)
            tweet = re.search('\d\d\d\d', film.text)            
            try:
                tweet = str(tweet.group())
            except:
                tweet = "0000"
            mxm = bird[0].strip(), tweet.strip(), wiki_url            
            director_films.add(tuple(mxm))

#print(director_films)
```

#### This searches through people and tries to match person to Director via Movies  
#### It then adds all the movies of a director to a Movie List


```python
dfMovies = pd.DataFrame(columns=['Movie_ID', 'Title', 'Year', 'Person_ID', 'Name'])
```


```python
rawdf = pd.DataFrame(director_films)
rawdf.to_csv('raw_movie_wikipedia.csv')
```


```python
ia = Cinemagoer()
person_id = []
found = False

#range is from 0 - length of directors: Only sections of the search at a time to avoid timeout issues
# so it will sleep for 5 seconds every 50 searches done
for i in range(0, len(names)): 
    
    #add a wait time of 5 seconds so there isn't a timeout
    if i % 50 == 0 and i > 0:
        print(i)
        time.sleep(5)

    people = ia.search_person(names[i][0])
    found = False
    for person in people:
        if found == False:
            #print(person['name'])        
            works = ia.get_person(people[0].personID)
            #print (works['filmography'].keys())
            if works.has_key('director'):
                for job in works['filmography'].keys():
                    if job == 'director': 
                        #print('Person ID:', person.personID, '\tDirector:', person['name'], '\t# Job:', job, )
                        for movie in works['filmography'][job]:
                            #print(movie, " ", movie.get('year'))                        
                            if next((i for i, v in enumerate(director_films) if v[0] == movie['title'] and v[1] == str(movie.get('year'))), None) and found == False:                             
                                #print("test 1:", '\tID:%s Title:%s Year: %s' % (movie.movieID, movie['title'], movie['year']))  
                                found = True                                                           
                                for m in works['filmography'][job]:            
                                    year = 0000 if 'year' not in m else int(m['year'])
                                    #print(m['title'],'\t', m['year'])
                                    dfMovies.loc[len(dfMovies.index)] = [m.movieID, m['title'], year, person.personID, person['name']] 
                            elif next((i for i, v in enumerate(director_films) if v[0] == movie['title']), None) and found == False:
                                #print ("test 2:", '\tID:%s Title:%s Year: %s' % (movie.movieID, movie['title'], movie['year']) )
                                found = True                                  
                                for m in works['filmography'][job]:
                                    year = 0000 if 'year' not in m else int(m['year'])
                                    #print(m['title'],'\t', m['year'])
                                    dfMovies.loc[len(dfMovies.index)] = [m.movieID, m['title'], year, person.personID, person['name']]                            
            else:
                continue #print('Person ID:', person.personID, '\tName:    ', person['name'], '\t# Job:', job, "(Doesn't have a filmography)")
```

    50
    100
    150
    200
    250
    300



```python
dfMovies.to_csv("imdb_movies_directors.csv")
```
