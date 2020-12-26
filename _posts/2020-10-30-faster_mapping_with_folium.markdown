---
layout: post
title:      "Faster Mapping With Folium"
date:       2020-10-30 12:30:51 -0400
permalink:  faster_mapping_with_folium
---

<h1>Table of Contents<span class="tocSkip"></span></h1>
<div class="toc"><ul class="toc-item"><li><span><a href="#Imports,-and-getting-the-data" data-toc-modified-id="Imports,-and-getting-the-data-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Imports, and getting the data</a></span></li><li><span><a href="#Plotting-with-folium" data-toc-modified-id="Plotting-with-folium-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Plotting with folium</a></span></li><li><span><a href="#Icons" data-toc-modified-id="Icons-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>Icons</a></span></li><li><span><a href="#colors" data-toc-modified-id="colors-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>colors</a></span></li><li><span><a href="#map-tiles" data-toc-modified-id="map-tiles-5"><span class="toc-item-num">5&nbsp;&nbsp;</span>map tiles</a></span></li><li><span><a href="#size" data-toc-modified-id="size-6"><span class="toc-item-num">6&nbsp;&nbsp;</span>size</a></span></li><li><span><a href="#colors" data-toc-modified-id="colors-7"><span class="toc-item-num">7&nbsp;&nbsp;</span>colors</a></span></li><li><span><a href="#zoom_level" data-toc-modified-id="zoom_level-8"><span class="toc-item-num">8&nbsp;&nbsp;</span>zoom_level</a></span></li></ul></div>

## Imports, and getting the data
> To get started we will import the required libraries and data
> - The data is obtained from [Kaggle](https://www.kaggle.com/shivachandel/kc-house-data)


```python
#!pip install folium
import folium
import pandas as pd
import requests
df = pd.read_csv("./data/housing.csv")
```

## Plotting with folium
There are many things we can do with folium that we will be going through such as:
- icons
- colors
- map tiles
- size
- colors
- zoom_level
- and many more found in the [folium documentation](https://python-visualization.github.io/folium/)

We'll start by defining a location of where to center the map.  The dataset we are using is centered near Seattle, Washington.  Be sure and take note below when a map is created with ```map = folium.Map(location=location)```  This is building a folium map at the given location.


```python
# Define the location to start the map at
location = (47.550579, -121.981752)

# Initializing the map
map = folium.Map(location=location)
```

Next we'll plot some basic points on a map with folium.  The plot will start off at the supplied location, and we will be plotting 10 (latitude/longitudes) from df.  The Latitude and Longitude of the data is stored as `lat` and `long` you may have to use a different iteration techinque if columns are named differently. <p>   We will be using a folium.Marker on the map, this takes a location, a popup, a tooltip, and whether or not the marker is draggable.  The location is a tuple of latitude, longitude which we are getting from the dataframe iteration along with how many bedrooms our house has for the popup.  The tooltip is what is shown when you hover over the item,  we will programmatically add the given date as the tooltip.</p><p></p>After building a folium marker with `folium.Marker()` we tag on a `.add_to(map)` to add the point to our map.  This is done for each of the 10 locations we are plotting which if just using the folium Marker will take much processing power if you have thousands of points.  We will go into speeding up the process with custom icons in the next section.


```python
for indx, row in df.iterrows():
    lat = row['lat']
    long = row['long']
    bedrooms = row['bedrooms']
    date = row['date']
    
    folium.Marker(
        [lat, long],
        popup=f'<i>House with {bedrooms} bedrooms</i>',
        tooltip=f'Sold on {date}').add_to(map)
    # Stop at 10 points
    if indx == 10:
        break
map
```

<iframe
    width="900"
    height="600"
    src="./maps/first.html"
    frameborder="0"
    allowfullscreen
></iframe>





```python

```


```python

```


```python

```

## Icons




```python
map = folium.Map(location=location)

```

## colors

## map tiles

## size

## colors

## zoom_level
