---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Interfaces
layout: page
description: This is detailed page having info on interfaces in golang.
link: https://botcliq.tech/golang/interfaces/
---
An interface type in Go is kind of like a definition. It defines and describes the exact methods that some other type must have.
** An interface is a collection of method signatures that a Type can implement (using methods). Hence interface defines
(not declares) the behavior of the object (of the type Type). **

## Declaring interface
Like struct, we need to create a derived type to simplify interface declaration using the keyword interface.
```txt
  type Shape interface {
      Area() float64
      Perimeter() float64
  }
```

Interfaces serve a few purposes, but the most common ones are:
* Forcing code encapsulation.
* To help reduce duplication or boilerplate code.
* As an architectural tool, to help enforce decoupling between parts of your codebase.
* To make it easier to use mocks instead of real objects in unit tests.

## Reducing boilerplate code
  We will take look into writer interface:
  ```txt
    type Writer interface {
        Write(p []byte) (n int, err error)
    }
  ```
 And bytes.Buffer and the os.File type satisfy this interface, due to them having the bytes.Buffer.Write() and os.File.Write().
 we can create the Customer.WriteJSON() method once, and we can call that method any time that we want to write to something that satisfies the io.Writer interface.
 As given in below codebase,  
```txt
package main

import (
    "bytes"
    "encoding/json"
    "io"
    "log"
    "os"
)

// Create a Customer type
type Customer struct {
    Name string
    Age  int
}

// Implement a WriteJSON method that takes an io.Writer as the parameter.
// It marshals the customer struct to JSON, and if the marshal worked
// successfully, then calls the relevant io.Writer's Write() method.
func (c *Customer) WriteJSON(w io.Writer) error {
    js, err := json.Marshal(c)
    if err != nil {
        return err
    }

    _, err = w.Write(js)
    return err
}

func main() {
    // Initialize a customer struct.
    c := &Customer{Name: "Alice", Age: 21}

    // We can then call the WriteJSON method using a buffer...
    var buf bytes.Buffer
    err := c.WriteJSON(&buf)
    if err != nil {
        log.Fatal(err)
    }

    // Or using a file.
    f, err := os.Create("/tmp/customer")
    if err != nil {
        log.Fatal(err)
    }
    defer f.Close()


    err = c.WriteJSON(f)
    if err != nil {
        log.Fatal(err)
    }
}
```

## Unit testing and mocking
  In golang you can replace one structure implementing struct with the other struct of same interface.
It can help us create structures that can help in mocking for test cases.

## Misc:
## Empty interface
In some go code some function that takes in a parameter of the type interface{} and you might start to ask yourself,
what the hell is that?
These are empty interfaces.
* The empty interface type essentially describes no methods. It has no rules. And because of that, it follows that
any and every object satisfies the empty interface.

```txt
  package main

import "fmt"


func main() {
    home := make(map[int]interface{}, 0)

    home[12] = "Ram"
    home[34] = 21
    home[35] = 167.64

    fmt.Printf("%+v", person)
 }
```
 * We need to be careful when using the empty interface, because by using the empty interface in code you are essentially 
  giving your compiler zero knowledge about the data coming in, so all of the benefits of a statically typed language go 
  out the door. The compiler no longer has the ability to tell you that you made a mistake and passed it the wrong data type.
  You are left to check for errors like that on your own, and you won’t be able to find any bugs until runtime.
 
## Type assertion
 We can find out the underlying dynamic value of an interface using the syntax i.(Type) where i is a variable of type interface
 and Type is a type that implements the interface.
 Go will check if dynamic type of i is identical to the Type and return the dynamic value is possible.
 
 ```txt
    package main

import "fmt"

func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // panic
	fmt.Println(f)
}
 ```
 * How would we know if the underlying value of an interface implements any other interfaces? This is also possible using type assertion. 
 If Type in type assertion syntax is an interface, then Go will check if dynamic type of i implements interface Type.
 
## Type switches
 A type switch is a construct that permits several type assertions in series.

A type switch is like a regular switch statement, but the cases in a type switch specify types (not values), 
and those values are compared against the type of the value held by the given interface value.

The syntax for type switch is similar to type assertion and it is i.(type) where i is interface and type is a fixed keyword.
Using this we can get the dynamic type of the interface instead of the dynamic value.
💡 But this syntax will only work in switch statement.

```txt
package main

import "fmt"

func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```
## Embedding interfaces
In Go, an interface cannot implement other interfaces or extend them, but we can create a new interface by merging two or more interfaces. 
*  In embedding, an interface can embed other interfaces or an interface can embed other interface’s method signatures in it.
*  When an interface, embed other interfaces in it if we made any changes in the methods of the interfaces, then it will reflect in the embedded interface.
*  You are allowed to embed any number of interfaces in a single interface.
```txt
// Go program to illustrate the concept 
// of the embedding interfaces 
package main 

import "fmt"

// Interface 1 
type AuthorDetails interface { 

	details() 
} 

// Interface 2 
type AuthorArticles interface { 

	articles() 
} 

// Interface 3 

// Interface 3 embedded with 
// interface 1 and 2 
type FinalDetails interface { 

	AuthorDetails 
	AuthorArticles 
} 

// Structure 
type author struct { 

	a_name string 
	branch string 
	college string 
	year	 int
	salary int
	particles int
	tarticles int
} 

// Implementing method of 
// the interface 1 
func (a author) details() { 

	fmt.Printf("Author Name: %s", a.a_name) 
	fmt.Printf("\nBranch: %s and passing year: %d", 
								a.branch, a.year) 
	fmt.Printf("\nCollege Name: %s", a.college) 
	fmt.Printf("\nSalary: %d", a.salary) 
	fmt.Printf("\nPublished articles: %d", a.particles) 
} 

// Implementing method 
// of the interface 2 
func (a author) articles() { 

	pendingarticles := a.tarticles - a.particles 
	fmt.Printf("\nPending articles: %d", pendingarticles) 
} 

// Main value 
func main() { 

	// Assigning values 
	// to the structure 
	values := author{ 
		a_name: "Mickey", 
		branch: "Computer science", 
		college: "XYZ", 
		year:	 2012, 
		salary: 50000, 
		particles: 209, 
		tarticles: 309, 
	} 

	// Accessing the methods of 
	// the interface 1 and 2 
	// Using FinalDetails interface 
	var f FinalDetails = values 
	f.details() 
	f.articles() 
} 
```

## Interface comparison
Two interfaces can be compared with == and != operators. 
* Two interfaces are always equal if the underlying dynamic values are nil, which means,
two nil interfaces are always equal, hence == operation returns true.
* If these interfaces are not nil, then their dynamic types (the type of their concrete values)
should be the same and the concrete values should be equal.
* If uncomparable values, then == or != operations will result in a runtime panic.

## Pointer vs Value receiver
 we have seen methods with value receivers. Will interface be ok with method accepting pointer receiver.
 No interface work with value receiver only.
