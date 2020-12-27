---
layout: post
title:      "Super, Decorators, and combined"
date:       2020-12-27 00:00:00 -0400
permalink:  super_decorators_and_combined
---


Here we'll be talking about how to use the super method, function decorators, and combining the two.  We'll start off with the super method, and how some use cases.

## Super

The super method is for calling functions of the "super" class or the parent class/object.  We'll start off with the common Child/Parent classes.

```python
class Parent():
    def display(self):
        print("I am the parent")
Parent().display()
```
OUTPUT `I am the parent`

Let's make a new class called child where we will also create a display method, and a display_parent method.  This is where super comes in.  Pay attention below to `class Child(Parent):` the child is inheriting the methods of the parent.

```python
class Child(Parent):
    def display(self):
        print("I am the child")
    
    def display_parent(self):
        super().display()
Child().display_parent()
```
OUTPUT `I am the parent`

The following code will display "I am the parent", because super is calling the parent's display function rather than the child's display function.

Now we will complicate things by making an init function that gives the Child and Parent names when they are initialized.  Now when we call the display function it will display the name as `I am the x, name`.  The parent will be called `Joe`, and the child will be called `Alfred`.


```python
class Parent():
    def __init__(self, name='Joe'):
        self.name = name

    def display(self):
        print(f"I am the parent, {self.name}")

class Child(Parent):
    def __init__(self, name='Alfred'):
        self.name = name

    def display(self):
        print(f"I am the child, {self.name}")
    
    def display_parent(self):
        super().display()
Child().display_parent()
```
OUTPUT: `I am the parent, Alfred`

It outputs I am the parent as expected but, Alfred?  The super function is equivalent to calling the parent's function with the instance of the child so the name does not magically change away from Alfred.  It would be like if we were to run the below.

```python
child = Child("Tyler")
Parent.display(child)
```
OUTPUT: `I am the parent, Tyler`

The Parent function is being called with the instance() of Child so the self in the Parent's`def display` is an instance of child.

Now what if we want to call super with arguments?  In our display_parent function we would simply use `super().display(x)` for our variable x we will now be taking in the display functions.  Coming out with:
```python
class Parent():
    def display(self, x):
        print(f"I am the parent, {x}")

class Child(Parent):
    def display(self, x):
        print(f"I am the child, {x}")
    
    def display_parent(self, x):
        super().display(x)
```
OUTPUT: `I am the parent, 10`

In my experience super can be used for many things such as:
 - Overriding a method of another class to add functionality for the child, and using the functionality of the parent method.
 - Overriding an entire class, then calling super().\_\_init__ in the overriding child's \_\_init__ function to use the Parent class as expected, but with new methods and attributes used in those new methods. `Example:`  If the Parent method returned an instance of parent you would want to wrap that function where it returns an instance of the Child instead so that the new methods and attributes are not lost.  We'll show a coding example of this later when combining super and decorators.

## Decorators

Python decorators have many use cases.  The example most developers know is to simply wrap a function.  Below we will decorate a function that prints `apples` with `-----` before and after.


```python
def decorator(func):
    def wrapper():
        print("------")
        func()
        print("------")
    return wrapper

@decorator
def print_apples():
    print("apples")

print_apples()
```

    ------
    apples
    ------
    

Now what about arguments?  If print_apples were to just be a pretty_print function that took a string argument it was supposed to print with `-----` before and after.  We could simple define a function to do it.


```python
def pretty_print(x):
    print("------")
    print(x)
    print("------")

pretty_print("Apples")
```

    ------
    Apples
    ------
    

Or we could create a decorator that could be used on several functions.  If we want the decorator to handle arguments of the function we need to add an `*args` parameter to the `def wrapper`, and call the function with the given `*args` variable.


```python
def decorator(func):
    def wrapper(*args):
        print("------")
        func(*args)
        print("------")
    return wrapper

@decorator
def pretty_print(x):
    print(x)

pretty_print("Bananas")
```

    ------
    Bananas
    ------
    

What if pretty_print takes a keyword argument along with x, let's say `times=1` where times is how many times the variable will be printed.  We would need to do the same as above, but add a \**kwargs argument to the wrapper. 


```python
def decorator(func):
    def wrapper(*args, **kwargs):
        print("------")
        func(*args, **kwargs)
        print("------")
    return wrapper

@decorator
def pretty_print(x, times=1):
    for t in range(times):
        print(x)

pretty_print("Bananas", times=3)
```

    ------
    Bananas
    Bananas
    Bananas
    ------
    

Or we could reverse it, and take times inside of the decorator.


```python
def decorator(func):
    def wrapper(*args, times=1):
        for t in range(times):
            print("------")
            func(*args)
            print("------")
    return wrapper

@decorator
def pretty_print(x):
    print(x)

pretty_print("Bananas", times=3)
```

    ------
    Bananas
    ------
    ------
    Bananas
    ------
    ------
    Bananas
    ------
    

Which as you can see prints the entire function three times including the dashes between.  We will take it one step further and have a keyword argument in our `pretty_print` function, and our `wrapper` function.  The wrapper will take the keyword argument it needs, and only give the print statement the function it needs.


```python
def decorator(func):
    def wrapper(*args, times=1, **kwargs):
        for t in range(times):
            print("------")
            func(*args, **kwargs)
            print("------")
    return wrapper

@decorator
def pretty_print(x, sort=False):
    if sort:
        print(''.join(sorted(x)))
    else:
        print(x)

pretty_print("Bananas", times=3, sort=True)
```

    ------
    Baaanns
    ------
    ------
    Baaanns
    ------
    ------
    Baaanns
    ------
    

Now as long as we use the function `print_apples` we made before as it was intended with no arguments we may use our new decorator for the same output with a new functionality of printing it x times.


```python
@decorator
def print_apples():
    print("apples")

print_apples(times=2)
```

    ------
    apples
    ------
    ------
    apples
    ------
    

## Combining

As stated in an example before we will want to combine the two for returning an instance of the Child class when using the Parent's method.  We will start off by defining a simple parent method that returns a copy of itself.  The parent class takes two numbers, x and y.  For showing its use we will also be defining a `show_id` of the parent function, that prints the id of the object.


```python
class Parent():
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
        
    def show_id(self):
        print(id(self))

    def copy(self):
        return Parent(self.x, self.y)

parent1 = Parent(5, 9)
parent2 = parent1.copy()

parent1.show_id(), parent2.show_id()
```

    1277848118216
    1277848120008
    



You can see the IDs are different for parent1 and parent2 after the copy function is ran.  What if we wanted a child that takes a z attribute, but keeps the show_id method of the parent.


```python
class Child(Parent):
    def __init__(self, x=0, y=0, z=0, parent=None):
        
        # If there is a parent the child will inherit the x and y of the parent 
        
        if parent:
            self.parent = parent
            self.x = parent.x
            self.y = parent.y
        else:
            self.parent = None
            self.x = x
            self.y = y
        self.z = z
        
child1 = Child(1, 2, 3)
print("child is type", type(child1))

child2 = child1.copy()
print("child2 is type", type(child2))
```

    child is type <class '__main__.Child'>
    child2 is type <class '__main__.Parent'>
    

You can see above from the output that the copy function is returning a Parent object rather than a child.  The Child class would also have to maintain the copy function, because without maintaining it, copy would return an instance of Parent. So to add to the above.
- We will define a copy function for the Child that uses the parents copy function while keeping the instance of child.
- We will decorate that copy function with a wrapper that keeps the z variable intact.


```python
class Child(Parent):
    def __init__(self, parent, z=0):
        self.parent = parent
        self.x = parent.x
        self.y = parent.y
        self.z = z
        
    def wrapper(func):
        def inner(self, *args, **kwargs):
            parent = func(self, *args, **kwargs)
            child = Child(parent)
            child.z = self.z
            return child
        return inner
    
    @wrapper
    def copy(self):
        return super().copy()
    
parent = Parent(1, 2)

print("Child one:")
child1 = Child(parent, 3)
print("child is type", type(child1))
child1.show_id()

print("\nChild two:")
child2 = child1.copy()
print("child2 is type", type(child2))
child2.show_id()
```

    Child one:
    child is type <class '__main__.Child'>
    1277848088136
    
    Child two:
    child2 is type <class '__main__.Child'>
    1277848086216
    

"What's the big deal?" may have crossed your mind just now.  I'll show you!  Below I will call the `show_id` method of each child's parent, and you will see that the IDs are different.  On top of that if there were complicated functions of the Parent we wanted to implement that return an instance of Parent we could now simply wrap them like our copy function!


```python
child1.parent.show_id(), child2.parent.show_id()
```

    1277848171016
    1277825302088



* One thing to note is if z was a class, list or other [mutable type](https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747) we would have to copy it as well so the children are completely separated with new parents (how morbid).

Let me know if you have any questions or blog post requests on [Linkedin](https://www.linkedin.com/in/samuel-stoltenberg-0b799bb5/)
