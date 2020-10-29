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

## Worker Pools.

 In this section we will see working with wokers pool.
 
 Worker pools are a model in which a fixed number of m workers (implemented in Go with goroutines) work their way through n tasks in a work queue (implemented in Go with a channel).
 
 Work stays in a queue until a worker finishes up its current task and pulls a new one off.
 
```
package main

import (
    "fmt"
    "time"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for j := range jobs {
        fmt.Println("worker", id, "started  job", j)
        time.Sleep(time.Second)
        fmt.Println("worker", id, "finished job", j)
        results <- j * 2
    }
}

func main() {

    const numJobs = 5
    jobs := make(chan int, numJobs)
    results := make(chan int, numJobs)

    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }

    for j := 1; j <= numJobs; j++ {
        jobs <- j
    }
    close(jobs)

    for a := 1; a <= numJobs; a++ {
        <-results
    }
}

op:
 go run goroutines/worker.go 
worker 3 started  job 1
worker 1 started  job 2
worker 2 started  job 3
worker 2 finished job 3
worker 2 started  job 4
worker 1 finished job 2
worker 1 started  job 5
worker 3 finished job 1
worker 1 finished job 5
worker 2 finished job 4
```

## Atomic
The package implements atomic operations, which are operations that cannot be interrupted. 
    Even though Go’s motto is “Do not communicate by sharing memory; instead, share memory by communicating,” there are cases where channels are overkill and you simply want a shared variable.
* They are lockless and generally implemented directly at hardware level.
* To use the atomic functions we need to import the sync/atomic package.
* The package contains load, store and the addition operation for the int32, int 64, uint32, uint64, etc. Since only ints are such primitive that can be correctly synchronized using atomics.

```
package main
 
import (
    "fmt"
    "sync"
    "sync/atomic"
)
 
func f(v *uint32, wg *sync.WaitGroup) {
    for i := 0; i < 3000; i++ {
        atomic.AddUint32(v, 1)
    }
    wg.Done()
}
 
func main() {
    var v uint32 = 42
    var wg sync.WaitGroup
    wg.Add(2)
    go f(&v, &amp;wg)
    go f(&v, &amp;wg)
    wg.Wait()
 
    fmt.Println(v)
}
```
## Mutex

You can use mutex from sync package.

```
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup, m *sync.Mutex) {  
    m.Lock()
    x = x + 1
    m.Unlock()
    wg.Done()   
}
func main() {  
    var w sync.WaitGroup
    var m sync.Mutex
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w, &m)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```