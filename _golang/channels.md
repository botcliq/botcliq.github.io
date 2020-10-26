---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Go channels
layout: page
description: This is detailed page having info on channels in golang.
link: https://botcliq.tech/golang/channels/
---

Channels are Used in Goroutines but they can be of different types too:

## Unbuffered Channels
 In unbuffered channel there is no capacity to hold any value before it's received. In this type of channels both a sending and receiving goroutine to be ready at the same instant before any send or receive operation can complete.
 
 If the two goroutines aren't ready at the same instant, the channel makes the goroutine that performs its respective send or receive operation first wait.
 
```txt
    package main

    import (
    "fmt"
    "time"
    )

    func main() {
    ch := make(chan int)

    go func(ch chan int) {
        fmt.Println("Func goroutine begins sending data")
        ch <- 1
        fmt.Println("Func goroutine ends sending data")
     }(ch)

    fmt.Println("Main goroutine sleeps 2 seconds")
    time.Sleep(time.Second * 2)

    fmt.Println("Main goroutine begins receiving data")
    d := <- ch
    fmt.Println("Main goroutine received data:", d)

    time.Sleep(time.Second)
    }
```

## Buffered Channels
In buffered channel there is a capacity to hold one or more values before they're received.
* Sends to a buffered channel are blocked only when the buffer is full. 
* Similarly receives from a buffered channel are blocked only when the buffer is empty.

There are also different conditions for when a send or receive does block.
* A receive will block only if there's no value in the channel to receive. 
* A send will block only if there's no available buffer to place the value being sent..

```txt
    package main    
    
    import (
    "fmt"  
    "math/rand"
    "sync"
    "time"
    )
 
    var goRoutine sync.WaitGroup
 
    func main(){
        rand.Seed(time.Now().Unix())
 
    // Create a buffered channel to manage the employee vs project load.
    projects := make(chan string,10)
 
    // Launch 5 goroutines to handle the projects.
    goRoutine.Add(5)
    for i :=1; i <= 5; i++ {
        go employee(projects, i)
    }
 
    for j :=1; j <= 10; j++ {
        projects <- fmt.Sprintf("Project :%d", j)
    }
 
    // Close the channel so the goroutines will quit    
    close(projects)
    goRoutine.Wait()
}
 
func employee(projects chan string, employee int) {
    defer goRoutine.Done()
    for {
        // Wait for project to be assigned.
        project, result := <-projects
 
        if result==false {
            // This means the channel is empty and closed.
            fmt.Printf("Employee : %d : Exit\n", employee)
            return
        }
 
        fmt.Printf("Employee : %d : Started   %s\n", employee, project)
 
        // Randomly wait to simulate work time.
        sleep := rand.Int63n(50)
        time.Sleep(time.Duration(sleep) * time.Millisecond)
        // Display time to wait
        fmt.Println("\nTime to sleep",sleep,"ms\n")
 
        // Display project completed by employee.
        fmt.Printf("Employee : %d : Completed %s\n", employee, project)
    }
 
}
```


Let's understand channel lets furst see its attributes:

## Guarantee Of Delivery
 Guarantee of delivery means the message or data sent by me is recieved.
 
| Attribute | UnBuffered  |  Buffered       |
|-----------|-------------|-----------------|
| Delivery  |Guranteed    | Not Guarantees  |

## State
 The channel state affects its behaviour. It may be one of open , nil or closed. 
 * The state determines how the send and receive operations behave.
 
| Operaton  | Nil      |  Open    | Closed   |
|-----------|----------|----------|----------|
| Send      |Blocked   | Allowed  | Panic    |
| Receive   |Blocked   | Allowed  | Allowed  |
 
## With or Without Data
The last attribute that needs to be taken into account is whether you need to signal with or without data.
When you signal with data, it’s usually because:
* A goroutine is being asked to start a new task.
* A goroutine reports back a result.

When you signal without data, it’s usually because:
* A goroutine is being told to stop what they are doing.
* A goroutine reports back they are done with no result.
* A goroutine reports that it has completed processing and shut down.

## Nil Channels
A nil channel reacts to actions on it as follows:
* Reading from a nil channel blocks forever
* Sending to a nil channel blocks forever
* closing a nil channel panics

```txt

package main

import (
	"fmt"
	"math/rand"
	"time"
)

func add(c chan int) {
	sum := 0
	t := time.NewTimer(time.Second)
	for {
		select {
		case input := <-c:
			sum = sum + input
		case <-t.C:
			c = nil
			fmt.Println(sum)
		}
	}
}

func send(c chan int) {
	for {
		c <- rand.Intn(10)
	}
}

func main() {
	c := make(chan int)
	go add(c)
	go send(c)
	time.Sleep(3 * time.Second)
}
```

## Signal Channels
Channels in Go are a great way to communicate data between goroutines, but you can also use them just for signalling. Such channels are called signal Channels.

Put simply, you can use a signal channel when you want to inform somebody else about something. Signal channels should not be used for transferring data.

* When doing this, it’s good practice to use an empty struct as the type of the channel. 
* It also has the interesting property of not taking up much space in memory — since an empty struct has no fields inside it.

*Example is combined with channel of channels*

## Channel of Channels
One simple reason you’d send a chan on a chan is to tell a goroutine to do work and then get an acknowledgement (ack hereafter) that it’s finished doing that work.
To declare a data type for a channel of channels. You can define a channel of channels using the chan keyword two times in a row, as shown in the following statement:

    c1 := make(chan chan int)

```txt

package main

import (
	"fmt"
	"os"
	"strconv"
	"time"
)

#The f channel is a signal channel used for ending the  goroutine when the real work is
finished.
func f1(cc chan chan int, f chan bool) {
	c := make(chan int)
	cc <- c
	defer close(c)
	sum := 0
	select {
	case x := <-c:
		for i := 0; i <= x; i++ {
			sum = sum + i
		}
		c <- sum
	case <-f:
		return
	}
}

func main() {
	arguments := os.Args
	if len(arguments) != 2 {
		fmt.Println("Need just one integer argument!")
		return
	}
	times, err := strconv.Atoi(arguments[1])
	if err != nil {
		fmt.Println(err)
		return
	}
	cc := make(chan chan int)
	for i := 1; i < times+1; i++ {
		f := make(chan bool)
		go f1(cc, f)
		ch := <-cc
		ch <- i
		for sum := range ch {
			fmt.Print("Sum(", i, ")=", sum)
		}
		fmt.Println()
		time.Sleep(time.Second)
		close(f)
	}
}
```



