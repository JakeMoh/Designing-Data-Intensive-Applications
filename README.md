# Designing-Data-Intensive-Applications


# Chapter 1

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
- *Average* is not a good measurement for response time since it does not tell you how many users actually experienced that delay
- It is better to use *percentiles*
- In order to figure out how bad your outliers are, you can look at higher percentiles: the p95, p99, and p99.9 percentiles are common
- Higher percentiles of response times, also known as *tail latencies*, are important because they directly affect users' experience of the service
- Queueing delays often account for a large part of the response time at high percentiles. As a server can only process a small number of things in parallel, it only takes a small number of slow requests to hold up the processing of subsequent requests - an effect sometimes known as *head-of-line blocking*
- High percentiles becomes especially important in backend services that are called multiple times as part of serving a sing end-user requests. Even if you make the calls in parallel, the end-user requests still needs to wait for the slowest of the parallel calls to complete

### Approaches for Coping with Load

- Scaling up: Vertical scaling, moving to a more powerful machine
- Scaling out: Horizontal scaling, distributing the load across multiple smaller machines
- Distributing load across multiple machines is also known as a *shared-nothing* architecture
- The architecture of systems that operate at large scale is usually highly specific to the application

### Maintainability

- Majority of the cost of software is not in its initial development but in its ongoing maintaenance
- To minimize pain during maintenance, we should pay particular attention to three design principles for software systmes:
1. Operability: Make it easy for operations team to keep the system running smoothly
2. Simplicity: Make it easy for new engineers to understand the system, by removing as much complexity as possible from the system
3. Evolvability: Make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change. Also known as *extensibility*, *modifiability*, *plasticity*

### Operabiltiy: Making Life Easy for Operations

- Operation teams are vital to keeping a software system running smoothly
- A good operations team typically is responsible for the following:
  - Monitoring the health of the system and quickly restoring service if it goes into a bad state
  - Tracking down the cause of problems, such as system failures or degraded performance
  - Keeping software and platforms up to date, including security patches
- Good operability means making routine tasks easy, allowing the oeprations team to focus their efforts on high-value activities. Data systems can do various things to make routine tasks easy, including:
  - Providing visibility into the runtime behaviour and internals of the system, with good monitoring
  - Providing good support for automation and integration with standard tools
  - Providing good documentation and an easy-to-understand operational model ("If I do X, Y will happen")

### Simplicity: Managing Complexity

- Small software projects can have delightfully simple and expressive code, but as projects get larger, they often become very complex and difficult to understand. This complexity slows down everyone who needs to work on the system, further increas‐ ing the cost of maintenance

- There are various possible symptoms of complexity:
  - Explosion of state space
  - Tight coupling of modules
  - Tangled dependencies
  - Inconsistent naming and terminology
  - Hacks aimed at solving performance problems
- In complex software, there is also a greater risk of introducting bugs when making a change: when the system is harder for developers to understand and reason about, hidden assumptions, unintended consequences, and unexpected interactions are more easily overlooked
- One of the best tools we have for removing accidental complexity is abstraction

### Evolvability: Making Change Easy


It’s extremely unlikely that your system’s requirements will remain unchanged for ever. They are much more likely to be in constant flux: you learn new facts, previously unanticipated use cases emerge, business priorities change, users request new features, new platforms replace old platforms, legal or regulatory requirements change, growth of the system forces architectural changes, etc

- In terms of organizational processes, Agile working patterns provide a framework for adapting to change
- The Agile community has also developed technical tools and patterns that are helpful when developing software in a frequently changing environment, such as test-driven development (TDD) and refactoring
- Evolvability == Aginity


### Summary

An application has to meet various requirements in order to be useful. There are *functional requirements* (what it should do, such as allowing data to be stored, retrieved, searched, and processed in various ways), and some *nonfunctional requirements* (general properties like security, reliability, compliance, scalability, compatibil‐ ity, and maintainability). In this chapter we discussed reliability, scalability, and maintainability in detail.

Reliability** means making systems work correctly, even when faults occur. Faults can be in hardware (typically random and uncorrelated), software (bugs are typically systematic and hard to deal with), and humans (who inevitably make mistakes from time to time). Fault-tolerance techniques can hide certain types of faults from the end user.

*Scalability* means having strategies for keeping performance good, even when load increases. In order to discuss scalability, we first need ways of describing load and performance quantitatively. We briefly looked at Twitter’s home timelines as an example of describing load, and response time percentiles as a way of measuring performance. In a scalable system, you can add processing capacity in order to remain reliable under high load.

*Maintainability* has many facets, but in essence it’s about making life better for the engineering and operations teams who need to work with the system. Good abstractions can help reduce complexity and make the system easier to modify and adapt for new use cases. Good operability means having good visibility into the system’s health, and having effective ways of managing it.