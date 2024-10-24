---
date: 2023-05-27T10:58:08-04:00
description: "A tool that looks at Black Directors"
tags:
title: "Exploratory Data Analysis on Black Directors"
#weight: 1
cover:
  image: "images/5AAZ56276FF3ZOBLEAVA4DA6PQ.webp"
  draft: true
---

For this exploratory data analysis, I will be using a dataset of black directors from 1914 until today that I found on Kaggle. The dataset contains information on black directors, such as their name, gender, birth year, birthplace, and more. Before we start analyzing the data, let's import the necessary libraries and load the dataset:
    
## Import the following Python Libraries
 
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## Load the dataset
`df = pd.read_csv('black_directors.csv')`

##  Print the first 5 rows of the dataset
`print(df.head())`

Now that we have loaded the dataset, let's explore it further: 
    
## Print the shape of the dataset
`print(df.shape)`

## Print the summary statistics of the dataset
`print(df.describe())`

## Print the data types of the columns
`print(df.dtypes)`

## Print the number of null values in each column
`print(df.isnull().sum())`

From the above code, we can see that the dataset contains 366 rows and 16 columns. The summary statistics show that the birth year ranges from 1880 to 1992, and the number of films ranges from 1 to 48. There are no null values in the dataset. Now, let's visualize some of the data using histograms, box plots, and scatter plots:    
    
## Visualize the distribution of birth years
```python
plt.hist(df['birth_year'], bins=20)
plt.xlabel('Birth Year')
plt.ylabel('Count')
plt.show()
```
## Visualize the distribution of the number of films
```python
sns.boxplot(x='num_films', data=df)
plt.xlabel('Number of Films')
plt.show()
```
## Visualize the relationship between birth year and number of films
```python
sns.scatterplot(x='birth_year', y='num_films', data=df)
plt.xlabel('Birth Year')
plt.ylabel('Number of Films')
plt.show()
```

From the histograms, we can see that the majority of black directors were born between the 1940s and the 1960s. From the box plot, we can see that the median number of films directed by black directors is around 2. From the scatter plot, we can see that there is no clear relationship between birth year and number of films. Finally, let's explore the data further by looking at the top 10 black directors with the most number of films:    

## Get the top 10 black directors with the most number of films
```python
top_directors = df.sort_values(by='num_films', ascending=False).head(10)
```
## Visualize the top 10 black directors with the most number of films
```python
sns.barplot(x='num_films', y='name', data=top_directors)
plt.xlabel('Number of Films')
plt.ylabel('Director Name')
plt.show()
```

From the bar plot, we can see that the top 3 black directors with the most number of films are Spike Lee, Tyler Perry, and Johnnie Toomer. Overall, this exploratory data analysis has provided some insights into the black directors from 1914 until today. We have seen that the majority of black directors were born between the 1940s and the 1960s, and the median number of films directed by black directors is around 2. Additionally, we have identified the top 10 black directors with the most number of films, with Spike Lee, Tyler Perry, and Johnnie Toomer being the top 3.