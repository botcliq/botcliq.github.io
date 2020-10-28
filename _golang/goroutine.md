---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Goroutines
layout: page
description: This is detailed page having info on goroutines in golang.
link: https://botcliq.tech/golang/goroutines/
---

[![hackmd-github-sync-badge](https://hackmd.io/hrLAiVI4SAytBq_99s-hVQ/badge)](https://hackmd.io/hrLAiVI4SAytBq_99s-hVQ)

Concurrent programming in many environments is made difficult by the subtleties required to implement correct access to shared variables. Go encourages a different approach in which shared values are passed around on channels and, in fact, never actively shared by separate threads of execution.

* Only one goroutine has access to the value at any given time.
* Data races cannot occur, by design.
* based on [Hoare’s Communicating Sequential Processes (CSP)](http://en.wikipedia.org/wiki/Communicating_sequential_processes)

## GoRoutine

 A  goroutine is a lightweight thread managed by the Go runtime.
 To create a go routine you need to write go just before calling any function.
 
```
go concurrent(args)
```

Things to keep in mind:
* The cost of creating a Goroutine is tiny when compared to a thread. 
* Goroutines run in the same address space, so access to shared memory must be synchronized.
*  The sync package provides useful primitives for synchronisation, there are other libraried too.

### Advantages of Goroutines over threads
* Goroutines are extremely cheap when compared to threads.*Only a few kb in stack size and the stack can grow and shrink according to needs*
* The Goroutines are multiplexed to fewer number of OS threads.
* Prevent race conditions from happening when accessing shared memory using Goroutines.

### Start a Goroutine
Prefix the function or method call with the keyword go. e.g.
package main

```
import (
    "fmt"
)

func fibonacci(n int, c chan int) {
    x, y := 0, 1
    for i := 0; i < n; i++ {
        c <- x
        x, y = y, x+y
    }
    close(c)
}

func main() {
    c := make(chan int, 10)
    go fibonacci(cap(c), c)
    for i := range c {
        fmt.Println(i)
    }
}
```

### WaitGroups.

lets take the below example.
package main

```
import "fmt"

func goroutineFunc() {
    fmt.Println("Inside my goroutine")
}

func main() {
    fmt.Println("Hello World")
    go goroutineFunc()
    fmt.Println("Finished Execution")
}
```

Here go routine is not run, as main goroutine exits before the goroutine func can be run.
In such cases we have to add sleep in the main func for goroutines to run.

**WaitGroups essentially allow us to tackle this problem by blocking until any goroutines within that WaitGroup have successfully executed.**

We first call .Add(1) on our WaitGroup to set the number of goroutines we want to wait for, and subsequently, we call .Done() within any goroutine to signal the end of its’ execution.

Our testcase will block only till the number of waitgroup finish execution.
lets see the updated code.

package main

```
package main

import (
    "fmt"
    "sync"
)

func goroutine1 (waitgroup *sync.WaitGroup) {
    fmt.Println("Inside my goroutine1")
    waitgroup.Done()
}

func goroutine2 (waitgroup *sync.WaitGroup) {
    fmt.Println("Inside my goroutine2")
    //return
    waitgroup.Done()
}

func main() {
    fmt.Println("Hello World")

    var waitgroup sync.WaitGroup
    waitgroup.Add(1)
    go goroutine1(&waitgroup)
    waitgroup.Add(1)
    go goroutine2(&waitgroup)
    waitgroup.Wait()

    fmt.Println("Finished Execution")
}
```

### Waithing Using Channels

  You can also use channel to block program from exiting.
  e.g.
```
package main

import (
    "fmt"
)

func fibonacci(n int, c chan int) {
    x, y := 0, 1
    for i := 0; i < n; i++ {
        c <- x
        x, y = y, x+y
    }
    close(c)
}

func main() {
    c := make(chan int, 10)
    go fibonacci(cap(c), c)
    for i := range c {
        fmt.Println(i)
    }
}
```