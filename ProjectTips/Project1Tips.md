# Project 1: Datapath Implementation Tips
## Best Resources
- textbook - a lot of the diagrams of the different units, as well as the descriptions of the different instructions themselves are implemented exactly in this project
- piazza
- office hours - start early when office hours will not have as many people
- 2110 LC3 datapath circuitsim file - if you have taken CS2110, that datapath is extremely similar to the one we implement here, although missing some datapath elements. However, this is a great resource if you have no idea how to start.
## Implementing Microcode
### Formatting
- since the excel sheet is so big, here are some formatting tips to help debug
- there is an excel option to highlight cells that are a certain value. In this case, we want to highlight all cells that have a `1` in them. Here is how to do this in excel:
  - click on the conditional formatting box:
![](../images/projects/1.png)
    - then, click "highlight cells rules"
      - then click "equal to"
        - and in this case, we want to highlight `1`, so type 1 into the box.
  - helps with seeing which signals you assert in each microstate
- finally, I would recommend you highlight each instruction and their respective microstates in a different color adjacent to each other so it just makes it easier to see which instruction corresponds to which
### Debugging
- i would create separate assembly files, simply calling 1 instruction and see if it outputs correctly in the datapath before testing the assembly file the project wants you to test
  - this allows you to isolate instructions one by one, and making sure you have implemented them all correctly
- make sure your `next state` fields are correct!
## Final Thoughts on Project
- this project is pretty representative of how projects in this class go; one of the main things being that you cannot even test your implementation until nearly the last step, which kind of sucks
  - for this reason, I would highly suggest that you read up on the concepts using the textbook/recitation slides that are discussed before implementing it, as a simple mistake in the datapath can lead to a lot of headaches in debugging later
  
# REMEMBER TO SIGN UP FOR DEMOS AND DO NOT MISS THEM!!!!