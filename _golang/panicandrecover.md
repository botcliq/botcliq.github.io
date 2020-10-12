---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Panic And Revover
layout: page
description: This is detailed page having info exception handling in golang.
link: https://botcliq.tech/golang/panicrecover/
---

Go doesn't support exception throwing and catching, instead explicit error handling is preferred to use in Go programming. In fact, Go supports an exception throw/catch alike mechanism. The mechanism is called panic/recover.

 * We can call the built-in panic function to create a panic to make the current goroutine enter panicking status. The panic is only alive within the current goroutine.

 * Panicking is another way to make a function return. Once a panic is produced in a function call, the function call returns immediately and enters its exiting phase. The   deferred function calls pushed in the defer-call stack will get executed, by their inverse order being pushed.

 * By calling the built-in recover function in a deferred call, an alive panic in the current goroutine can be removed so that the current goroutine will enter normal calm status again.

 * If a panicking goroutine exits without being recovered, it will make the whole program crash.

The built-in panic and recover functions are declared as
	` func panic(v interface{}) `
	` func recover() interface{} `
  
Two things to remember is :
  * The value returned by a recover function call is the value a panic function call consumed.
  * The  blank interface type interface{} can be viewed as the any type or the Object type.
  
Panic Uses:
 * Go runtime will create panics for some circumstances, such as dividing an integer by zero. For
    ` package main
    `
    ` func main() {
    `   a, b := 1, 0
    `	_ = a/b
    ` }
    
 ** For the standard Go compiler, some fatal errors, such as stack overflow and out of memory are not recoverable. Once they occur, program will crash. **
 
 * Avoid Panics Crashing Programs
  This should be the most popular use case of panic/recover. The use case is used commonly in concurrent programs, especially client-server programs.
 
 * Automatically Restart a Crashed Goroutine
   When a panic is detected in a goroutine, we can create a new goroutine for it. An example:
   package main

` import "log"
`  import "time"
`
`  func shouldNotExit() {
`	  for {
`		  // Simulate a workload.
`		  time.Sleep(time.Second)
`
`		  // Simulate an unexpected panic.
`		  if time.Now().UnixNano() & 0x3 == 0 {
`			panic("unexpected situation")
`		  }
`	  }
` }
`
`  func NeverExit(name string, f func()) {
`	  defer func() {
`		  if v := recover(); v != nil {
`			  // A panic is detected.
`			  log.Println(name, "is crashed. Restart it now.")
`			  go NeverExit(name, f) // restart
`	  	}
`	  }()
`	  f()
`  }
`  
`  func main() {
`	  log.SetFlags(0)
`	  go NeverExit("job#A", shouldNotExit)
`	  go NeverExit("job#B", shouldNotExit)
`	  select{} // block here for ever
`  }
`  
  
  
