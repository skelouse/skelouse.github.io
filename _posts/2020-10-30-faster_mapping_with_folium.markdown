---
layout: post
title:      "Faster Mapping With Folium"
date:       2020-10-30 12:30:51 -0400
permalink:  faster_mapping_with_folium
image: 
---
<head>
<meta name="image" property="og:image"
          content="https://i.imgur.com/JSO3JL4.png">
<meta name="author"
        content="Samuel Stoltenberg">
</head>

<p>Here we showcase different ways of creating a map with folium, and different attributes of markers and circles. There are many ways to build a map, so don't be afraid to dig into documentation or message me on Linkedin if you need something specific.</p>
<p>When a map is created you can either simply view it in a jupyter notebook, or extract it with
<code>map.save("/path/to/map.html")</code>.  I've saved each of the below maps, and put them in iframes pointing to each map.html file in the
<a href="https://github.com/skelouse/skelouse.github.io/tree/master/maps">maps folder</a> of my blog on github.</p>


## Imports, and getting the data
> To get started we will import the required libraries and data
> - The data is obtained from [Kaggle](https://www.kaggle.com/shivachandel/kc-house-data)


```python
#  pip install folium

import folium
import pandas as pd
import requests
df = pd.read_csv("./data/housing.csv")
```

## Plotting with folium
There are many things we can do with folium that we will be going through such as:
- points
- colors
- map tiles
- size
- opacity
- zoom_level
- and many more found in the [folium documentation](https://python-visualization.github.io/folium/)

We'll start by defining a location of where to center the map.  The dataset we are using is centered near Seattle, Washington.  Be sure and take note below when a map is created with ```map = folium.Map(location=location)```  This is building a folium map at the given location.


```python
# Define the location to start the map at

location = (47.550579, -121.981752)

# Initializing the map

map = folium.Map(location=location)
```

Next we'll plot some basic points on a map with folium.  The plot will start off at the supplied location, and we will be plotting 10 (latitude/longitudes) from df.  The Latitude and Longitude of the data is stored as `lat` and `long` you may have to use a different iteration technique if columns are named differently.
<p>We will be using a folium.Marker on the map, this takes a location, a popup, a tooltip, and whether or not the marker is draggable.  The location is a tuple of latitude, longitude which we are getting from the dataframe iteration along with how many bedrooms our house has for the popup.  The tooltip is what is shown when you hover over the item,  we will programmatically add the given date as the tooltip.</p>
<p>After building a folium marker with
<code>folium.Marker()`</code> we tag on a 
<code>.add_to(map)`</code> to add the point to our map.  This is done for each of the 10 locations we are plotting which if just using the folium Marker will take much processing power if you have thousands of points.  We will go into speeding up the process with custom Points in the next section.</p>


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

# Function I used for displaying it on this blog

# map.save("./maps/first.html")

map
```

<iframe
    width="900"
    height="600"
    src="./maps/first.html"
    frameborder="0"
    allowfullscreen
></iframe>

## Points

<p>Why do you want a point?  A point is much faster than a marker, as it doesn't highlight when you hover over it, and is simply a circle drawn on the map versus with a Marker there are many other attributes attached to it.  It would take several minutes or hours to load a map of several thousand Markers versus seconds with several thousand Circles.</p>
<p>Here we will touch on the Circle point from folium, there are different points such as a Rectangle, Polygon and more.  This is simply touching on the customization of points for speed purposes.  There are many attributes of a point we can define, such as:
<ul>
    <li>radius</li>
    <li>color</li>
    <li>weight</li>
    <li>opacity</li>
</ul> 
</p>

Each of the above can take a set variable, or you can program them however you like for each of the data points you have a location for.




### Radius

We will start with the radius,  the radius is small, so we will have `1000 * bedrooms` to showcase the radius.  We keep the same style as before except now we are using a `folium.Circle()` rather than the `folium.Marker()`


```python
map = folium.Map(location=location)
for indx, row in df.iterrows():
    lat = row['lat']
    long = row['long']
    bedrooms = row['bedrooms']
    
    folium.Circle(
        [lat, long],
        radius=1000*bedrooms).add_to(map)
    # Stop at 10 points

    if indx == 10:
        break
map
```

<iframe
    width="900"
    height="600"
    src="./maps/radius.html"
    frameborder="0"
    allowfullscreen
></iframe>

## colors

Next we are going to go over the circle color.  You can either set a color for all the circles to be, make different colors for different arguments, or have a gradient of colors.  I will be showing the gradient of colors below.  To generate a gradient we will be using the colour module, which is installed with `pip install colour`.  After importing color we will look at our grade feature, as that is what we will be using for the color.  We need the maximum of that feature, so we can deduce how many colors our color gradient needs.


```python
from colour import Color
```


```python
df['grade'].max(), df['grade'].min()
```




    (13, 3)




```python
colors = list(Color('red').range_to(Color('green'), 13))
len(colors), colors
```




    (13,
     [<Color red>,
      <Color #f42900>,
      <Color #ea4e00>,
      <Color #df7000>,
      <Color #d58e00>,
      <Color #caa800>,
      <Color #bfbf00>,
      <Color #97b500>,
      <Color #72aa00>,
      <Color #50a000>,
      <Color #329500>,
      <Color #178b00>,
      <Color green>])



The colors are objects of Color, so we will use a list comprehension to make the colors simple strings to be used by folium.  Each color has a `.get_web()` method for extracting the raw string.


```python
colors = [color.get_web() for color in colors]
colors
```




    ['red',
     '#f42900',
     '#ea4e00',
     '#df7000',
     '#d58e00',
     '#caa800',
     '#bfbf00',
     '#97b500',
     '#72aa00',
     '#50a000',
     '#329500',
     '#178b00',
     'green']



There are 13 different grades, so we made a range of 13 colors from red to green.  As a list starts with 0 we will be using `grade-1` to define which color we will use for our circle.  After defining the color we can use it for the circle as before with the radius.


```python
map = folium.Map(location=location)
for indx, row in df.iterrows():
    lat = row['lat']
    long = row['long']
    grade = row['grade']
    
    # Define what color the circle will be
    
    color = colors[grade-1]
    
    folium.Circle(
        [lat, long],
        radius=1000,
        weight=50,
        color=color).add_to(map)
    # Stop at 10 points

    if indx == 10:
        break
map
```

<iframe
    width="900"
    height="600"
    src="./maps/color.html"
    frameborder="0"
    allowfullscreen
></iframe>

## weight
For the weight we will be using the price feature.  Price is quite large relative to the map, so we will be dividing the price by 10,000.  Just like before with the grade and color weight is an argument of the folium.Circle().


```python
map = folium.Map(location=location)
for indx, row in df.iterrows():
    lat = row['lat']
    long = row['long']
    price = row['price']
    
    folium.Circle(
        [lat, long],
        radius=1,
        weight=price/10000).add_to(map)
    # Stop at 10 points

    if indx == 10:
        break
map
```

<iframe
    width="900"
    height="600"
    src="./maps/weight.html"
    frameborder="0"
    allowfullscreen
></iframe>

## opacity

Opacity is a float argument of the Circle between 0 and 1 defining how transparent an object is.  Since it is between 0 and 1 we will first import MinMaxScaler from scikit-learn, and then scale the `yr_built` feature.  After the feature is scaled we can use it for the opacity argument.  The scaler requires a 2D array, thus when extracting and defining `yr_built` we use [[double brackets]] to extract a 2D array rather than a 1D Series with [single brackets].


```python
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()

df[['yr_built']] = scaler.fit_transform(df[['yr_built']])
```

Let's build it!  After using the scaler our yr_built feature is now all values between 0 and 1 with the previous max being 1 and the previous min being 0.  I first tried using sqft_living and bedrooms as the opacity, but the first ten homes of the dataset were almost completely transparent!


```python
map = folium.Map(location=location)
for indx, row in df.iterrows():
    lat = row['lat']
    long = row['long']
    year = row['yr_built']
    
    folium.Circle(
        [lat, long],
        radius=1000,
        opacity=year).add_to(map)
    # Stop at 10 points

    if indx == 10:
        break
map
```

<iframe
    width="900"
    height="600"
    src="./maps/opacity.html"
    frameborder="0"
    allowfullscreen
></iframe>

## zoom_level

The zoom level is defined by four attributes.
- max_zoom the maximum level you can zoom into the map
- min_zoom is how far you can zoom out from the map
- zoom_start is 
- zoom_control is whether a user can zoom in and out of the map, or if they are stuck at the defined zoom_start

The zoom level is values between 0 and 18, 0 being a view of the whole globe multiple times, and 18 being close enough to read side streets.  We'll show zoomed all the way in first, and then show zoomed all the way out


```python
map = folium.Map(location=location, zoom_start=18)
map
```

<iframe
    width="900"
    height="600"
    src="./maps/zoom_start.html"
    frameborder="0"
    allowfullscreen
></iframe>


```python
map = folium.Map(location=location, zoom_start=0)
map
```

<iframe
    width="900"
    height="600"
    src="./maps/zoom_end.html"
    frameborder="0"
    allowfullscreen
></iframe>

## map tiles

There are ten tile patterns within folium, six of which are free.   We will be showcasing the free tiles, but here is a list of all the tiles obtained from [folium](https://python-visualization.github.io/folium/modules.html).

 - OpenStreetMap
 - Mapbox Bright (Limited levels of zoom for free tiles)
 - Mapbox Control Room (Limited levels of zoom for free tiles)
 - Stamen (Terrain, Toner, and Watercolor)
 - Cloudmade (Must pass API key)
 - Mapbox (Must pass API key)
 - CartoDB (positron and dark_matter)
 
We won't be touching on the `OpenStreetMap` as that is the default you have seen in our maps before.  We will start off with Stamen Terrain.  When creating the map it takes a `tiles` argument where we will feed the string "Stamen Terrain".  After that we will show Stamen (Toner and Watercolor), and CartoDB (positron and dark_matter).

#### Stamen Terrain


```python
map = folium.Map(
    location=location,
    tiles='Stamen Terrain')
map
```

<iframe
    width="900"
    height="600"
    src="./maps/s-terrain.html"
    frameborder="0"
    allowfullscreen
></iframe>

#### Stamen Toner


```python
map = folium.Map(
    location=location,
    tiles='Stamen Toner')
map
```

<iframe
    width="900"
    height="600"
    src="./maps/s-toner.html"
    frameborder="0"
    allowfullscreen
></iframe>

#### Stamen Watercolor


```python
map = folium.Map(
    location=location,
    tiles='Stamen Watercolor')
map
```

<iframe
    width="900"
    height="600"
    src="./maps/s-watercolor.html"
    frameborder="0"
    allowfullscreen
></iframe>

#### CartoDB positron


```python
map = folium.Map(
    location=location,
    tiles='CartoDB positron')
map
```

<iframe
    width="900"
    height="600"
    src="./maps/c-positron.html"
    frameborder="0"
    allowfullscreen
></iframe>

#### CartoDB dark_matter


```python
map = folium.Map(
    location=location,
    tiles='CartoDB dark_matter')
map
```

<iframe
    width="900"
    height="600"
    src="./maps/c-dark-matter.html"
    frameborder="0"
    allowfullscreen
></iframe>

To end I will point you to the [folium documentation](https://python-visualization.github.io/folium/index.html) read through it and make your own map!  Share to my [linkedin](https://www.linkedin.com/in/samuel-stoltenberg-0b799bb5/) what you come up with.  Download the data set we used from [here](https://github.com/skelouse/mod-2-project/blob/master/kc_house_data.csv).
