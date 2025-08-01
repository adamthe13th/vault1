# Volcano
#volcano
there is no pipelining in the same sense as CPU. the entire pipeline is only populated by a single tuple at one time
### Drawbacks
1. combined instruction footprint may be too large
2. **Many instruction cache misses**
3. a lot of function call overhead (Ops constantly call each other's functionality)
4. **State** of the operators may be too large to be cache resident
>[!info] **state of an Operator**
> Data it keeps between calls to get next
> used to: resume where it left off, control its behavior, avoid reprocessing data
> for a hash join the built hash table is part of the internal state of the operator
5. some Ops (Aggregators) must block until all their children emit all their tuples
6. Low IPC
>[!tip] **Low IPC**
>here it is cause by **Inefficient memory accesses, frequent Control Flow changes** (that come with frequent function calls) and **poor CPU pipeline utilization** (because purely sequential logic no multi-threading possible)
7. Compiler optimizations difficult/ not possible (since they would need to happen across functions)
8. Time is spent on field accesses (Data is stored in row store and getting the nth element of the tuple costs performance)
9. **SIMD** hardly applicable

>[!question] *Allows for tuple pipelining*
>does that mean that each Op would be working on a tuple (CPU style pipelining)?
> -> In Standard Volcano model: .

>[!note] if number of instructions exceeds a certain number -> instruction cache thrashing
# Materialization
#materialization
**Operator at a time** produces full columns or tables and *fully materializes then in memory* 
**No Pipelining** rather a sequence of consecutive statements
Each Op runs exactly once
(Bottom up)
## Benefits
1. loops can fit into cache
2. can be optimized by modern compilers (*Loop unrolling and SIMD can be used*)
3. can leverage modern CPU features (*HW prefetching*)
4. fewer function calls (get next out of the critical code path)
>[!info] **Critical code path**
>Code that must be executed, Determines performance **Because it's on the hot path (executed most frequently)** and cannot be optimized away
5. Cache efficient with respect to **Code** and **Operator state**
>[!warning] **BUT** each operator reads everything and writes out everything
>Data won't fully fit into cache
>repeated scans will fetch data from meory over and over
>Terrible if intermediate result doesn't fit into cache/ memory

# Vectorization
#vectorization
somewhere in the middle (also called batch at a time)
### Vector size choice
**Large enough** to amortize iteration overhead
**Small enough** to not thrash data caches

>[!note] TPC-DS Q1..4
>Q2 apparently is the only one of these 4 that performs better on *Row store* than *Column*
>*According to ChatGPT* this is because it accesses (almost) all the columns of a few rows (*more transactions like that OLAP*)

>[!abstract] **Real World systems**
>either hybrid or Column store can materialize some rows to optimize for *OLTP-like* access patterns

# Postgres Vectorized
#vectorization 
divides pipeline into groups with buffers between *Execution groups* 
**Buffer** is used to batch up tuples inside but provide a *Tuple-at-a-time interface*
> **Connect *Vectorized execution Groups* to *Tuple at a time Exec Groups***

#exchangeOp
>[!question] How does this differ from the exchange Op?

|Feature|Buffer Operator|Exchange Operator|
|---|---|---|
|Interface|Externally tuple-at-a-time, internally batch-based|Passes batches or tuples between execution units (often blocking)|
|Purpose|Connect vectorized groups to tuple-based operators|Data redistribution or pipeline breaking (often for parallelism)|
|Behavior|Non-blocking, incremental output|Often blocking, materializes input|
|Parallelism|Usually within a single process|Designed for parallel and distributed execution|
|Usage|Smooth integration of vectorization|Enables parallel query execution|

# Further
## Compiler query
#compiled
### Process
since the Ops themselves are quite simple **Cache accesses** can incur a noticeable cost
-> **Keep tuples in registers between Ops**
> **Hyper does this** by using *Jit compilation* to turn the entire query plan into a **Tight, Monolithic loop** 
> instead of calling next() since the query is compiler into *Machine code* the tuple can stay in the register

>[!abstract] Hyper also makes use of SIMD registers for this

### Pipeline breakers
#breaker
**Pipeline breakers** are a *subset of operators* that requires consuming all its inputs before it can produce any outputs
>**Sort** for example is an obvious *Pipeline breaker* since it needs to see all its inputs before it can sort them and output them in order

>[!quote] *A pipeline breaker is not an Op type rather a characteristic of a how an Op behaves within a plan*

>[!warning] when a pipeline breaker is encountered, Tuples **MUST** be moved out of registers (due to size, they cannot hold materializations)
>**Trying to keep tuples within CPU registers is only possible in between Pipeline breakers**
>->Fuze all adjacent non-breaking Ops of a query pipeline into a single *Monolithic tight loop*
>->Compile the query plan into machine code

>these are **Push-based** rather than the standard **Pull-based**
>each *Code block* consumes from the Last *pipeline breaker* and pushes it into the next
>**Carefully keep data within registers within one *Code Block***

>[!failure] No clear winner between *Typer* and *TectorWise*
>**Materialization costs** hurt TW performance while Typer keeps in Regs
>**Cache miss latency** hurts *Typer* more since batch retrieval *TW* is better at hiding cache latency

>[!info] *Typer* better for more Computational queries that can hold intermediate results in Regs and have few cache misses
>**But** suffers due to Memory stalls more than *TW*

>[!success] Relaxed Operator Fusion
>Relax **Pipeline breakers** to create *mini-batches* *if the operator can be vectorized (hash-join)*
>leverage Software prefetching
>Use ***SIMD***
-> This integrates *Cache latency hiding using batches* into *Typer*





