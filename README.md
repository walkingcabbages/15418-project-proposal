# 15418-project-proposal

[Link to Proposal](https://walkingcabbages.github.io/15418-project-proposal/)

## SUMMARY
We are going to build a trace-driven cache simulator, implement several snooping-based cache coherence protocols, and measure metrics such as hit rate, eviction rate, and amount of coherence traffic.

## BACKGROUND
In class we learned about different snooping-based cache coherence protocols, such as MESI, MESIF, and MOESI.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/MESI.png?raw=true)

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/coherence_state.png?raw=true)

As a stretch goal, we additionally would like to add read snarfing, in which a processor will snoop for other processors’ read responses for blocks that its own cache has invalidated. This is a natural extension of snooping-based cache coherence protocols we have learned in class, and may reduce cache misses and traffic.

## THE CHALLENGE
One of the challenges of parallelizing a cache is ensuring coherence correctness is maintained. All threads need to share an order of events that they can agree on. Communicating when lines become invalid and written to is essential for keeping a correct multi-core cache, and this challenge is difficult because for every cache line, we must keep track of its state. This will also be challenging because we need to understand all aspects of cache coherence protocols we learned in lecture in order to implement correctly. 

Second, we need to create test programs of varying workloads to generate the memory traces, on which we will run our cache simulator. It is important to test different workloads to ensure that the performance metrics have an applicable use to general programs and to potentially find which ones are better from different protocols.

We also may need to find and integrate additional frameworks to build on top of in order to provide a realistic and applicable simulation.

## RESOURCES
We are planning to start from scratch for the cache simulator, but we will use intel’s Pin tool to generate traces to feed into the simulator. We will use Open MPI for simulating communication across cores. We will also use this paper as a resource to add read snarfing. We will use PSC machines for higher core counts.

## GOALS & DELIVERABLES
*PLAN TO ACHIEVE:*
MESI snoop-based cache coherence implementation
MESIF snoop-based cache coherence implementation
MOESI snoop-based cache coherence implementation
Obtain traces and run cache simulator for several programs with a range of workloads
Experiments with varying number of cores, workloads, coherence protocols

*HOPE TO ACHIEVE:*
Adding read snarfing and additional optimizations
Obtain traces and run cache simulator for programs with more unique workloads

*DEMO:*
Graphs of our experiments
All protocols, any optimizations, and all metrics on all programs

*HOPE TO LEARN:*
Questions we hope to answer in our analysis are the performance of MESI, MESIF, MOESI against multiple different programs and which protocols suit certain programs better. We want to see if there is one best protocol or do they all have trade offs (which is more likely). We are also hoping to answer questions like are the effects of adding read snarfing and other potential goals to the protocols, and how are they affected differently, both good and bad. 

## PLATFORM CHOICE
We will develop and debug on GHC machines, and run experiments on PSC machines. It makes sense to use this system because in developing we will need multiple cores, but probably not a super high amount. GHC has 8 available cores which should be good. For testing we wish to see the performance of the simulator on many cores which the PSC machines allow us to do.

## SCHEDULE
*Week 1:* Learn Pin tool to generate traces and plan and write out pseudocode for the multicore cache.

*Week 2:* Finish MESI implementation.

*Week 3:* Create MESIF and MOESI.

*Week 4:* Add snarfing and other optimizations




