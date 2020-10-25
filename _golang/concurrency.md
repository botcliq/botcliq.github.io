# Go Concurrency
 Go has built in concurrency support. That is supported by Go scheduler.
 Go works using M:N scheduler,
  - It schedules goroutines (lighter than os threads), i.e. M
  - It uses Os threads to schedules goroutines i.e. N
  - The value of Os threads set by GOMAXPROCS variable. (usually set to no of processor)
 
 Go uses *fork-join* concurrency model
   * fork - child branch can be created at any point of a program
   * join - In this part the child branch will end and joing parent programs.
          sync.Wait() and channels are the joint as they collect results.
          
Concurrency in golang is provided by goroutines. 
 *A gorouting is a task, whereas everyting after gorouting is a continuation.*

## The Select Keyword
  The select keyword on golang is similar to switch statement, but it allow goroutines to wait on multiple channels.
  *It provides non-blocking operaion on channels.*
  
```txt

    package main
    import (
	    "fmt"
	    "math/rand"
	    "os"
	    "strconv"
	    "time"
    )

    func gen(min, max int, createNumber chan int, end chan bool) {
	    for {
		select {
		    case createNumber <- rand.Intn(max-min) + min:
		    case <-end:
			    close(end)
			    return
		    case <-time.After(4 * time.Second):
			    fmt.Println("n\time.After()!")
		    }
	    }
    }

    func main() {
	    rand.Seed(time.Now().Unix())
	    createNumber := make(chan int)
	    end := make(chan bool)
	    if len(os.Args) != 2 {
		    fmt.Println("Please give me an integer!")
		    return
	    }
	    n, _ := strconv.Atoi(os.Args[1])
	    fmt.Printf("Going to create %d random numbers.\n", n)
	    go gen(0, 2*n, createNumber, end)
	    for i := 0; i < n; i++ {
		    fmt.Printf("%d ", <-createNumber)
	    }

	    time.Sleep(5 * time.Second)
	    fmt.Println("Exiting...")
	    end <- true
    }
```

## Go Timouts
  Concurrent programming has its own idioms. A good example is timeouts.
Implementing timeouts in Go is easy and elegant thanks to channels and select.
 Take a scenario where we want to receive from the channel, but want to wait at most one second for the value to arrive.
```txt

    package main
    import (
        "fmt"
        "time"
    )

    func main() {

        c1 := make(chan string, 1)
        ## Sleep for 2 second to have timout condition hit
        go func() {
            time.Sleep(2 * time.Second)
            c1 <- "result 1"
        }()

        select {
            case res := <-c1:
                fmt.Println(res)
            case <-time.After(1 * time.Second):
                fmt.Println("timeout 1")
        }

        c2 := make(chan string, 1)
        #time is less than timout ,so timeout condition will not hit.
        go func() {
            time.Sleep(2 * time.Second)
            c2 <- "result 2"
        }()
        select {
            case res := <-c2:
                fmt.Println(res)
            case <-time.After(3 * time.Second):
                fmt.Println("timeout 2")
        }
    }
```

## Order of Execution of Goroutines
  You might ask why choose to execute goroutines in a given order when
simple functions could do the same job much more easily. The answer is
simple: goroutines are able to operate concurrently and wait for other
goroutines to end, whereas functions executed in sequence cannot do that!

  We can use channels to define order of execution of goroutines.
  * The goroutine can wait on rrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrrr
 
```txt
package main

import (
	"fmt"
	"time"
)

func A(a, b chan struct{}) {
	<-a
	fmt.Println("A()!")
	time.Sleep(time.Second)
	close(b)
}

func B(a, b chan struct{}) {
	<-a
	fmt.Println("B()!")
	close(b)
}

func C(a chan struct{}) {
	<-a
	fmt.Println("C()!")
}

func main() {
	x := make(chan struct{})
	y := make(chan struct{})
	z := make(chan struct{})
	go C(z)
	go A(x, y)
	go C(z)
	go B(y, z)
	go C(z)
	close(x)
	time.Sleep(3 * time.Second)
}


$ go run defineOrder.go
A()!
A()!
B()!
C()!
C()!
C()!
panic: close of closed channel
```