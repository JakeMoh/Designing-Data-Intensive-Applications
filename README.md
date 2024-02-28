# Designing-Data-Intensive-Applications

---

# Chapter 1

---

## Realiability
The system should continue to work *correctly* (performing the correct function at the desired level of performance) even in the face of *adversity* (hardware or software faults, and even human error).

- "Continue to work correctly, when when things go wrong (fault)"
- *Fault-tolerant* is misleading as it suggests we could make a system tolerant for every possible kinds of faults
- Fault != Failure
  - Fault is defined as one component of the system deviating from its spec
  - Failure is when the system as a whole stops providing the required service to the user
  - It is impossible to have 0 fault system and it is the goal to design fault-tolerance mechanisms that prevent faults form causing failures
- Counterintuitatively, in such fault-tolerant systems, it could make sense to increase the rate of faults by triggering them deliverately as many critical bugs are actually due to poor error handling
- We genrally prefer tolerating faults over preventing faults because no cure exists

### Hardware Faults
Hardware faults happen *all the tiem* when you have a lot of machines and there is a move towards systems that can tolerate the loss of entire machines, by using software fault-tolerant techniques in preface or in addition to hardware redudancy.

### Software Errors
Unlike hardware faults, systematic faults within the system are hard to anticipate, and because they are corrleated across nodes, they tend to cause many more system failures than uncorrelated hardware faults.

- Example
  - A software bug that causes every instance of application server to crash when given a particular bad input
  - A service that the system depends on that slows down, becoming unresponsive, or starts returning corrupted response
- The bugs that cause these kinds of software faults often lie dormant for a long time until they are triggered by an unusual set of circumstances.
- There is no quick solution to the problem of systematic faults in the software but lots of small things can help:
  - Carefully thinking about assumptions and interactions in the system
  - Thorough testing
  - Process isolation
  - Allowing process to crash and restart
  - Measuring
  - Monitoring (alarms)
  - Analyzing system behaviour in production

### Human Errors
Humans design, build, and operate software systems but are unreliable.

How do we make our systems reliable?
  - Design systems in a way that minimizes opportunities for error (e.g. well-designed abstractions, APIs, and admin interfaces)
  - Decouple the places where people make the most mistakes from the places where they can cause failures
  - Test thoroughly at all levels, from unit tests to whole-system integration tests and manual tests
  - Allow quick and easy recovery from human errors, to minimize the impact in the case of failure
  - Set up detailed and clear monitoring, such as performance metrics and error rates
  - Implement good management practices and training

### Scalability
As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with growth.

### Maintainability
Over time, may different people will work on the system (engineering and operations, both maintaining current hehaviour and adapting the system to new use cases), and they should all be able to work on it *productively*.

---

## Scalability

Scalability is a term used to describe a system's ability to cope with increased load.

### Describing Load

Load can be described with a new numbers called *load parameters*. For Twitter example, the system design depends on the load parameters such as rate of read and write rate.

### Describing Performance

Once load is described in the systerm, you can invest what happens when the load increases. You can look at it in two ways:

1. When you increase a load parameter and keep the system resources (CPU, memory, network bandwidth, etc.) unchanged, how is the performance of your system affected?
2. When you increase a load parameter, how much do you need to increase the resources if you want to keep performance unchanged?

> *Latency* and *response time* are often used synonymously, but they are not the same. The response time is what the client sees; besides the actual time to process the request (service time), it includes network delays and queueing delays. Latency is the duration that a request is waiting to be handled - during which it is latent, awaiting service.

- In practice, in a system handling a variety of requests, the response time can vary a lot. We therefore need to think of response time not as a single number, but as a *distribution* of values that you can measure
- Random latnecy variation can come from context switch to a background process, the loss of a network packet and TCP retransmission, a garbase collection pause, a page fault forcing a read from disk, mechanical vibrations in the server rack, or many other causes

