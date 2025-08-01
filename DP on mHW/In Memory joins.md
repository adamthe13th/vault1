>[!question] In a plot showing the Bandwidth decrease relative to Dataset size
> Slide 2: [InMemJoin(PDF)](Resources/InMEmJoins.pdf)
> - can we infer the *Cache size* ? 
> - What if all  cachelines by chance map to the same set
> - Can we then infer an *Untereschranke* for the size of the set-associative caches?
## Vector at a time
#vectorization 
[[Execution Model]]
if vector size is not optimal:
- **Too small** closer to tuple at a time so **More function calls than Materialization** but also still has the Materialization step so **slower than tuple-at-a-time**
- **Too large** exceeds cache size, too many cache misses (*I you can also see stairs when it is getting slower*)
>[!note] Vectorized worse perf than Materialization
>in theory vectorized should be at worst as good as *Operator at a time* when the vector size is too large but it can perform worse
>could it be that it has similar *Cache-unfriendliness* when it comes to data (No cache residency) but worse when it comes to **Instruction cache** since it does more function calls?

---
# Kinds of Hash-joins
### simple
build then probe and follow pointers into the build relation and compare keys (*All sequential*)
### simple parallel
**Build** in parallel (Build relation is divided among threads) into a shared hashtable that needs to be *Synchronized with locks* ("*usually very low contention*")
>[!danger] *But we want to minimize synchronization, minimize Data access cost (keep data local) and reuse data while it is still in cache*

**Probe** relation is divided into chunks among all threads, all readers so no need for synchronization
>[!warning] BAD
>data may not be local to threads
>too many random access (*for large relations, every hash_table access will likely be a miss*)
>Better hash function -> more random distribution of keys -> worse space locality

# Memory translation
#mmu 
>[!example] Translation process: Slide 14: [InMemJoin(PDF)](InMemJoin.pdf)
### Steps
1. CPU (Virtual Address) -> MMU
2. MMU (Virtual Page Number) -> TLB
3. **Hit?**
	1. TLB (Page Table Entry) -> MMU
	
4. **Miss?**
	1. MMU (Page table entry Address) -> RAM::Page table
	2. Page Table (Page table entry) -> TLB && MMU
5. MMU (Physical address) -> Main Memory
6. RAM (Data) -> CPU Chip
>[!info] TLB stores Virtual Page Number -> Page table entry (Physical address)

 >[!warning] The data may not even be in the page table (in RAM) in which case *Page fault* -> fetch from disk

>[!warning] the more *scattered*  your data is, the more pages you use,  the more the TLB is *thrashed*

>[!success] TLBs also can have hierarchy (*which is the case in modern CPUs*) generally 2 levels 
>and it seems that this is **One of the few uses where a fully associative cache makes sense** although, real sue case (i7) seems to have 2 levels of 4-way associative TLB
## Huge pages
usually inefficient because a process will most likely not end up using the entire page (2MB, 1GB) 
**BUT** using huge pages -> using less pages -> less entries in the TLB -> less misses/ Thrashing.


