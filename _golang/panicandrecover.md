Go doesn't support exception throwing and catching, instead explicit error handling is preferred to use in Go programming. In fact, Go supports an exception throw/catch alike mechanism. The mechanism is called panic/recover.

We can call the built-in panic function to create a panic to make the current goroutine enter panicking status. The panic is only alive within the current goroutine.

Panicking is another way to make a function return. Once a panic is produced in a function call, the function call returns immediately and enters its exiting phase. The deferred function calls pushed in the defer-call stack will get executed, by their inverse order being pushed.

By calling the built-in recover function in a deferred call, an alive panic in the current goroutine can be removed so that the current goroutine will enter normal calm status again.

If a panicking goroutine exits without being recovered, it will make the whole program crash.

The built-in panic and recover functions are declared as
