# Project 3: Virtual Memory Tips
## Don't Just Read, Understand
- make sure to not just read the textbook/lecture slides very carefully, but make sure that you fundamentally know the concepts that it discusses
    - in this case knowing exactly what a frame table and page table are (what their entries map to, and how to index into them), the concept of paging (basically contiguous memory chunks)
    - will save you extreme amounts of time debugging if you have a good grasp of concepts

## Use office hours, But plan ahead
- when you are stuck debugging something or having trouble understanding something, TA office hours are your best friend, but also realize that the days leading up to the project, office hours will be packed
    - I waited around 2.5 hours for a TA to help me in the queue, and this was two days before the project was due! Plan ahead and go to office hours when it is not as packed
## Piazza
- look on Piazza and see if anyone has made any clarifying posts/relevant questions
    - I solved a lot of my segfaults by looking on Piazza and finding related questions
## Read Instructions Carefully
- read code comments and project PDF carefully
    - double check that you implemented all of the things that the PDF and the comments tell you to do! I could have saved a lot of time debugging if I had realized I was missing an entire implementation detail within my code
## Diagrams
- drawing a diagram of where the frame table and page tables are in memory made this project so much more simple, since a lot of the portion of the project involves indexing into the exact memory locations of the entries within these tables. Here's an example diagram I used for my project:
![](./images/projects/0.jpg)
## GDB
- inevitably when you get a segfault, you will need to debug! GDB is one of the best ways to do this. While GDB may seem scary at first since it is a command line tool with no GUI, it is equally as powerful.
- first, to install gdb within your Vagrant VM, use the command
    - `sudo apt install gdb`
- to start debugging after you have changed your source code, run these commands:
1. `make clean` - trashes any old object files/executables
2. `make debug` - makes new executables/object files, and allows you to debug using GDB
    - or simply use `make` if you want to check you differences between log files
    - MAKE SURE TO DO THESE 2 COMMANDS EVERY TIME YOU MAKE A CHANGE TO YOUR SOURCE CODE
3. `gdb ./vm-sim` - runs GDB with the simulator
4. `r -i traces/<trace_file_you_want_>.trace -<page_replacement_algo_to_test> -c`
    - the `-c` flag runs it with the corruption checker
        - this verifies that your data structures are correct after each cycle, potentially catching segfaults before they occur
        - I would highly recommend you run it with this flag if you are having segfaults
5. Last step: once you run into the segfault/error, you want to see the code and variables around it!
    - this is where the GDB commands come in. I would highly recommend you check out this website: http://www.yolinux.com/TUTORIALS/GDB-Commands.html or other tutorials online on the useful commands that GDB offers you.

# REMEMBER TO SIGN UP FOR DEMOS AND DO NOT MISS THEM!!!!