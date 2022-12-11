---
layout: post
title:      "Golang OOP, structs, and interfaces"
date:       2022-12-11 06:00:00 -0000
permalink:  golang_oop_structs_and_interfaces
---

This is a quick walkthrough of structs, interfaces, and some OOP concepts.  Check out the go documentation for a more thorough walkthrough on:
- [structs](https://go.dev/tour/moretypes/2)
- [interfaces](https://go.dev/tour/methods/9)

Quick note: some error messages and prints may be different to a normal go execution as this is being done with jupyter notebook using [gophernotes](https://github.com/gopherdata/gophernotes).

### Structs


```go
import "fmt"

type Vertex struct {
	X int
	Y int
}

fmt.Println(Vertex{1, 2})

nil
```

    {1 2}


#### Struct method

Now if we want to create a method of a struct, we simply write a normal function with the struct as the receiver


```go
func (v Vertex) GetX() int {
    
    // Note this
    v.X += 1

    return v.X
}

v := Vertex{1, 0}

v.GetX()
v.GetX()
v.GetX()
```




    2



Notice how we got `2` from the above even though we have v.X += 1?  This is because Vertex.GetX() simple has a receiver, not a pointer receiver.  Everything after this point will be pointer receivers.


```go
// Note the *Vertex vs Vertex before
func (v *Vertex) GetX() int {
    
    // Note this
    v.X += 1

    return v.X
}

v := Vertex{1, 0}

v.GetX()
v.GetX()
v.GetX()
```




    4



There we got 4, because Vertex.X itself was changed.

### Interfaces


In the last section we created a method of Vertex named `GetX`.  In this section I'll be creating an interface which implements GetX.  After that I'll be type casting the interface back to Vertex so that I can extract X and Y.


```go
type getter interface{
    GetX() int
}

/*  variable     type      Vertex */
var vertexGetter getter = &Vertex{1, 1}

vertexGetter.GetX()
vertexGetter.GetX()
vertexGetter.GetX()
```




    4



Now `vertexGetter` only has a method `GetX`.  We can no longer access X and Y directly.


```go
vertexGetter.X
```


    repl.go:1:1: type main.getter has no field or method "X": vertexGetter.X



```go
vertex, ok := vertexGetter.(*Vertex)
if ok == false {
    fmt.Println("Type cast failed, got type(%T) expected type(%T)", vertexGetter, new(Vertex))
} else{
    fmt.Printf("Vertex X: %d, Y: %d \n", vertex.X, vertex.Y)
}
```

    Vertex X: 4, Y: 1 


That's it, we got our original type out from the interface!  I also checked ok as a best practice as without it the conversion would panic on failure.

### OOP

Some would say Golang is not an Object Oriented Programming language.  Here I'm going to show you otherwise with inheritance and method overriding.  I'm going to use the classic OOP animal example.


```go
// First we'll define our Animal Object
type Animal struct {
	name string
    sound string
}

// Then we'll defined a method `Sound` which will output the sound the animal makes
func (a *Animal) Sound() {
    fmt.Printf("%s says %s\n", a.name, a.sound)
}
```

We could simply create an animal which barks and has a name


```go
dog := &Animal{"bob", "bark"}

dog.Sound()
```

    bob says bark


Or we could create a dog struct which inherits from animal, and overrides the sound method.

#### Inheritance

Create struct `Dog` which inherits from `Animal`


```go
type Dog struct {
    *Animal
}
```


```go
// Now we can call dog.Sound

dog := &Dog{&Animal{"joey", "bark"}}

dog.Sound()
```

    joey says bark


#### Method Overriding

Now if we want to override the sound method to simply say "Bark! Bark!" we can do so


```go
func (d *Dog) Sound() {
    fmt.Println("Bark! Bark!")
}

dog.Sound()
```

    Bark! Bark!


We can also call the parent method within the overriden method


```go
func (d *Dog) Sound() {
    // Call the parent method
    d.Animal.Sound()

    fmt.Println("Bark! Bark!")
}

dog.Sound()
```

    joey says bark
    Bark! Bark!


 
