# more-Data-Wrangling-Answers
import pandas as pd
import numpy as np

import json
from pandas.io.json import json_normalize

# further populate tables created from nested element
json_normalize(data, 'counties', ['state', 'shortname', ['info', 'governor']])

# load json as string
json.load((open('data/world_bank_projects_less.json')))

# load as Pandas dataframe
sample_json_df = pd.read_json('data/world_bank_projects_less.json')
sample_json_df


JSON exercise
Using data in file 'data/world_bank_projects.json' and the techniques demonstrated above,

Find the 10 countries with most projects
Find the top 10 major project themes (using column 'mjtheme_namecode')
In 2. above you will notice that some entries have only the code and the name is missing. Create a dataframe with the missing names filled in.

json.load((open('data/world_bank_projects.json')))

df = pd.read_json("data/world_bank_projects.json")

# The answer to the first question
country = df["countryname"].value_counts(ascending=False)
country.head(10)

# The answer to the second question
df['mjtheme'] = df['mjtheme'].astype(str).str.replace('[', '').str.replace(']', '')

ff = df['mjtheme'].str.split(",", expand=True).stack().reset_index(level=1, drop=True).rename('word')

word_counts = ff.value_counts()
word_counts.head(10)

#The start of question 3

bob = df[["mjtheme", "mjthemecode"]]
pog = bob.dropna()
pog.head(40)

#count of number of mjthemecode values in row
pog["mjthemecode"] = pog["mjthemecode"].apply(lambda x: [int(i) for i in x.split(",")])

def dod(x):
    return len(x)

s = pog.mjthemecode.apply(dod)
s

# count of number of mjtheme values in each row
def counter(item):
    return len(item)

sa = pog.mjtheme.apply(counter)
sa

# This will now only have rows that have the correctly indexed names to codes.
# From this we can create a dict with code name pairs

filtered = pog.drop(pog[s!=sa].index)

#This creates a dict that has the key value pairs of the code with the name of the project.
dic = {}

def dic_create(x, y, dic):
    for i in range(len(x)):
        dic[x[i]] = y[i]

filtered.apply(lambda row: dic_create(row['mjthemecode'], row['mjtheme'], dic), axis=1)

df["mjthemecode"]=df["mjthemecode"].apply(lambda x: [int(i) for i in x.split(",")])


#The answer to question 3

# Here we will iterate through the lists in the original df's column "mjthemecode" to create a new column that 
# has all the mentioned codes in the form of coulmns
def connector(x, dic):
    l = []
    for i in x:
        l.append(dic[int(i)])
    return l
df["all_projects"] = df.apply(lambda row: connector(row["mjthemecode"], dic), axis=1)
df
