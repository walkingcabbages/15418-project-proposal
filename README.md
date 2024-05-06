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


# FINAL REPORT

## Summary:
We created a cache coherence simulator on top of Professor Railing’s computer architecture framework. We implemented MSI, MESI, MOESI, and MESIF protocols along with adding read snarfing to MSI by writing our own cache.


## Background:
As described in class, MSI, MESI, MOESI, and MESIF all build on top of one another. MSI builds upon MI by adding the Shared state, which allows multiple processors to read data and reduces the amount of bus traffic and invalidations. MESI builds upon MSI by adding the Exclusive state to combat the inefficiency of bus traffic upon read-writes. We go into further detail on MESIF, MOESI, and read snarfing, which were covered less in depth in class.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/mesif_state.png?raw=true)

MESIF is a continuation of MESI which allows a state of Forward to be the designated sender of data when other processors wish to reach the Shared state. In MESI, for instance, when multiple caches have a data x in shared state and another processor requests a read copy of x, all other caches which have x in Shared state will respond to the requesting processor. However, MESIF eliminates the unnecessary bus traffic, since only the singular cache holding the shared line in Forward will respond to the request for data.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/moesi_state.png?raw=true)

MOESI is another continuation of MESI where cache lines can have a state of Owned. These Owned states are responsible for sending data to other processors who request read copies to gain Shared state. This also allows a cache line in Modified to delay the flush when another processor becomes Shared.

Finally, read snarfing is a protocol which is used to reduce traffic on the bus. With read snarfing, processors can snoop the bus for the data fulfillment of other processors’ requests for data. If a processor snoops a data request for another processor’s read, it will check if the data is in their cache. If the processor holds an invalidated copy inside of their cache, it will update with the new version and set it to valid. This allows processors to have up-to-date data that they might use in the future which reduces the contention for the bus when they finally need that data. Read snarfing generally helps in the case where there is one writer and multiple readers, as all readers will have the updated data from the data fulfillment of the first reader’s request.

## Approach:
We built our protocols on top of Professor Railing’s CADSS framework, which enabled us to combine our coherence, interconnect, and cache implementations with reference solutions of other parts of the architecture.
To implement the MSI, MESI, MOESI, and MESIF protocols, we programmed each state as a case which allows us to check every time a request was made if there were conditions necessary for a change of state. We also implemented many intermediate states, such as between invalid and modified to enforce the serial order of processors’ requests. This allows cache lines a state for while they wait for the necessary resources, be it data or permission, to advance to the next state. Processors observe other processors actions on the bus by snooping and will react accordingly via a cache action. To implement this, we created two functions, one for handling the initial request and one for snooping the bus. Both are implemented via switch statements, which essentially make up a finite state machine. For the interconnect, we mostly used what was given in the framework. However, one place where we changed it was to add a new bus request, a bus upgrade (BUSUPG). This upgrade allows cache lines in the Shared state to check if they are allowed to move into Modified without sending data through the bus: processors simply broadcast a bus upgrade and if any other processors are in a state of shared, they will downgrade to invalid. When the processor who made the request gets back the request, it knows the serial order has been enforced and it is safe to transition to Modified. 

To implement read snarfing on top of MSI, we needed to support fetching information about whether snooped data is inside the cache and invalid, so we implemented our own cache to support these extra needs. We used our 213 code as a baseline, and built on top of the simpleCache example to support read snarfing, requests which span multiple lines, etc. We also changed the interconnect to allow processors to snoop data fulfilling read requests. If a processor were to do so, their cache action for that snoop would be to try snarfing which signals the cache to search for a matching line, even if invalid. The cache then responds back to the coherence controller and if the cache has found the tag then the coherence will update its state from invalid to shared. 

We also instrumented our code with metrics tracking. We tracked latency of requests, memory transfer count, cache transfer count, total number of caches responding to data requests, flush count, bus request count as a measure of bus traffic, read miss count, write miss count, and invalidation count–a selection of these metrics are shown below in our results.

Throughout our process, we encountered several challenges with our implementation, understanding, and testing. While MSI, MESI, MOESI, MESIF have great state diagrams which helped us to understand the desired end-goal, read snarfing did not have as much information. Based on the paper Professor Mowry provided us, we were able to hash out what read snarfing was and design the interaction between cache and coherence. Also, while going through the codebase, we found some inconsistencies between our assumptions and what reference solutions were printing out. To solve this issue, we read more and more of the codebase, tested many different traces to attempt to figure out why the reference solution behaved as it did, and wrote our own implementations to test. Lastly, we struggled a bit with testing correctness of our implementations, as there were no set test cases or validation scripts. We tested by creating our own traces, aiming for total code coverage in each of our coherence protocols, and manually verifying our traces through the print statements we placed in our code. Unfortunately, we were not able to use the Pin tool as planned to generate traces, due to problems installing onto ghc and incompatibility with our local machines, so we ended up obtaining our results through alternative traces. This was pretty unfortunate as we were hoping to obtain bigger traces like traces of parallel programs we programmed in class. Given more time, we would have prioritized obtaining larger and more representative traces earlier on, so our results could be more representative of real-world workloads.

## Results:
We were successfully able to implement all the protocols and read snarfing. However, we did not end up using the Pin tool or related tools to generate large traces, as we were unable to find out how to install Pin to the ghc machines, and our local machines were incompatible. Instead, we used two traces from /afs/cs.cmu.edu/academic/class/15346-s22/public/traces/coher directory, and designed two of our own traces to try to illustrate the different behaviors of different protocols.

Below, we present the results of our experiments, in which we run four different traces with each protocol MI, MSI, MESI, MOESI, MESIF, and Read snarfing MSI.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/write_misses.png?raw=true)

For write misses, MI has the most as all its misses are writes due to only having the modified state: both reads and writes in MI count as write misses since the processor will issue a BUSWR request. The rest of the protocols all share the same amount of misses as all these protocols only allow one writer, therefore not being able to reduce write misses.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/read_misses.png?raw=true)

Looking at read misses for the different protocols, obviously MI cannot read miss as it only has readXs, which we account for as write misses. MSI, MESI, MOESI, and MESIF all share around the same read misses as they all have a form of sharing. For some reason that we are unable to explain, MSI with read snarfing seems to have higher misses for one trace, but read snarfing should always be expected to have lower misses than MSI. However for the other traces, it is expected that snarfing would reduce read misses as more processors would have data that is readable, and our results do confirm this. 

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/cache_supplying_data.png?raw=true)

For cache data responses, it is expected that MI has the most. This is due to the fact that every read will incur a response from another cache. Other protocols have the shared state which allows multiple processors to continuously read without any transfer of data. However, for MI to do the same, when processors request the same data, data must be continuously passed between caches. It is also expected that MOESI and MESIF would have less, as both of them have a designated sender instead of all caches responding. However, only MESIF seems to have less cache data responses. This could be due to the forward state being more commonly accessed compared to the owned state. In the owned state, one must reach modified first and then observe a read. This is a rarer state to get to rather than one of the sharers in MESIF being the forward. For MSI with read snarfing, it makes sense that there are some cases that incur higher responses and some lower. This is due to the fact that if the processor would benefit in the future from a snarf, then the amount of cache responses is net lowered from snarfing. If the processor would not have needed the data in the future, then that cache response is just wasted, therefore increasing the total amount.

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/invalidations.png?raw=true)

MI obviously has the most invalidations due to processors having to invalidate everytime there is a read. The other protocols all have around the same which mostly makes sense. These protocols cannot prevent invalidations when a state is in modified and another processor is attempting to write as well. 

![alt text](https://github.com/walkingcabbages/15418-project-proposal/blob/main/bus_traffic.png?raw=true)

Bus traffic generally decreases at four observable places: (1) from MI to MSI, (2) from MSI to MESI on read-write trace, (3) from MESI to MESIF, and (4) from MSI to Read Snarfing MSI. At (1) from MI to MSI, bus traffic decreases due to fewer invalidations: the addition of the Shared state enables multiple caches to hold a line for reading. At (2) from MSI to MESI on read-write trace, since each processor does a read followed by a write, there is one fewer bus request per read-write sequence in MESI: MESI’s Exclusive state to Modified state transition saves a bus request. At (3) from MESI to MESIF, the addition of the Forward state reduces the number of processors in Shared state which respond to data requests; with the Forward state, only the cache with the line in Forward state will respond, rather than all caches with the line in Shard state. At (4) from MSI to Read Snarfing MSI, we see a decrease in bus traffic for some of the traces, which can be explained by the way read snarfing will bring valid data into the cache in Shared state, despite the cache not having been the requestor.

Overall, the general trends in our experiments confirmed what we learned in class and in additional research.

## References:
We have much to thank for Professor Railing’s CADSS framework. Without it, it would’ve been extremely hard to implement higher level protocols without spending significantly more time building the lower levels of the framework.
We also used the paper “Boosting the Performance of Hybrid Cache Protocols” that Professor Mowry led us to as a reference for read snarfing.
- https://github.com/bprail/cadss_public
- https://dl.acm.org/doi/pdf/10.1145/225830.223998 



