---
layout: post
title:      "Matplotlib Tips and Tricks"
date:       2021-01-01 00:00:00 -0800
image:      https://i.imgur.com/EtQXsCz.png
permalink:  matplotlib_tips_and_tricks
---

Here we'll be looking at a python library matplotlib which is used for plotting data in various ways.  We'll be detailing things such as iterating subplots, different grid specs, and complex labeling.  We will not be covering all of the different types of plots that can be done, those can be found in the documentation [here](https://matplotlib.org/3.3.3/tutorials/introductory/sample_plots.html).

## Import matplotlib


```python
import matplotlib.pyplot as plt
```

## Plot Sizing

The plot size can be defined in `plt.subplots()` as a keyword argument `figsize=(X, y)`.  The ax variable created will be used later.


```python
fig, ax = plt.subplots(figsize=(10, 7))
```

![fig_1](./data/figures/figure_1.png)


```python
fig, ax = plt.subplots(figsize=(1, .7))
```

![fig_2](./data/figures/figure_2.png/)

### Figure With Two Rows

We can also define a grid of different plots with `plt.subplots()` containing `nrows`, and `ncols` as keyword arguments.  Notice how I change the `ax` variable to `axes` this is due to there now being multiple plots, more on that later.


```python
fig, axes = plt.subplots(nrows=2)
```

![fig_3](./data/figures/figure_3.png/)

### Figure With Two Columns

Now we plot a figure with two columns.


```python
fig, axes = plt.subplots(ncols=2)
```

![fig_4](./data/figures/figure_4.png/)

## Basic Plots

Now after showing how to create subplots we will look at plotting lines on an individual plot, and then we will move on to plotting on separate axes.  There are many different plots that can be done with matplotlib, here are just a few examples before we dive into different methods.  We'll start by plotting a simple line.


```python
X = list(range(0, 20))
y = X

plt.plot(X, y)
```

![fig_5](./data/figures/figure_5.png/)

### Ax Lines

Now we'll take our line and add some ax lines on the x and y axis which are vertical and horizontal lines respectively.  You'll also notice a color argument, without it the lines would all be blue. `C0 -> C9` are the default colors of matplotlib.  When plotting with structured data matplotlib will sometimes use those default colors automatically.


```python
plt.plot(X, y)

plt.axhline(10, color='C1')
plt.axvline(15, color='C2')
```

![fig_6](./data/figures/figure_6.png/)

### Filling Area

If we wanted to fill the center triangle created we can input x and y points of our polygon before calling plt.fill().


```python
plt.plot(X, y)

plt.axhline(10, color='C1')
plt.axvline(15, color='C2')

plt.fill([10, 15, 15], [10, 15, 10], color='C3')
```

![fig_7](./data/figures/figure_7.png/)

### Annotation

Now let's label the triangle as if it were something important.


```python
plt.plot(X, y)

plt.axhline(10, color='C1')
plt.axvline(15, color='C2')

plt.fill([10, 15, 15], [10, 15, 10], color='C3')

plt.annotate("A red triangle", (10, 15))
```

![fig_8](./data/figures/figure_8.png/)

### The Basic Color Scheme

Before moving back into the axes we will look at the default colors from matplotlib.  I will simply iterate 0-9 and plot vertical lines at each x coordinate.


```python
for x in range(10):
    plt.axvline(x, color=f"C{x}")
```

![fig_9](./data/figures/figure_9.png/)

## Plotting On Different Axes

Now we'll start plotting on multiple axes.  When `plt.subplots()` is called, two variables figure and axis are returned.  The `figure` is "The top level container for all the plot elements." (source: [Figure documentation](https://matplotlib.org/3.3.3/api/_as_gen/matplotlib.pyplot.figure.html))  An `axis` of the axes is the actual subplot or plot returned.  For example with `plt.subplots()` a single ax is returned.  With `plt.subplots(nrows=2)`, ax becomes a list of two elements being the top and bottom axis.


```python
fig, axes = plt.subplots(nrows=2)
```

![fig_10](./data/figures/figure_10.png/)


```python
axes
```




    OUTPUT: (array([<AxesSubplot:>, <AxesSubplot:>], dtype=object), 2)



### Grid Axes

To complicate things if we were to say there are two rows and two columns the length of axis would still be 2 as each row is an element in the list with elements pertaining to left and right.  We could also say axes is a two dimensional array, with rows being the first dimension, and columns being the second.


```python
fig, axes = plt.subplots(nrows=2, ncols=2)
```

![fig_11](./data/figures/figure_11.png/)


```python
axes
```




    OUTPUT: array([[<AxesSubplot:>, <AxesSubplot:>],
           [<AxesSubplot:>, <AxesSubplot:>]], dtype=object)




```python
len(axes)
```




    OUTPUT: 2



### Iteratively Labeling a Grid

`axes[0]` is the first row, and `axes[1]` is the second.  You may want to keep it this way if you are plotting similar data on each row, or you may call `axes.flatten()` to return a list going left to right -> top to bottom like so:


```python
fig, axes = plt.subplots(nrows=2, ncols=2)
axes = axes.flatten()

names = ['top_left', 'top_right', 'bottom left', 'bottom_right']

for ax, name in zip(axes, names):
    ax.set_title(name)
```

![fig_12](./data/figures/figure_12.png/)

### tight_layout

Notice how the Titles we set for the bottom two axes are overlapping the axis labels.  To fix this we can call `fig.tight_layout()` which will make sure there is no overlap.


```python
fig, axes = plt.subplots(nrows=2, ncols=2)
axes = axes.flatten()

# Tight Layout
fig.tight_layout()

names = ['top_left', 'top_right', 'bottom left', 'bottom_right']

for ax, name in zip(axes, names):
    ax.set_title(name)
```

![fig_13](./data/figures/figure_13.png/)

## Plotting Data

Now that you've seen how to plot and how to define a grid of axes let's plot!  We will start by importing a dataset with searborn.


```python
from seaborn import load_dataset
df = load_dataset("titanic")
df.head()
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
      <th>survived</th>
      <th>pclass</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>fare</th>
      <th>embarked</th>
      <th>class</th>
      <th>who</th>
      <th>adult_male</th>
      <th>deck</th>
      <th>embark_town</th>
      <th>alive</th>
      <th>alone</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>7.2500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>71.2833</td>
      <td>C</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Cherbourg</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>7.9250</td>
      <td>S</td>
      <td>Third</td>
      <td>woman</td>
      <td>False</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>53.1000</td>
      <td>S</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>8.0500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



df is a pandas dataframe that we will use to plot data on different axes. If we want to use df as the data for plotting we simply say `ax.plot(col, data=df)` and feed in the columns we would like to plot as x and y strings for the plot method.


```python
plt.plot('fare', data=df)
```

![fig_14](./data/figures/figure_14.png/)

### Plotting On Separate Axes

Now we will define a grid of four axis, and iterate over each row and label. On each row we will plot a line and histogram.


```python
fig, axes = plt.subplots(nrows=2, ncols=2)

plot_cols = ['age', 'fare']

for col, axis_row in zip(plot_cols, axes):
    ax1 = axis_row[0]
    ax2 = axis_row[1]

    ax1.plot(col, data=df)
    ax2.hist(col, data=df)
```

![fig_15](./data/figures/figure_15.png/)

Now before showing how to flatten the axes we will plot the bar and line on the same axis.  There will only be two subplots of the following plot.


```python
fig, axes = plt.subplots(nrows=2)

plot_cols = ['age', 'fare']

for col, ax in zip(plot_cols, axes):

    ax.plot(col, data=df)
    ax.hist(col, data=df)
```

![fig_16](./data/figures/figure_16.png/)

We could also use four different columns, and plot each one on a plot.  We would do this by flattening the axes array before iterating over it.


```python
fig, axes = plt.subplots(nrows=2, ncols=2)

axes = axes.flatten()

plot_cols = ['age', 'fare', 'class', 'sex']

for col, ax in zip(plot_cols, axes):
    ax.hist(col, data=df)
```

![fig_17](./data/figures/figure_17.png/)

## Using Grid Spec

I'll start off by showing you the basic grid spec usage from matplotlib's [documentation](https://matplotlib.org/3.3.3/tutorials/intermediate/gridspec.html).  After that I will open the door for iteratively changing the grid spec.



```python
# Using documentation style editing
import matplotlib.gridspec as gridspec

fig = plt.figure(constrained_layout=True)
spec = gridspec.GridSpec(ncols=2, nrows=2, figure=fig)
ax1 = fig.add_subplot(spec[0, 0])
ax2 = fig.add_subplot(spec[0, 1])
ax3 = fig.add_subplot(spec[1, :])
```

![fig_18](./data/figures/figure_18.png/)

Now if we wanted to change the right row to be one plot we would change the `ax2` spec to point to 1:0 (bottom left) and us the right side for the plot.  The difference from the previous plot being where `ax1` and `ax2` are instantiated.


```python
fig = plt.figure(constrained_layout=True)
spec = gridspec.GridSpec(ncols=2, nrows=2, figure=fig)
ax1 = fig.add_subplot(spec[0, 0])
ax2 = fig.add_subplot(spec[1, 0])
ax3 = fig.add_subplot(spec[:, 1])
```

![fig_19](./data/figures/figure_19.png/)

Note at how we are using the colon to select the entire row or column.  If you had a 5x5 grid or some other large size you may want to only select rows one and two to be a large plot in the middle as we will show here.


```python
fig = plt.figure(constrained_layout=True)
spec = gridspec.GridSpec(ncols=5, nrows=5, figure=fig)

# Iterate the rows that will not be in the large plot
single_axis = [0, 3, 4]
for num in single_axis:
    
    # Iterate over each column of the row, and plot
    for i in range(5):
        ax = fig.add_subplot(spec[num, i])
        ax.plot(X, y)

# Slice out the spec of the large plot
mid_ax = fig.add_subplot(spec[1:3, :])
mid_ax.plot(X, y, color='C1')
```

![fig_20](./data/figures/figure_20.png/)

### Iteratively Creating a Grid

Now what if you wanted 3 rows, but only five plots where the final row was a single plot.  First we create a 3x3 plot, and then remove the grid_spec already attached to the bottom axis.  If we did not remove the axis we would be adding a plot onto other plots, and the default tick labels would show through.  Our bottom indice is two so we will define a variable to use as this bottom indice.


```python
fig, axes = plt.subplots(nrows=3, ncols=2)
fig.tight_layout()
bottom_indice = 2

gs = axes[0][0].get_gridspec()

for ax in axes[bottom_indice]:
    ax.remove()

for axes in axes[0: bottom_indice]:
    axes[0].plot(X, y)
    axes[1].plot(X, y, color='C1')

ax = fig.add_subplot(gs[bottom_indice, :])

ax.plot(X, y, color='C3')
```

![fig_21](./data/figures/figure_21.png/)

This works the same way if you have more than two columns as well.


```python
fig, axes = plt.subplots(nrows=3, ncols=3)
fig.tight_layout()
bottom_indice = 2

gs = axes[0][0].get_gridspec()

for ax in axes[bottom_indice]:
    ax.remove()

for axes in axes[0: bottom_indice]:
    axes[0].plot(X, y)
    axes[1].plot(X, y, color='C1')
    axes[2].plot(X, y, color='C2')

ax = fig.add_subplot(gs[bottom_indice, :])

ax.plot(X, y, color='C3')
```

![fig_22](./data/figures/figure_22.png/)

## Labeling

Now that we've talked over some basic methods of plotting, how do you label them?  We'll go over that next starting with a single plot's title then moving onto iteratively titling each axis.


```python
plt.scatter(x='fare', y='age', data=df)

plt.title("This is a title")
```

![fig_23](./data/figures/figure_23.png/)

Note that the methods for changing labels on an axis are different for example: `plt.title()` vs `ax.set_title()`.  I recommend becoming familiar with the axis labeling techniques as those will be used more often, and can be used for any plot, vs the `plt` commands can only be used with a single subplot.


```python
fig, ax = plt.subplots()

ax.scatter(x='fare', y='age', data=df)

ax.set_title("This is a title")
```

![fig_24](./data/figures/figure_24.png/)

There are also default parameters you can set found [Here in the documentation](https://matplotlib.org/3.3.3/tutorials/introductory/customizing.html)

Now if we wanted to label the x and y axis, we use `ax.set_xlabel()` and `ax.set_ylabel()`


```python
fig, ax = plt.subplots()

ax.scatter(x='fare', y='age', data=df)

ax.set_xlabel("Fare")
ax.set_ylabel("age")
```

![fig_25](./data/figures/figure_25.png/)

Now if we wanted to plot multiple datasets on one plot we may want to have a legend.  We do this by calling `ax.legend()` and setting the labels for each axis as we plot.  We'll start by splitting out whether or not someone survived, and then after that plot each of the survived and died datasets.


```python
# Separating survived from died
survived_df = df.loc[df['survived'] == 1]
died_df = df.loc[df['survived'] == 0]

fig, ax = plt.subplots()

# Setting an opacity variable for how transparent the points are
opacity=.2

ax.scatter(x='fare', y='pclass', data=survived_df, label='survived',
           alpha=opacity)

ax.scatter(x='fare', y='pclass', data=died_df, label='died',
           alpha=opacity)

ax.legend()
```

![fig_26](./data/figures/figure_26.png/)

There is much more to matplotlib that you can find in the [Documentation](https://matplotlib.org/3.3.3/). I also recommend checking out [seaborn](https://seaborn.pydata.org/).  Let me know on [linkedin](https://www.linkedin.com/in/samuel-stoltenberg-0b799bb5/) if you want any specific blog posts or have questions about a post!
