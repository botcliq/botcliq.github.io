---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Functions
layout: page
description: This is detailed page having info on functions in golang.
link: https://botcliq.tech/golang/functions/
---

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
