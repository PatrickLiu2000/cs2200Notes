# Project 4: Process Scheduling/Multithreading
- relevant chapters: 6 and 12
## Locking/Unlocking Mutices
- Book has good documentation/examples of `pthread` methods and their usages
## Debugging Tips
- make sure to always recompile before rerunning the simulation :)
- if your code is infinitely looping, then you are running into a deadlock!
    - check that for every lock in your code, there is a corresponding unlock statement
    - also check that you are unlocking on edge cases where you return early
- if your code does not deadlock, but is not producing the correct output, check for any weird behavior in your Gantt chart
    - for example, if all of your processes are study in the ready queue, check if you are dequeuing correctly
```
2.1   0  3  0       (IDLE)       < <
2.2   0  3  0       (IDLE)       < <
2.3   0  3  0       (IDLE)       < <
2.4   0  3  0       (IDLE)       < <
2.5   0  3  0       (IDLE)       < <
2.6   0  3  0       (IDLE)       < <
2.7   0  3  0       (IDLE)       < <
2.8   0  3  0       (IDLE)       < <
2.9   0  3  0       (IDLE)       < <
3.0   0  3  0       (IDLE)       < <
```
    
- if all of your processes are waiting, then there might be a problem with enqueue
    - CHECK EDGE CASES FOR DEQUEUE/ENQUEUE
        - edge case for dequeuing when there is only 1 node left
        - enqueueing first element
        - updating head and tail pointers correctly
- if ready times are too high, check if you are setting you process states correctly/in the order that the pdf/method headers tell you
- if you are encountering `Scheduled a terminated process! PID: 0`, then you are most likely missing an edge case in your ready queue code
## Hints
- when you are adding support for the different command line arguments and changing your methods accordingly, try using static variables to store the state of your program
- when doing priority with preemption, you must update your queueing methods to be a priority queue

- other than that, same advice as the other projects: read the pdf and method headers very carefully, check piazza for good notes/insights other students have posted, and obviously if you are really stuck, go to TA's office hours

# REMEMBER TO SIGN UP FOR DEMOS AND DO NOT MISS THEM!!!!