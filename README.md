# ep-framework
Extreme Program Framework
This framework is aim at background micro services, and also include a preemptive web server balancer for this framework.
framework is develop by C++ (11 and above) and Lua (5.3.4).

Let's introduce how this framework working.

# ep-midas
this c++ library is the core of framework, a cross-protocol and cross-platform middle ware.
## cross-protocol! 
what it cross-protocol? this means it can change various protocol very easily, so that we can compatible with old projects.
for this example, ep-midas have it's own high speed transfer protocol, and also compatible with HTTP GET and POST( add more in furture )
**NOTICE:** this cross-protocol is not running each protocol in different listen port to make it _cross_, as this is an _extreme_ program, I use a crazy way to make it, HTTP and EP-Protocol is using the same TCP listen port.

**ep-midas** contain several true **LOCK-FREE**  producer-consumer async-modules, and make a async-module, but it didn't contain any thread at all. it just supply a looks like below diagram:

single-thread producer -> protocol decoder -> multi-thread consumer

multi-thread producer  -> scheduler        -> single-thread consumer

this diagram is use for in a classic scenario of TCP like:
single-thread( recv(socket) ) -> multithread( process, reply ) -> single-thread( send(socket) )

why use single thread to receive and send? because socket library, the highest speed is single thread mode. now a days, one CPU core is already powerful enough for receive job, and send job, a hardware network-card's core won't higher than CPU itself, multi-thread to do receive or send job, just waste more machine resource.


# ep-framework
this is a **C++ and Lua** projecj. as we known, there's Client-Server, Brower-Server type in program. this framework, it's all in one.
this means, framework could be a server, also could be a client, could be a master, also could be a slave. it can acting as a server also could be a client of another server. this's what **Cloud** cluster needed.
then, framework's main logic is all in LUA, framework provide several LUA object for lua script to control the whole program.



# LUA-project
basic on ep-framework, and running with different, then make various micro service. the basic micro serveice is below:


## Balancer
which use for load-balancing, what we usually used, it non-preemptive balancer. For me, a true load-balance is must be preemptive.
this is simple, think about the situation: 
1. Wokers:  I'm free, I'll take this job!     
     Boss: Jobs are overthere, just take it.
   
2.   Boss: Are you free?
   Wokers: yes, I'm.
     Boss: take this job to do.
    
situation 1, it means workers would keep get job as long as finished then current job.
situation 2, it means workers need to wait until boss give a job.

Load-Balance is aim at the situation 1 excatly, but why we use situation 2? the reason is simple, because we don't have time to develop it! we only have time to search some components and use it. components are not so perfact to fit for any kind of use. so all we can do is use non-preemptive balancer, better than nothing.

**balancer** program, it receive any request, and put request in a queue basic on various condition, and all the other worker program is link to balancer to take the job.

**module worker** program, this are different micro service, module worker would only take the job that it can deal with. we can make a big system apart into several micro service to do different job.


finally, we can't make a background service looks like:

```
-------------------------------------------------------------------------------------------------
| clients    clients     clients ...                   clients      clients     clients ...
|             |                                                       |
|             |                                                       |
|             |                                                       |
|          Balancer1 (side 1)                                     Balancer2 (side 2)
|             |                                                       |
|     -------------------------------------------------------------------------
|     |                 |                  |
|     |                 |                  |
|     |                 |                  |
|     |                 |                  |
| user service        money service       other service
--------------------------------------------------------------------------------------------------
```
service can add in any time, and if the same service is in full-loaded, we can run one more same service for load-balance.
between service and balancer, they would contain each status in every job transfer, so, in fact, balancer would do like:
receive a job, find the queue, if queue is 0 size, push to a servvice to do, else put to queue. 




















