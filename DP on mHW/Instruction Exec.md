# Basics
#pipeline
 Ideally k-stage piepelining should improve performance by a factor of K
 slowest stage determines the clock cycle
 -> we want all cycles to be balanced
  >[!info] RISC has a 5 stage pipeline but she mentioned something about others having around 15 "*deeper pipelines*"

>[!warning] Reading the Registers happens at the instruction decode not the Execution stage

>[!warning] Even though all instructions have to go through all 5 stages,  it seems Branching instruction only requires 3 cycles and Store only 4
>The instruction still goes through all the stages but It does not use them. 
>-> The instruction is considered *Done* when it finishes all stages, even the ones that don't *do useful work for it*

>[!success] The *Branch instruction* doesn't need the *Memory access and Write-back* stage. however it still goes through them. 
>*Keep in mind the writing back to the Program counter does not count as a Write-Back stage*

### Structural Hazards
#hazard
>[!bug] Structural hazards
>some functional units are not *pipelined and single cycle*
>so, while only one instruction can be in the execution stage at a time, if the instruction needs a *Floating point divide* Functional unit (takes about **30 Cycles to complete**). instructions not using this unit can enter Execution on their own (*sometimes duplicated*) functional units. -> This might count as a ***Super-Scalar*** pipeline. 
>Purely Theoretically, it can be considered that only one instruction can be in the *Logical/ Theoretical* execution stage

as an example,  if we assume that the CPU only has one *Memory access unit* an **Instruction fetch** and a **memory access** instruction cannot happen at the same time because of a **Structural hazard** -> ***Stall*** 
These happen rarely now, perf impact non-important
### Data Hazard
#hazard
result isn't ready for the next instruction
>[!note] I think when talking about re-ordering these take a different meaning

>[!success] Solutions: *Re-ordering* or *Forwarding*

> ***Loop Unrolling*** Maximize the amount of *actual work* done per iteration to minimize the impact of the *Loop logic* - **Reduce instructions per Element**

### Control hazards
#hazard
Is the branch taken or not
#### Hardware
>[!success] *Branch Target Buffers* (or Branch Target cache)
>lookup table that stores if a certain branch was taken or not (before)
> table contains: *Lookup PC, Predicted PC, Taken?*

>[!bug] Predicate selectivity
>For high and Low *Predicate selectivity* good performance because guessing gains in accuracy
>for middling selectivity, branching becomes more *random* and more difficult to predict
#### Software
>[!tip] => we want to reduce branching: *Software predication*
>"*Turn control flow into Data flow*" 
> ` for (i=0; ... N) {count += quanitity[i]>3; }` 
> => **Performance is independent of selectivity**
> This adds extra execution of `count += 0 ` (*which makes this a bit slower than extremely high or low selectivity but much faster in between*)

>[!quote] *j+=... performs memory write in each iteration*

**Special example: 
Intel Itanium** executes both branches at the same time and keeps the one that is validated 
-> this eliminates selectivity impact and is faster than *Non-predicated (Data flow)* 

>[!warning] *Count += causes a Data Hazard*
> =>**Add a form of *Loop Unrolling* into the mix** (*two cursors*)
> loop over half the elements
> 2 counters, 1 for the first half, one of the 2nd half (with offset)
> **Better results than best case extremely low and high selectivity**

>[!note] *wouldn't it be more cache-friendly to have the cursors work on closer bits of data, on the same cache line*
>counter1 works on odd indices
>counter2 works on even
>unless guaranteed no conflicts in which case this would be slower

>[!tip] The && Operator is equivalent to *Nested if statements*
> Benefit if the first Predicate fails -> none of the subsequent predicates are evaluated 
> **But** if taken -> extra branch
> ***Alternative: Logical &***  `if (p1 & p2 & p3)` 
> + *adds* the cost of evaluating all predicates even if the first one fails
> + *removes* The cost of extra branches

> Now you can add *Logical &* to the *No branching* -> no *Predicate-selectivity dependence* 
> **But** slower Best case

![[Screenshot From 2025-08-02 11-22-24.png]]
>[!note] *&&* is much better at lower selectivity since it takes much fewer branches and checks fewer predicates
>sometimes *&&* is necessary to avoid null pointer dereferencing `if (p && p->foo==1)` 

#### Cost Model
Predicting the cost of a variant might be hard due to the parameters involved
*Characteristics of the data, machine, workload, etc.*
>[!example] Slide 27: [[InstrExec.pdf]]

> ***Micro-adaptivity:*** generate variants for each primitive operators (*with/ without branching, different compiler, Op parameters*) -> *exploit and explore* use profiling to refine the cost model and choose variant based on said *Cost Model*: exploit. (*with small probability choose a different variant to explore if there's better*)

 >[!example] Slide 29-30: [[InstrExec.pdf]]
 >*The spikes in performance are signs of exploration*

# Dynamic scheduling
#schedule #hazard
first check is for structural Hazards
>[!quote] *we keep in-order scheduling but out-of-order Execution*
>-> introduces Data hazards

### Register renaming for Data hazards
if a read is supposed to happen before a write and the re-ordering places the write before, we still want the read to have the original value
-> ***Solution*** re-route the write into another register: *Register renaming*.
>[!info] *Register renaming* happens inside the *reservation stations*
>Also inside the *Reservation stations*, an instruction is checked 
>- are there any Data hazards
>- are its Operands ready
>--> gets *dispatched to the functional unit*






