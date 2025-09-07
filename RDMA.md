# Programming models
>[!warning] NUMA has cache coherency. when a core from a NUMA node tries to access memory that is in another node's cache, the mechanism kicks in to make the *holder* flush its cache with the latest version of the data
>-> This however incurs a *Huge performance penalty.* The solution is then to treat the system like it's *non-coherent*
>-> *Write-through* instead of *Write-back* and use fencing around accessed memory to avoid conflict.
## Shared memory programming
you treat the whole memory as a single address space -> *allows the cache coherency mechanism to work* ->**Cache Coherent NUMA** (*pthreads/ OpenMP*)
-> Implicit communication through memory
## (Global) Partitioned address space
essentially NUMA standard. there's both local and remote memory accesses
>[!success] *PGAS Partitioned Global Address Space*
>a *Programming language concept* for writing **Parallel applications** for large **Distributed memory machines**

>[!tip] *Atomics* `fetch_and_add, compare_and_swap` are highly useful

>[!quote] *ChatGPT: "Even though it’s NUMA, **cache coherence is still maintained** between nodes (unless the system is explicitly non-coherent)."*
## Distributed Memory programming
communication is explicit through ***Messages***
>[!tip] *Cache-Coherency Domain*
>is a group of components that stay automatically in sync when memory is loaded into them
>(e.g. Registers stay automatically in sync if a value is changed)

# RDMA through NIC
>[!info] The OS (*and thus the CPU too*) is involved in the *setup* (it assigns the buffer that the NIC uses) but then the NIC can act separately and does not need any further *Wasted cycles.* 

>*Bypassing the kernel* -> no interrupts or context switching (*cheaper*)
>***Zero-copy*** data refers to the lack of need for a copy from *User-space* to *Kernel space*
>->Less memory bus contention
>***Message based transaction*** between the NICs
>***Async operation*** -> overlap communication and computation

![[Screenshot From 2025-08-04 10-27-53.png]]
## Setup
1. Pin the memory (so it cannot be swapped out by the OS)
2. Store the *Address translation information* in the NIC (so it can do the VA->PA)
3. Set permissions for the memory region
4. return a *remote and local key* used by the adapters when executing RDMA operations (for access control/ protection mechanism at the RDMA hardware level)
>[!warning] RDMA can be *Send-and-receive* (both sides are actively sending and receiving) or *Read-and-Write* where during an access only one side (sender) is active (The other side can be notified or kept oblivious)
>a lot of times the *Send-and-Receive* variant is used to establish confidential things before moving on the *Read-and-Write*

The work request for a *Read-and-Write* RDMA operation needs to contain:
1. **The remote side virtual memory address**
2. **The remote side memory registration key** (mentioned in setup above)
 
