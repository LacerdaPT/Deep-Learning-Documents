# Hyperparameters Optimizatiobn and good practice

## What is the advantage of keeping batch size a power of 2?

This is a problem of alignment of the virtual processors (VP) onto the physical processors (PP) of the GPU. Since the number of PP is often a power of 2, using a number of VP different from a power of 2 leads to poor performance.

You can see the mapping of the VP onto the PP as a pile of slices of size the number of PP.

Say you've got 16 PP. You can map 16 VP on them : 1 VP is mapped onto 1 PP. You can map 32 VP on them : 2 slices of 16 VP, 1 PP will be responsible for 2 VP.Etc. 

During execution, each PP will execute the job of the 1st VP he is responsible for, then the job of the 2nd VP etc.

If you use 17 VP, each PP will execute the job of their 1st PP, then 1 PP will execute the job of the 17th AND the other ones will do nothing (precised below).

This is due to the SIMD paradigm (called vector in the 70s) used by GPUs. This is often called Data Parallelism : all the PP do the same thing at the same time but on different data. See https://en.wikipedia.org/wiki/SIMD.

More precisely, in the example with 17 VP, once the job of the 1st slice done (by all the PPs doing the job of their 1st VP), all the PP will do the same job (2nd VP), but only one has some data to work on.
