---
layout: post
title:      "Initializing a Class/ Showcase \__init__ Function."
date:       2020-07-10 16:48:19 -0400
permalink:  initializing_a_class_showcase_init_function
---

<p>
    Below I start out by creating a class called Dog with an init function that prints "initializing" self.  When I instantiate it with 'a = Dog()' you can see that it runs the init function, printing ('initializing', self), although it won't be "self" due to it printing the actual object pointer.  After defining the class I print out a, which shows basically \__main__.A object at xyz </p>

<p>
    Next you will see the definition of class B with an init function that prints initializing self, and sets the name of itself which is taken as a variable from the init function.  b is set to B('Haron) with given name Haron, and you can see that it gets set to the 'name' variable of the object.  After it is set I can simply print b.name, and it returns "Haron"
</p>

<p> 
    I could also do something like `c =B(name='xyz')`  to set the name of it. </p>


# Code:
<pre><code>class Dog():
    def __init__(self):
        print("Initializing", self)
        
a = Dog()  # initialized by setting a to an instance of Dog
print(a)


class B():
    def __init__(self, name):
        """Note how name /\ is here
        and name \/ is set to self.name"""
        self.name = name
        print("Initializing", self)

b = B('Haron')  # initializing an instance of B with name variable


print("\nName of instance b is", b.name)  # printing the instances name


print("\n It can also be initialized and name printed by simply calling like so -------  ", B("new").name)
</code></pre>

# Output:

<pre><code>Initializing <__main__.A object at 0x7f30a0356310>
<__main__.A object at 0x7f30a0356310>


Initializing <__main__.B object at 0x7f30a0356410>
Name of instance b is Haron
Initializing <__main__.B object at 0x7f30a0356450>
 It can also be initialized by simply calling like so -------   new
</code></pre>

# Use Case:

<p> This shows in the matplotlib module where you instantiate a figure by calling the below where figsize is accepted in the init function of class Figure.
<pre><code>import matplotlib.pyplot as plt
figure = plt.figure(figsize=(6, 8))</code></pre>


Which per matplotlib documentation, instantiates a Figure.

<pre><code>:mod:`matplotlib.figure`
        defines the :class:`~matplotlib.figure.Figure` class.
</code></pre>
</p>
