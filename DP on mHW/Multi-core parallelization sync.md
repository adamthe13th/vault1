>[!tip] *The main reasons for sublinear scaling*
>- Not enough parallelism (*some parts of the program may be inherently sequential*)
>- Overhead due to the parallelization (*Typically synchronization*)
>- Architectural limitations (*memory contention*)

>[!danger] *Amdahl's Law*
>if *p* is the proportion of the task that is parallelizable (*1-p must be sequential*) (*n number of cores*)
>then the maximum speedup that can be achieved is **$\frac{1}{(1-p)\frac{p}{n}}$** 

for example, if 90% of the code is *parallelizable* the maximum speed-up possible is 10

>[!tip] *More throughput -> Query level parallelism (inter-query) || Lower Latency -> Intra-Query parallelism (should still allow a little inter)* 
>OLTP better with inter OLAP better with intra

>[!quote] *Process is an abstraction of a computer (with its own allocated private memory), a Thread is an abstraction of a processor (or Core, they share all the process memory)*
>**User level threads** are implemented in user space (can be preemptively or cooperatively scheduled, invisible to kernel) 
>**Fibers** lighter weight that **User level threads** cooperative and yield themselves to run another fiber while executing
## Process model of the DBMS
### Os process per DBMS worker (outdated)
on each incoming request an OS process is spawned (*expensive*) to handle the request then dies
+ better isolation
### OS thread
lighter weight
the DBMS is a *multi-threaded* process
+ dispatcher thread listens for requests and assigns worked threads
>[!tip] *A combination is also possible* where the OS processes don't have to be spawned each time but are taken from a pool (*less expensive*)

### DBMS threads
user level threads
+ more control
+ faster task switching
+ **BUT** you need to replicate a lot of OS logic

# Volcano style parallelization
this can be done *intra-operator* for example parallelized hash join OR *inter-operator* 2 independent operators being run at the same time (*Bushy trees*)
## Inter
using the ***Exchange Operator***
Upside:
+ Operators are largely oblivious to the parallelism
Downsides:
+ static work partitioning
+ cannot easily change degree of parallelism *mid-query*
+ Potential overhead (*too many exchange operators -> could cause thread over-subscription*)
+ Exchange operators create additional copies of tuples

>[!info] *DOP Degree of Parallelism* is the number of servers associated with an operator

>[!danger] An important aspect of parallelizing something like the Radix-join is to use a *Task queuing model*
>+ +fine granularity of tasks
>+  +More tasks than there are threads (different from *over-subscription* since these stay in a  queue until worked on and don't cause contention)

if one of the tasks is too big (can be determined in advance using histograms) either
+ split it into smaller tasks
+ if splitting is not possible, leave it for later once all other tasks are done (*to avoid all threads slowing down due to one*)

>*Determining the threshold for a Task being too big is of course important here. we don't want to incur re-partitioning cost for nothing*

>[!tip] *Query pipeline parallelization* in relation to *Morcel driven parallelization*
>within the query pipeline, morcels are processed in parallel **between *Pipeline breakers***

***Pessimistic lock*** is just the standard lock
read-write lock stores an exclusive bit and 63 bit counter of readers
**Optimistic lock** stores n exclusive bit and a 63 bit version number (if write to object -> change version number. when reading check version number before and after to know if something changed in between)
> ***Optimistic lock*** never locks for reads only for writes (*version checking is if it gets locked and changed after I read*)

>[!bug] *Optimistic lock only works if restarting (because version number changed in the mean time) can be done without side-effects*
>-> if too much *write contention* -> readers get starved

>[!Danger] ***Cache line ping-ponging!*** is a side effect of spin locking (or a read write lock in general and the changing the number of readers invalidates the cache line containing the RW-lock)

> although spin locking wastes CPU cycles, it does happen in user space which is less expensive than using the kernel to make the thread wait and wake it up when ready

>[!tip] when doing **mostly** big reads (e.g. an entire table scan within one read) *Optimistic lock becomes an issue because restarting a read becomes too expensive*
>->shared becomes a better option
>*for mostly reads **That aren't too big** an optimistic lock is still the best option*

>[!success] ***Solution***
>they created a *hybrid-lock* that includes both a normal read-write lock and a version number. (*when locking exclusively, always increment version number before unlocking*)
>-> reading is done optimistically by default but if it is not possible resort to normal "*pessimistic*" shared lock (RW)

>[!example] *Lock benchmarking* 
>[Multi-core_parallel_sync(PDF)](Resources/Multi_para_sync.pdf)

## Locking granularity
the number of tuples **protected by a single lock** 
*Benchmarking results in the same PDF above*
optimistic for example struggles a lot with coarser granularity
hybrid obviously better at adapting
## Contention handling strats
### Spinning
types:
1. Naive: *test and set*
2. *Test, test and set* (with back off)
3. Local spinning
4. Ticket lock (with back off)
>[!note] *Am i supposed to know how all these work?*

### blocking
1. std::mutex 
2. Parking lot: each thread *parks itself in a hash table until the callback condition is satisfied*
>[!quote] *the **Sentinel** elements of a list are the head and tail*

>Optimistic lock coupling: 
>for writes:
>+traverse the list like a reader *but lock the elements you are changing* you also have a version number (*update counter*) instead of having to check if the elements are still reachable but traversing the list again, you can just check if the version number changed 
>*keep in mind a version number change can be unrelated to the elements that concern the current transaction* -> increment before leaving
>for reads: no locking *but check version number when done*






