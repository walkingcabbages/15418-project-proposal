# 15418/618 Cache Coherence Simulator

Amy Cheng, David Herman

[Link to Proposal](https://walkingcabbages.github.io/15418-project-proposal/)

## SUMMARY
We are going to build a trace-driven cache simulator, implement several snooping-based cache coherence protocols, and measure metrics such as hit/miss rate, latency, and amount of coherence traffic.

## BACKGROUND
In class we learned about different snooping-based cache coherence protocols, such as MESI, MESIF, and MOESI.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/MESI.png?raw=true)

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/coherence_state.png?raw=true)

We additionally are adding read snarfing optimization, in which a processor will snoop for other processors’ read responses for blocks that its own cache has invalidated. This is a natural extension of snooping-based cache coherence protocols we have learned in class, and may reduce cache misses and traffic.

## THE CHALLENGE
One of the challenges of parallelizing a cache is ensuring coherence correctness is maintained. All processors need to share an order of events that they can agree on. Communicating when lines become invalid and written to is essential for keeping a correct multi-core cache simulation. This will be challenging because we need to understand all aspects of cache coherence protocols we learned in lecture in order to implement correctly. 

Second, we need to create test programs of varying workloads to generate the memory traces, on which we will run our cache simulator. It is important to test different workloads to ensure that the performance metrics have an applicable use to general programs and to potentially find which ones are better from different protocols.

We also may need to find and integrate additional frameworks to build on top of in order to provide a realistic and applicable simulation.

## RESOURCES
We are planning to start from [15-346 _Design and Simulation's_ starter framework](https://github.com/bprail/cadss_public) for the cache simulator. We will use some larger coherence traces (/afs/cs.cmu.edu/academic/class/15346-s22/public/traces/coher) and use intel’s Pin tool to generate our own traces. We will also use [this paper](https://dl.acm.org/doi/pdf/10.1145/225830.223998) as a resource to add read snarfing.

## GOALS & DELIVERABLES
*PLAN TO ACHIEVE:*

- MESI snoop-based cache coherence implementation
- MESIF snoop-based cache coherence implementation
- MOESI snoop-based cache coherence implementation
- Add read snarfing 
- Obtain traces and run cache simulator for several programs with a range of workloads
- Experiments with varying number of cores, workloads, coherence protocols, etc

*HOPE TO ACHIEVE:*

- Add additional optimizations
- Obtain traces and run cache simulator for programs with more unique workloads

*DEMO:*

- Graphs of our experiments
- All protocols, any optimizations, and all metrics on all programs

*HOPE TO LEARN:*

Questions we hope to answer in our analysis are the performance of MESI, MESIF, MOESI against multiple different programs and which protocols suit certain programs better. We want to see if there is one best protocol or do they all have trade offs (which is more likely). We are also hoping to answer questions like are the effects of adding read snarfing and other potential goals to the protocols, and how are they affected differently, both good and bad. 

## PLATFORM CHOICE
We will develop, debug, and run experiments on GHC machines.

## MILESTONES
*April 14:* Implement MSI (David) and MESI (Amy).

*April 17:* Test MSI (David) and MESI (Amy) implementations.

*April 21:*: Add metric tracking. Finish testing MSI and MESI.

*April 24:* Implement and test MESIF (Amy) and MOESI (David). Write test traces to aim for 100% coherence code coverage for MSI, MESI, MESIF, MOESI.

*April 28:* Select larger traces and run experiments. Add snarfing (both) and other optimizations.

*May 1:* Learn Pin tool to generate our own traces. Run all experiments.


# MILESTONE REPORT

## Work Completed:
We have read through the starter framework at https://github.com/bprail/cadss_public. Based on our understanding of the APIs, we have implemented MSI and MESI but have not yet tested these implementations. Both of these protocols are compiling and can execute, however we are unsure of exactly how correct they are.

## Progress:
With respect to the goals and deliverables in our proposal, we are a little behind schedule. Understanding the starter framework and changing our plan to use the starter framework rather than coding on top of other simulation frameworks or from scratch took a bit of time. We have reorganized our schedule to use Pin tool to generate traces last, or as an extra, given that there are larger traces provided at /afs/cs.cmu.edu/academic/class/15346-s22/public/traces/coher.

## Poster Session Plan:
We are still planning on showing graphs of metrics such as hit rate, eviction rate, coherence traffic, etc from our experiments with varying number of cores, workloads, coherence protocols at the poster demo session.

## Preliminary Results:
n/a

## Concerning Issues:
We are currently still trying to understand different parts of the starter code and figure out how to test our MSI and MESI implementations. We plan to write different traces, add print statements within state transitions in MSI and MESI, and manually verify correctness. However, we are unsure if this is the most robust and efficient way to test. We believe that it will still produce good results as we can write edge cases for traces to determine if the implementation is working correctly. There are not that many cases to test especially for protocols like MSI. One issue right now is that without the reference code, we are unsure how certain parts of the code work as well as how to figure out how it works without asking the professor: for instance, how exactly different functions we write in coherence are being called, what different bus request types mean, etc. Not knowing the framework in its entirety is the biggest limiting factor to our work. 










