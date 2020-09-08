---
layout: post
title:      "Styling a Jupyter Notebook"
date:       2020-09-07 23:47:07 -0400
permalink:  styling_a_jupyter_notebook
---

Here I will go over multiple methods of using css and html to style and prettify your jupyter notebook like my photo below. For use in any html/css scenario I will be going over:
- alert boxes
- editing attributes in VSCode
- css of headers
- css of blockquotes
- adding a shadow to an alert box
- using markdown in a &lt;div&gt; block

## Before:

![](https://i.imgur.com/kdCXHBM.png)

## After:

![](https://i.imgur.com/OGwzciY.png)


<br>

# Alert Boxes:
> These are basic html, that you can simply copy and paste into a markdown block of a Jupyter Notebook
> - Select desired markdown cell.
> - `ctrl+m`
> - try pasting the code below.

<div class="alert alert-info">
  <strong>Let's start with THIS blue box:</strong>
<pre><code>&lt;div class=&quot;alert alert-info&quot;&gt;
  &lt;strong&gt;Let's start with THIS blue box:&lt;/strong&gt;
&lt;/div&gt;</code></pre>
<ul>
<li>
class="alert alert-success"  for <p class="alert alert-success">green</p>
</li>
<li>
class="alert alert-warning"  for <p class="alert alert-warning">yellow, indicating a warning, or some findings that could be neutral->negative</p>
</li>
<li>
class="alert alert-danger"  for <p class="alert alert-danger">red, indicating something is bad or "dangerous", negative findings.</p>
</li>
<li>
class="alert alert-info"  for <p class="alert alert-info">blue</p>
</li>
</ul>
</div>

<br>

# Adding css into a notebook:
> css is the different shadows, colors, and titles you see in the After: screenshot above

```
The folder structure you would use for css.

+--student.ipynb
|
|
+--styles
|  |
|  |
|  +--custom.css
```

If you want to import the css from that folder structure into your notebook you would use the below in a code cell.

```
# Styling notebook
from IPython.core.display import HTML
def css_styling():
    styles = open("./styles/custom.css", "r").read()
    return HTML(styles)
css_styling()
```

<div class="alert alert-info">
  <strong>Inside of the custom.css file you can customize things like:</strong>
<ul>
<li>Headers</li>
<li>Codeblocks</li>
<li>Bolded text</li>
<li>*alerts*</li>
<li>blockquotes</li>
<li>listelements</li>
<li> And many more...</li>
</ul> 
</div>
<br>

# Custom Headers:
> A header is ^  exactly like that,  there are 6 different header types ( h1-h6 )    defined as (## Header) in markdown with the number of # symbols being significant.

<div class="alert alert-info">
  <strong>Lets take an h1 header as example:</strong>
<br>
In html<pre><code>&lt;h1&gt; Title &lt;/h1&gt;    
</code></pre>

Or in markdown
<pre><code># Title</code></pre>
</div>

**Both showing up as:**

<h1> Title </h1>


<div class="alert alert-success">
  <strong>If I wanted to style the h1 header I would have in the .css file:</strong>
<pre><code>&lt;style&gt;
h1 {
  border: 1.5px solid #333;
  padding: 8px 12px;
  background-image: linear-gradient(180deg, #fff, rgb(160, 147, 147));
  position: static;
}
&lt;/style&gt;
</code></pre>


</div>

> Which would show **all** of the h1 headers as:

![](https://i.imgur.com/JTXjjGY.png)



<div class="alert alert-warning">
  <strong>An HTML/CSS developing environment:</strong>
<ul>
<li><a href="https://code.visualstudio.com/">Visual Studio Code</a></li>
<ul>
<li>HTML Preview extension by Thomas Haakon Townsend</li>
<li>A .html file where you can test the styles</li>

</ul><li>For testing with markdown <a href="https://markdown-it.github.io/">markdown-it</a> Which I am using right now</li>
</ul>
</div>
<br>

# Developing css in Visual Studio Code:

## Color:
> Hovering over any color opens a color selector window, and will automatically fill in the required code for the color you pick.

![](https://i.imgur.com/BqfIBto.png)

## Styles:
> Starting to type inside of a block will give you different suggestions of styling

![](https://i.imgur.com/giAkITe.png)
<div class="alert alert-info">
  <strong>Searching on the web for your specific needs can also help you find different attributes to change.</strong>
</div>

# Blockquotes:
> This is a block quote

<div class="alert alert-info">
  <strong>There are two ways to make a blockquote:</strong>
<br>
In html<pre><code>&lt;blockquote&gt;This is a blockquote&lt;/blockquote&gt;   
</code></pre>

Or in markdown
<pre><code>> This is a blockquote</code></pre>
</div>

**Both showing up as:**
> This is a blockquote

<div class="alert alert-success">
  <strong>If I wanted to style the blockquotes I would have in the .css file:</strong>
<pre><code>&lt;style&gt;
blockquote {
  border: 1px groove gray;
  background-color: rgb(219, 214, 214);
}
&lt;/style&gt;
</code></pre>
</div>

> Which would show **all** blockquotes as:

![](https://i.imgur.com/gY2rt86.png)

<br>

# Adding the shadow to alert boxes:
> This is a little more complicated, as we will be "wrapping" the alert box with a shadow.

If I wanted to give a shadow to alert for jupyter notebook I would have in my css file:
<pre><code>Quick note: /* This is a css comment */

&lt;style&gt;
.shadow {

    /*Edit or add new attributes, change size, color, etc */
    width: 45em;
    box-shadow: 8px 8px 10px #444;
    border: 1px solid silver;

    /*For positioning in a jupyter notebook*/
    margin-top: 2em;
    position: relative;
    top: -25px
}
&lt;/style&gt;
</code></pre>
And I would display the alert box in the html/markdown as
<pre><code>&lt;div class=&quot;shadow alert alert-success&quot;&gt;  /*Note the 'shadow' keyword*/
  This is an alert box
&lt;/div&gt;
</code></pre>

<div class="alert alert-info" markdown="1">

**Quick note:** If you want to use markdown inside of an alert or any &lt;div&gt; block, you can simply include markdown="1" in the class:

<pre><code>&lt;div class=&quot;alert alert-success&quot; markdown=&quot;1&quot;&gt;

&gt; This is a blockquote
&gt; - And a list
&gt;     - And a nested element with a **bold**

&lt;/div&gt;
</code></pre>
Which would look like:
</div>
<div class="alert alert-success" markdown="1">

> This is a blockquote
> - And a list
>     - And a nested element with a **bold**

</div>

<br>

# In Conclusion:
> We only looked at a few ways you can edit the css of a notebook or webpage, but there are many more markdown and html attributes you can change with css ...  **EXPLORE!**

<br>
# Useful Links:

- <a href="https://github.com/skelouse">My github</a>
- <a href="http://www.csszengarden.com/">CSS zen garden</a>: For css examples, inspect the page
- <a href="https://lmgtfy.com/?q=css+help">The greatest source of css help on the planet!</a>
- Inspect this page or elements on any webpage!

<style>
a {
  color: rgb(6,69,173);
}
</style>
