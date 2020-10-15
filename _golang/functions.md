---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Functions
layout: page
description: This is detailed page having info on functions in golang.
link: https://botcliq.tech/golang/functions/
---
A function is a block of code that takes some input(s), does some processing on the input(s) and produces some output(s).

* Go functions are first-class citizens. Functions can be assigned to variables, passed as arguments to functions or 
 returned from functions. This makes the language more flexible. *

* In Golang, we declare a function using the func keyword. A function has a name, a list of comma-separated input 
 parameters along with their types, the result type(s), and a body.

# Functions:
 * Function parameters and return type(s) are optional
 The main() function is an example of such a function -
```txt 
    func main(){
    }
 ```
 * You need to specify the type only once for multiple consecutive parameters of the same type
```txt
 func avg(x, y float64) float64 { }
// Same as - func avg(x float64, y float64) float64 { }
 ```
 *Functions with multiple return values.
```txt
    func getMulSum(i, j float64) (float64, float64) {
	sum := i + j
	mul := i*j
	return mul, sum
}
 ```
 * Returning an error value from a function
```txt
    package main

import (
   "fmt"
)

func main() {
   id, err := ReturnId()

   if err != nil {
      fmt.Printf("ERROR: %s", err)
      return
   }

   fmt.Printf("Id: %d\n", id)
}

func ReturnId() (id int,err  error) {
   id = 10
   if id == 10 {
      err = fmt.Errorf("Invalid Id\n")
      return
   }
   return
}
```
 * Functions with named return values
```txt
  package main

import "fmt"

func sum(x, y, z int) (a, b int) {

    a = x + y
    b = y + x
    return
}

func main() {
    x, y := sum(10, 100)
    fmt.Println(x, y, z)
}
```
 * Go anonymous function.
 We can create anonymous functions which do not have a name.
```txt
    package main

    import "fmt"

    func main() {

    sum := func(a, b, c int) int {
        return a + b + c
    }
    fmt.Println(sum(5,3,7))
    }
```
 * Go Closure
```txt
    package main

import "fmt"

func intSeq() func() int {

    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {

    nextInt := intSeq()

    fmt.Println(nextInt())
    fmt.Println(nextInt())
    fmt.Println(nextInt())
    fmt.Println(nextInt())

    nextInt2 := intSeq()
    fmt.Println(nextInt2())
}
```
 * Go higher-order function
```txt
     package main
import "fmt"

func apply(x, y int, add func(int, int) int, sub func(int, int) int) (int, int) {
    r1 := add(x, y)
    r2 := sub(x, y)
    return r1, r2
}


func main() {
    x := 3
    y := 4
    add, sub := getAddSub()
    r1, r2 := apply(x, y, add, sub)
    fmt.Printf("%d + %d = %d\n", x, y, r1)
    fmt.Printf("%d - %d = %d\n", x, y, r2)
}

func getAddSub() (func(int, int) int, func(int, int) int) {
    add := func(x, y int) int {
        return x + y
    }
    sub := func(x, y int) int {
        return x - y
    }
    return add, sub
}
```
 
# Variadic Functions
  Think of a function that can take any number of  arguments, it could be 0 , 1 or more.
A variadic function is a function that accepts a variable number of arguments. 
If the last parameter of a function definition is prefixed by ellipsis ..., then the function can accept any number of arguments for that parameter.

## How to Make a Function Variadic.
  * If the last parameter of a function has type ...T it can be called with any number of trailing arguments of type T.
    You can only have one variadic parameter in a function, and it must be the last parameter defined in the function
'''txt
package main

import "fmt"

func Mul(nums ...int) int {
    res := 0
    for _, n := range nums {
        res *= n
    }
    return res
}

func main()
    fmt.Println(Mul())        // 0
    fmt.Println(Mul(1, 2, 3)) // 6
}
'''

  * The actual type of ...T inside the function is []T.
  * You can pass the elements of a slice s directly to a variadic function using the s... notation. 
  In this case no new slice is created.

'''txt
primes := []int{2, 3, 5, 7}
fmt.Println(Sum(primes...)) // 17
'''

Some examples of Variadic functions are:
 * fmt.Println
   '''txt
   fmt.Println("THis is the print")
   fmt.Println("This is the Print with additional args", 1, 2)
   '''
 * append is variadic.
   ```txt
   var buf []byte
   buf = append(buf, 'a', 'b')
   buf = append(buf, "cd"...)
   fmt.Println(buf) // [97 98 99 100]
   ```
## How is IT different from Slice Arguments:
 * There is no need to create a slice during each function call.
 * Creating empty slice not required in case of variadic function.
 ```txt
 package main
 import fmt
 func Mul(nums []int) int {
    res := 0
    for _, n := range nums {
        res += n
    }
    return res
}

func main()
    fmt.Println(Sum([]{}))        // 0
    fmt.Println(Sum([]{1, 2, 3})) // 6
}
 ```
## Exercise: 
  * what would be output of this code
  ```txt
  package main

import (
	"fmt"
)

func change(s ...string) {
	s[0] = "Go"
	s = append(s, "playground")
	fmt.Println(s)
}

func main() {
	welcome := []string{"hello", "world"}
	change(welcome...)
	fmt.Println(welcome)
}
```
