# Security
#security
- **Confidentiality** limit access of information to privileged entities 
	- requires authentication and access rights (according to a policy)
- **Integrity** limit ability to modify information to privileged entities
	- also required authentication and access rights according to policy
- **Availability** service remains available (no DDoS) "Legitimate use of the service remains available"
## Principles
- **Compartmentalization**
- **Principle of least privilege**
	- a component only has the least amount of privilege it needs to function
- **Isolation via privilege mediation**
	- "trust boundaries"
	- not entirely sure but a possible example is having a getter function for a value and having the value private (the getter function could mediate access based on some policy and and there is never direct access)
- break service up into **ISOLATED** compartments that function with the least privilege possible
- compartments are isolated (the interfaces between them can be used for trust checks "trust Boundary")

- **Authentication** : determine user Identity. can be used to determine if the user is allowed access to the system at all
- **Authorization** : given an assumed ID of the use (through Authentication) determine whether a specific action is allowed for said user
## Access control
- **ACL** access control list
- **CBAC** capability based access control
---
# Software Deployment
#deployment #T09
[Software_deployment (PDF)](PDFs/T09_Tutorial_slides.pdf)

the process of delivering software from a **development environment** to a **Live Environment**
### stages
#defs
- Testing
- Packaging: Bundling the software and its deps in an installable format
- Installation: Deploy on target system
- configuration: set up settings and Env to fit the target system or user's needs
- Validation: confirm as expected operation in the real environment

| Deployment method | Provisioning                                         | deployment                                                                                                         |
| ----------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Bare-metal        | Installation through network boot                    | Install and configure application (using Config management)                                                        |
| VM                | create VM using cloud provider API based on VM image | install and configure application ...                                                                              |
| Container         | pull image from registry                             | start container via orchestrator (Docker Swarm, Kubernetes), apply config                                          |
| Serverless        | Upload function code to serverless Provider          | - Configure requests, triggers and environment    <br>- The serverless platform executes code in a scalable manner |
- **Orchestrator** : Manages deployment, scaling, networking, and high availability of **containers across multiple machines**.
---
# Software Mgmt, Build systems DevOps
>[!example] [Software_Mgmt (PDF)](PDFs/T08_Tutorial_Slides.pdf)

#git 
- **System Building:** Collecting components to create an executable system
- **Change management** : Keeping track of requests for changes
- **Release management** : preparing software for external release -> keep track of released system versions
### Build systems
#### Task Based
e.g Make, Maven, Gradle
describes how to build using tasks `gcc something.c -o `
#### Artifact based
e.g Bazel, Buck, Pants
describes what to build and lets the build system figure out the "how"
#### Hermetic
given the same inputs and Config it always returns the same Output
#### Distributed
scale builds across thousands of build servers
## Release Management Goals
>[!example] **Page 13** [Software_Mgmt (PDF)](PDFs/T08_Tutorial_Slides.pdf)

## Deployment Automation
### Continuous Integration
automated Testing and code integration 
each push triggers: **Build,  Unit test, Static code analysis**
=> new changes get Built and we make sure they do not break anything
### Continuous Delivery
*On top of previous*
Always keep the app in a deployable state
After passing **CI**, the code is automatically **Packaged and delivered to a staging or testing environment**
> Deployment is still manual! but can be done immediately since it is ready
### Continuous Deployment
*On top of previous*
Code that passes all tests is automatically deployed to Production 
*"Every code that passes the pipeline goes live"*
> No human Approval needed (Unless configured to)
### Continuous Fuzzing
Automatically look for security/ stability issues by throwing random data at the app
specially useful for testing: Input Validation, Parsers, file readers, etc.

---

# Program Analysis
#error #analysis #failure

## Faults and failures
fault -> error -> failure
>Not all faults cause errors,  Not all errors cause failures

- **Code Coverage** depending on inputs not all code may be executed (the fault may not even come into scope)
- **Transient errors** *Invalid state* entered only briefly with no Observable side-effects *"this may happen when the program performs work that is aborted due to an interruption, retried, or otherwise reset."*
- **Fault detection or protection** before it affects system services

- **Fault avoidance**:
	- design and implementation are done in such a way that faults are avoided (e.g. good choice of programming language)
- **Fault detection:**
	- Verification and validation process. Faults are found before deploying to *Production* (Through dynamic and static code analysis)
		- **Static analysis:** scan the code without running it (syntax and type checking, linting etc.)
		- **Dynamic Analysis:** analyse the code while it is running (if you do not go through a certain execution branch, it will not be analysed e.g. ASan)
- **Fault Tolerance:** system detects faults at runtime and mitigates them

> **Reliability** the probability of *Failure-free Operation* over a specified time
> **Availability** the probability that a system will be operational and deliver its services

## Static Analysis 
>[!example] **Slide 7: [Program_Analysis (PDF)](PDFs/T07_Tutorial_Slides.pdf)**
## C
- **Spatial errors** Unintended address: Out of bounds access (Buffer/ Stack overflow)
- **Temporal errors** unintended time: *Use after free & Use after return*
## Dynamic analysis
>[!example] **Slide 16: [Program_Analysis (PDF)](PDFs/T07_Tutorial_Slides.pdf)**

---

# Testing
#test 
- **Failure** observed behavior deviated from expected (a.k.a *Crash*)
- **Error** system is in a *Erroneous state* -> further processing can lead to **Failure**
- **Fault** *bug* mechanical or algorithmic cause of an error
- **Validation** Checking deviation of Observed <-> specified behavior
### Types of Testing
- **Unit testing** test individual components
- **Integration testing** test groups of subsystems ==Test interfaces==
- **System testing** test entire system (*Does it meet requirements*)
- **Acceptance testing** ==**CLIENT**== evaluates the delivered system

> **Black Box Testing:** no visibility over code.  give inputs -> observe outputs
> 	-> **What** the software does
> **White Box Testing:** see code, test internal Logic, conditions and paths
> 	->**How** the software works

>[!example] **Slide 8: [Software_Testing (PDF)](PDFs/T08_Tutorial_Slides.pdf)**

> **Fuzzing** give the program random, abnormal inputs and check for *Bad behavior*


### Test Doubles
- **Dummy** fills parameter lists, passed around but never actually used
- **Fake** working implementation that contains a shortcut -> not suitable for production
- **Stub** provides *Canned answers* during the test
- **Mock** mimic the behavior of the real object -> knows how to deal with a specific sequence of calls that it expects to receive
---

# Patterns
#pattern 
>[!example] **End of recap: [System_Design (PDF)](PDFs/T05_Tutorial_Slides.pdf)**

- **Adapter Pattern** 
	- need to convert the interface of a class into another
	- want to reuse existing class (no compatible interface)
	- ==need to provide unified interface to a set of classes with diverse interfaces== (*Do not confuse with strategy*)
- **Observer Pattern**
	- need to establish **One-To_Many** relationship between objects
	- need to update set of dependent objects automatically on **state Change**
	- want to decouple subject form its observers (they just get notified when needed)
- **Strategy Pattern** 
	- need to define a family of Algos and make them interchangeable
	- want to select an Algo at runtime
	- want to encapsulate Algos and avoid code duplicaiton

---

# Performance
#perf
>[!example] **[System_Design_II(PDF)](PDFs/T04_Tutorial_Slides.pdf)**
### Metrics
- **Latency** time between user request and system response
- **Throughput** # of *work units* (or requests served) done per time unit
- **Utilization** % of capacity used to serve a given workload of requests

### Directions for improving perf
- **Resource Splitting** dedicated resources -> faster. resource allocator becomes more predictable
- **Caching** cache answers to expensive computations
- **Compute in Background** (*Async processing*)
- **Batch processing** rather than individual
- **Parallelism**

### Locks
- **Deadlock** threads waiting for each other to process
- **LiveLock** Threads keep doing things while waiting but cannot progress anyway
- **Starvation** state where a thread is perpetually denied access to a resource

### Parallel Programming Patterns (Scale Up)
- **Fork-Join** standard Thread forking/ joining
- **Work-stealing** 
	- Pool of *Worker Threads*
	- *Work queue*
	- "steal" tasks from the queue and process
### Scale Out
- **Replication** -> more reliability and less congestion (Work balancing)
- **Sharding** 
=> both techniques are used together

---

# Basics
#basic
>[!example] **[System_Design_Basics  (PDF)](PDFs/T03_Tutorial_Slides.pdf)**
#### Good system -> High Cohesion, Low Coupling
-> high cohesion within a single module
-> low coupling *Inter-Module*

### Facade design Pattern
- Consists of a set of public operations
- defines a higher level interface that hides away internals/ details -> ease of use

**Interface has:**
- **Formal parts** explicitly specified in the code (API/ method signatures)
- **Informal parts** captures high level behavior (usually in comments) (e.g. //this will change the array passed to it)
**=> *"The best modules are deep, Powerful functionality + Simple interface"***

### Layered architecture (Open Vs. Closed)
>[!example] **Slide 11: [System_Design_Basics (PDF)](PDFs/T03_Tutorial_Slides.pdf)**

## ACID properties
#acid

- **Atomicity** 
- **consistency** data is in a consistent state when a transaction start and ends
- **Isolation** a transaction cannot see the intermediate state of another
- **Durability** changes made successfully persist and are not undone even in the case of a system failure


## Scrum
#scrum
#### Release promotion
moving a software build through the stages of development and testing
Stages:
- Development
- build: code is compiled
- test/ QA: automated + manual testing
- staging: Pre-production environment for final validation
- Production: live release to end users

**Version**  is a named release of a product (Coarse)
**Revision** a specific change or a snapshot (fine-gained) (can also be correlated to Git)
##### roles
- Product owner: Manages Backlog and sets priorities
- Scrum master: "*facilitates scrum, removes blockers, supports the team*"
- Development team "*cross functional devs*" who actually build the product
