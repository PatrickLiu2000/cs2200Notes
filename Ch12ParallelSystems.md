**Ch12: Multithreaded Programming and Processors**

- do multiple things at once

# Why Multithreading?
- `thread` - represents active unit of processing
- allows program to express concurrent activities easily
- allows computing I/O activities as well since it doesn't need CPU
![](./images/ch12/0.png)
- many programs must have multiple processes run, so doing them all at once increases efficiency
# Programming Support for Threads
- need key abstraction operations for threads
    - create
    - terminate
    - communicate
    - synchronize
## Thread Creation/Termination
- entry point of a thread is any user-defined procedure
    - **top level procedure** - any procedure that is visible where procedure is used for thread creation
    - `thread_create (top-level procedure, args);`
        - thread creation call
        - creates another thread that is a child of the thread that made the call
        - parent-child relationship
- starting point for threaded program is typical sequential procedure
- address space is the same for children threads as well as parent thread
- process vs thread
    - process
        - has more state information (because thread shares address space with a process)
    - thread
        - threads have no memory protection from each other within the address space
- termination   
    - automatically terminates when it exits top-level procedure 
    - or explicit call: `thread_terminate (tid);`
        - `tid` = thread id
## Communication Among Threads
- threads need to share data
- data structures are shared between threads as well if they are in the top-level procedure
    - example: global vars
## Data Race and Non-Termination
- **data race** - multiple threads attempt to access same shared variable at same time and another thread trying to write to shared variable
    - doesn't always mean there is an error
- example:
![](./images/ch12/1.png)
    - what is printed depends on which thread finishes first
- since everything executes asynchronously, program order (order in which instructions appear in code)is not deterministic anymore
- since order is not guaranteed, can have lots of machine instructions between threads interleaved
    - instructions of the same thread execute in program order
![](./images/ch12/2.png)
- programmer must understand the non-deterministic nature of multithreaded programs
## Synchronization Among Threads
- **producer and consumer threads**
    - producer must not modify shared buffer while consumer is reading it
        - called **mutual exclusion**
- **mutual exclusion lock**
    - like declaring variables
    - once thread acquires lock, no other thread can get that lock until the thread releases it
    - declaration
        - `mutex_lock_type mylock;`
    - acquire/release lock
        - `thread_mutex_lock (mylock);`
        - `thread_mutex_unlock(mylock);`
    - if thread is currently using lock but other thread needs it, it is **blocked**
    - can also check to see if lock is in use
        - `{success, failure} <- thread_mutex_trylock (mylock);`
    - what do other threads do if it is trying to access a lock that is in use?
        - either execute something else, or wait
    - **critical section**
        - region in program where execution of threads is ordered
    - example:
    ```c
    mutex_lock_type lock;
    int counter; /* shared counter */
    int increment_counter() {
        thread_mutex_lock(lock);
        counter = counter + 1;
        thread_mutex_unlock(lock);
        return 0;
    }
    ```
    - can be used by all threads, but only 1 thread at a time can increment counter
- Rendezvous
    - parent should wait for child 
    - example: parent spawns child to read file, and parent needs file contents so it waits
    - `thread_join (peer_thread_id);`
        - function blocks the caller until the named thread terminates - only works on 1 main and 1 child thread
    - basically all threads that participate in the rendezvous resume execution once they all reach the rendezvous point
    - when parent thread terminates, watch out for children processes also terminating
        - might want to wait until all children processes are done before terminating
## Internal Representation of Data Types Provided By Thread Library
- `thread type` and `mutex_lock_type` types are opaque types (user has no direct access to internal representation)
- `mutex_lock_type`
    - internal representation contains current user of the lock
    - queue of requestors for the lock
## Simple Programming Examples
- basic code w/ no synchronization
    - sample program 1:
    ![](./images/ch12/3.png)
    - `frame_buf` and `bufavail` are modified by both threads
        - `fram_buf` is a circular queue with head & tail
    - 2 threads are independent except in shaded areas, where they use the shared variables
- need for atomicity for a group of instructions 
    - problem is that both threads can be writing/reading from same var at the same time
    - correct execution is that first thread executes, then 2nd thread, or vice versa - **atomic**
    - need to make sure access to shared data structures is mutex
- code refinement with coarse grain critical sections
    - sample program 2:
    - uses mutex lock, so ensures atomicity, but has performance issues, as entire other thread cannot loop if lock is in use
    ![](./images/ch12/4.png)
- Code refinement with fine grain critical sections
    - no need to lock entire loop - only access to shared data structure
    - sample program 3:
    ![](./images/ch12/5.png)
## Deadlocks/Livelocks
- upon analysis of program 3, can see that deadlock can occur when buffer is full - digitizer continuously checks while statement, but tracker cannot remove image from buffer since digitizer has lock
- **livelock** - special case of deadlock where thread is actively checking for an event that will never occur
- solution: remove locks around while statements
![](./images/ch12/6.png)
## Condition Variables
- ideally, we want system to recognize that thread is waiting on a condition to be satisfied, so until the condition is satisfied, it should release control of lock - this is where condition vars come in
- declaration: `cond_var_type buf_not_empty;`
- wait and signal:
    - `thread_cond_wait(buf_not_empty, buflock);`
        - waiting descedules the thread that makes the call
        - implicitly performs an unlock
    - `thread_cond_signal(buf_not_empty);`
        - signals any thread that may be waiting on condition var
        - indicates that any thread waiting can resume execution
        - performs an implicit lock, and schedules waiting thread
    - multiple threads can wait on a signal, typically handled in FCFS manner
    - must ensure that signal doesn't come prematurely
### Internal Representation of Condition Variable Data Type
- `cond_var_type`
    - contains queue of threads waiting for signal
    - for each thread, its associated mutex lock
## Complete solution to example
![](./images/ch12/7.png)
- maintains an invariant that calling thread always holds the lock for the conditional wait
### Discussion of Solution
- concurrency
    - when checking the buffer, in either outcome, the lock is always released
    - when updating the variable, only lock when need to update it
    - no lack of concurrency
- no deadlocks
## Rechecking the Predicate
- example only works for 1 digitizer and tracker
- more general approach