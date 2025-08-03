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



