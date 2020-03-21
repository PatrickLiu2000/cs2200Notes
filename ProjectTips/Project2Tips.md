# Project 2: Interrupts Tips
- in general, for adding the timer handlers in circuitsim, the overarching process is extremely similar to project 1
## Resources List
- textbook
- piazza
- office hours
- reading project pdf carefully :)
## Assembly Code Tips
- make sure that your implementation of saving registers onto your stack is correct
  - common errors: creating too much space onto stack (ie decrementing/incrementing stack pointer too many times)
    - I would strongly recommend you simply decrement/increment the stack pointer by 1, then saving/restoring the register to avoid incorrect arithmetic
- rest is pretty simple assembly stuff
## Microcode Tips
- see my project 1 tips for formatting tricks that will make your life a lot easier
- hint: when should the CPU check for interrupts? (what macrostate)
- Interrupt ROM works extremely similar to the comparison ROM
- almost all of the instructions are implemented in microcode exactly how they are explained in the textbook/project pdf

# REMEMBER TO SIGN UP FOR DEMOS AND DO NOT MISS THEM!!!!