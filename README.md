
# Microsoft Movie Venture

Author: Sylvia Sarange Manono

## Overview

The goal of this project is to perform exploratory data analysis on movie data to provide insights that can help guide Microsoft's new movie studio on what types of films to produce.

## Business Problem

With more and more movie studios opting to distribute their content on their own platforms, successful entertainment companies today are increasingly dependent on original content. Additionally, major tech companies like Netflix, Amazon, and Apple have been investing heavily in creating original video content. For instance, at its inception, Netflix, one of the pioneer movie streaming sites rose to popularity by distributing content licensed from established movie studios. However, with the growing demand for streaming services, these studios established their own streaming platforms, forcing Netflix to invest heavily in its own content.  

Microsoft, a world renowned tech company, has decided to follow suit by establishing a new movie studio division to compete with other entertainment industry stakeholders. However, Microsoft has little prior experience in the movie production business. To assist in defining the strategic direction for the new studio, data-driven insights are needed on current box office trends and successful movie genres, styles, and so on. This project seeks to provide actionable insights from explortatory data analysis to inform Microsoft's decision and ensure the venture is successful and profitable.

## **Data Understanding**

### **Data Sources**

Movie datasets are obtained from the following two sources:

1. im.db
2. tn.movie_budgets.csv
3. rt.movie_info.tsv
4. tmdb.movies.csv

### **Library Importation**

# Importing the necessary libraries and modules
import pandas as pd
import numpy as np
import csv
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline 
import os
import sqlite3
import pandas as pd

# Creating a path

imdb_path = os.path.join('im.db')

# Opening a connection and initializing a cursor

conn = sqlite3.connect('im.db')
cursor = conn.cursor()
The database has various tables, but the ones that are of interest in this case are 'movie_basics' and 'movie_ratings'.
table_name_query = """SELECT name 
                      AS 'Table Names' 
                      FROM sqlite_master 
                      WHERE type='table';"""

pd.read_sql(table_name_query, conn)

#### *Reading the budget csv file*

# Reading the bom.movies csv file and displaying the first two entries

bomovies_df = pd.read_csv("zippedData/bom.movie_gross.csv.gz")
bomovies_df.head(2)

# Reding the movie budgets csv file and displaying the first two entries

movie_budgets_df = pd.read_csv("zippedData/tn.movie_budgets.csv.gz")
movie_budgets_df.head(2)
movie_budgets_df.columns

## **Data Preparation**

**a.  Combining the relevant tables in im.db**

I combined the 'movie_basics' and 'movie_ratings' tables by executing thr following SQL command in DB Browser for SQLite. By doing so, I ensured that the data contained in the merged table was complete with no null values.

SELECT *
FROM movie_basics INNER JOIN movie_ratings
ON movie_basics.movie_id = movie_ratings.movie_id

**b. Checking for null values in tn.movie_budgets.csv**

movie_budgets_df.isna().sum()
There are no null values in this file

**c. Checking the column data types in tn.movie_budgets.csv**

movie_budgets_df.dtypes
It seems the data in all columns are strings. Since we would like the production, domestic and worldwide gross amounts to be integers, we convert the (str) to (int) as follows:
movie_budgets_df["production_budget"] = movie_budgets_df["production_budget"].replace("[$,]", "", regex=True).astype(float)
movie_budgets_df["worldwide_gross"] = movie_budgets_df["worldwide_gross"].replace("[$,]", "", regex=True).astype(float)
movie_budgets_df["domestic_gross"] = movie_budgets_df["domestic_gross"].replace("[$,]", "", regex=True).astype(float)
movie_budgets_df.head(10)
Additionally, we would like to compare movie release years to track annual trends. This information could be useful in assessing the cyclical (if any) trends in preferred genres.
movie_budgets_df['release_date'] = movie_budgets_df['release_date'].str[-4:]
movie_budgets_df.head(10)

## Data Analysis

## High-grossing Movie Budgets

At this stage, I'm working with the hypothesis that production budgets are proportional to wordwide gross income. I will prove or disprove this hypothesis using a scatter plot to establish corelation.

![correlation](https://github.com/SarangeManono/dsc-phase-1-project-v2-4/assets/164891014/6983f851-eb6b-4d9c-b1aa-d3c4589b8f06)

![production budgets](https://github.com/SarangeManono/dsc-phase-1-project-v2-4/assets/164891014/726b2459-a7c1-45b6-b12f-3a3f23faf1ef)


## Popular Genres

![genres](https://github.com/SarangeManono/dsc-phase-1-project-v2-4/assets/164891014/d7fe697a-17c2-4381-bf41-8cd58caf9856)

## Popular Languages

![languages](https://github.com/SarangeManono/dsc-phase-1-project-v2-4/assets/164891014/85bb1e24-bfb4-4eb3-9ee7-96fa532efbca)

## **Recommendations** 

### **1. Budgetary Allocation**

Using the worldwide gross income data along with available production budget information, I identified a positive corelation between both. As a result, it is possible to determine a ratio of the production budget and the expected income. By doing so, Microsoft will make data-driven investment decisions after considering realistic possible impacts on the company's bottom line. Besides that, this analysis can be expanded to provide nuance between movie performance in domestic and international markets.

**Recommedation:** Production budget approvals should be based on histroric data with gross income consideed. Additionally, nuanced decisions should be made based on the target market, i.e. domestic or international audiences.

### **2. Genre Preferences**

The findings show that viewers prefer diverse genres, but Drama emerged as the leading one. Except for comedy, which came second among the most preferred, drama featured in the next 3 of the top 5 slots. This result was due to the viewers selecting a combination of genres due to the multi-faceted nature of most movies in today's entertainment landscape. The top 5 slots were occupied by:
    a. Drama
    b. Comedy
    c. Comedy, and drama
    d. Drama, and Mystery and Suspense
    e. Art House and International, and Drama

**Recommendation:** MIcrosoft would benefit highly from investing in the popular genres, especially Drama and Comedy.

### **3. Language Preferences**

Based on the target audience, the language spoken in a movie is instrumental in determining its success. For instance, despite being the third-most spoken language globally, the findings revealed English as the most popular language among 96.7% of respondents. Consequently, this is the recommended language for a company looking to enter the movie-making sector. However, an argument can be made in favor of the other top languages, including French (1.5%), Japanese (1.1%), Spanish (0.6%), and Russian (0.1%). With English being so prominent among already-released content so far, there is room to increase the market share of the other languages.

**Recommendation:** Microsoft should prioritize investment in English language original content. However, the company should also make conservative invest,ent in French and Japanese language content in an attempt to make inroards in those unsaturated markets.
