---
layout: post
title:      "Scraping Data As a Group"
date:       2020-07-31 13:10:08 -0400
permalink:  parallel_data_scraping
---

The teacher supplies a project, and the students complete the project.  What if that data involves web scraping?  Well I had the grand idea of getting the entire class in on scraping the budgets and grosses of 700,000 movies to match the 1-2 million movie datasets i downloaded from [IMDB](https://datasets.imdbws.com/).  So I went on my merry way and split the IMDB indexes I had into 10 parts, each with the name of a person.

### Creating the indexes to be split as a file `qlist.txt`
```
import pandasql as ps

# Here I used a sql query to get my base indices I needed to split the workload across the group
q1 = """
SELECT tconst FROM title_ratings
JOIN title_basics
USING(tconst)
WHERE startYear > 2009
AND (titleType = 'movie' OR titleType = 'tvMovie')
"""

# Here I have a qlist, and for each indice of the query I am adding it to qlist along with a comma.
qlist = ''
x = 0
df = ps.sqldf(q1, locals())
for i in df['tconst']:
    x+=1
    qlist += str(i+',')

# I save qlist to a file `qlist.txt` and move on to splitting it up
with open('qlist.txt', 'w') as f:
    f.write(qlist)
```

### Splitting the `qlist.txt` into separate file names for each person
```
import json
import math

# Open the qlist.txt I created before
with open('./qlist.txt', 'r') as f:
    q_list = f.read().split(',')


num_people_scraping = 10
# Splitting the qlist by number of people
num = len(math.ceil(q_list/num_people_scraping))

x = 0
done = False
while not done:
    x += 1
    person_list = ''
    for i in range(num):
        try:
            person_list += str(q_list.pop(0)) + ','
        except IndexError:
            done = True
            break
	# Saving each person's workload to a separate num.txt
    with open('./nums%s.txt'%x, 'w') as f:
        f.write(person_list)
				
# In hindsight I could have combined the two above codes
with a list of people participating, and have it automatically
save to their given name's file rather then changing the names myself.
```


### To each person I sent the below along with their `name.txt` file
```
import re
import json
import requests
from bs4 import BeautifulSoup

# They simply had to change the name here to their name, and run this .py file
file_name = 'name.txt'
data = {}

def extract_numbers(string):
    try:
        return int("".join(re.findall(r'\b\d+\b', string)))
    except ValueError:
        return None


def find(soup, q_num):
    budget = None
    gross = None
    ww_gross = None
    rating = None
    # Find the rating
    for div in soup.findAll('div', class_='subtext'):
        rating = (div.text.split('\n')[1].replace(' ', '').replace('\n', ''))

    # Find the budget, gross, ww_gross in page
    for h4 in soup.findAll('h4'):
        if h4.text.startswith('Budget'):
            text = h4.parent.text
            text = text.split(' ')[0]
            budget = extract_numbers(text)
            
        elif h4.text.startswith('Gross USA'):
            text = h4.parent.text
            text = text.split(' ')[2]
            gross = extract_numbers(text)
            
        elif h4.text.startswith('Cumulative Worldwide'):
            text = h4.parent.text
            text = text.split(' ')[3]
            ww_gross = extract_numbers(text)
    # If any, update the data dict() with the new data
    if budget or gross or ww_gross or rating:
        new_data = {
            q_num:{
                'budget': budget,
                'gross': gross,
                'ww_gross': ww_gross,
                'rating': rating
                }
            }
        data.update(new_data)

url = "https://www.imdb.com/title/"
def get_soup(q_num):
    req = requests.get(str(url+q_num))
    return BeautifulSoup(req.content.decode(), 'html.parser')

# Here is where the dictionary is saved to a data.json file
def save(data):
    with open('data.json', 'w') as f:
        json.dump(data, f)

with open(file_name, 'r') as f:
    q_list = f.read().split(',')

x = 0
# Iterating through the list I created for them
for num in q_list:
    x += 1
    if x == 100:
        x = 0
		# The dictionary is saved every 100 queries so if it gets stuck,
		# or the computer crashes/goes to sleep the data is still available.
        save(data)
    print('analysing', num)
    find(get_soup(num), num)

save(data)
```

<p>   We did actually scrape all 700,000+ , but oh boy was the data a mess.  For one, the ratings also had genres tied into that same tag.  Secondly the amount of null values was immense, some movies had gross but not budget, some had world wide gross but not gross, some had none.  Lastly some countries had the budget stored in a different currency.  So I ended up doing another query, based from the indices we had already parsed which meant a LOT less data.  I think we ended up with a little short of 100,000 queries which could easily be split between myself and one other classmate.  I ended up pulling the conversions of different currencies and feeding that into the scraper in a way it could distinguish, and end up with all budgets converted to USD.  Here was the change to the above.</p>

```
def extract_budget(string):
    try:
        return int("".join(re.findall(r'\b\d+\b', string)))
    except ValueError:
        return None

def extract_numbers(string):
    try:
        return int("".join(re.findall(r'\b\d+\b', string)))
    except ValueError:
        return None

# Currency conversions as of 8/1/2020
usd_from_GBP = 1.31
usd_from_NOK = 0.11
usd_from_DKK = 0.16
usd_from_AUD = 0.71
usd_from_EUR = 1.18
usd_from_MXN = 0.045

def find(soup, q_num):
    budget = None
    gross = None
    ww_gross = None
    rating = None
    # Find the rating
    for div in soup.findAll('div', class_='subtext'):
        rating = (div.text.split('\n')[1].replace(' ', '').replace('\n', ''))
    # Find the budget, gross, ww_gross in page
    for h4 in soup.findAll('h4'):
        if h4.text.startswith('Budget'):
            text = h4.parent.text
            # Split by currency
            if 'GBP' in text:
                text = text.split(' ')[0].replace('GBP', '')
                budget = int(extract_budget(text)*usd_from_GBP)
            elif 'NOK' in text:
                text = text.split(' ')[0].replace('NOK', '')
                budget = int(extract_budget(text)*usd_from_NOK)
            elif 'DKK' in text:
                text = text.split(' ')[0].replace('DKK', '')
                budget = int(extract_budget(text)*usd_from_DKK)
            elif 'AUD' in text:
                text = text.split(' ')[0].replace('AUD', '')
                budget = int(extract_budget(text)*usd_from_AUD)
            elif 'EUR' in text:
                text = text.split(' ')[0].replace('EUR', '')
                budget = int(extract_budget(text)*usd_from_EUR)
            elif 'MXN' in text:
                text = text.split(' ')[0].replace('MXN', '')
                budget = int(extract_budget(text)*usd_from_MXN)
            elif '$' in text:
                text = text.split(' ')[0]
                budget = extract_budget(text)
            else:
                print('failed', text)
            
        elif h4.text.startswith('Gross USA'):
            # Get the domestic gross
            text = h4.parent.text
            text = text.split(' ')[2]
            gross = extract_numbers(text)
            
        elif h4.text.startswith('Cumulative Worldwide'):
            # Get the Worldwide gross
            text = h4.parent.text
            text = text.split(' ')[3]
            ww_gross = extract_numbers(text)
    if budget or gross or ww_gross or rating:
        # Combine into dictionary
        new_data = {
            q_num:{
                'budget': budget,
                'gross': gross,
                'ww_gross': ww_gross,
                'rating': rating
                }
            }
        # Update main dictionary with data
        data.update(new_data)
```

### Splitting the data mining with one other person made it easier to gather it all, and here is how I combined the datasets.  I would simply place each .json file I was sent on Slack into one folder and run the below.

```
import os
import json
import pandas as pd
folder_name = 'all_data'  # The folder where the `.json` files were located
file_list = os.listdir(folder_name)  # A list of files in said folder

data = {}  # Where I am updating all of the stored files to

for filename in file_list:
    with open((folder_name +'/'+filename), 'r') as f:
        data.update(json.load(f))

# transposing my sideways column and reseting the index to reflect the other dataframes
df = pd.DataFrame.from_dict(data)
df = df.transpose()
df = df.reset_index()
df = df.rename(columns={'index': 'tconst'})

# Save DataFrame to csv
df.to_csv('scraped_data/budget_ratings.csv')
```

Make sure to thoroughly test your files before sending them around for group efforts.
