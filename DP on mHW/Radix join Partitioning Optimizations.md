>[!tip] All these Optimizations seem to be boosted further by the use of *Huge Pages*
>source [[Resources/InMEmJoins.pdf]] Chart comparison

>[!danger] All Optimizations *and their results* mentioned here are applied to Narrow tuples 
>for larger Tuples,  no-partitioning can be more stable/ faster
>in slide 49 of source above, for larger tuples less byte transfers are needed for both building and probing after a certain size and we want to minimize memory transfers -> **No-Partitioning becomes better**

>[!note] if optimized well, (*with prefetching or SWWCB and streaming instructions*) the *Join* can become *Memory bound*. 
# single Vs. Multi-pass partitioning
#partition #radix
## Why is multi-pass better
assuming a *Radix partitioning* using 12 bits -> 2¹² Partitions = 4096
that means in a **Single pass** the TLB would *potentially* have to keep track of 4096 pages

***Using Multi-pass*** we would first partition using 6 bits -> 64 **BIG** partitions that are not cache resident (*not to be using for the join*)
then we grab 1 ***BIG partition*** at a time and re-partition it again using 6 more bits -> 64 more partitions per resulting in a total of 4096
=> at one point in time (*one BIG partition*) the TLB will only have to deal with 64 pages then move on to the next 64 -> less thrashing that having to deal with 4096 at once
# Software write-combine buffers
#swcb
Before flushing something to a particular Partition on a ***per-tuple basis*** 
Use a **Software write-combine Buffer** to write out chunks at a time
***Each Partition has a buffer to itself*** obviously smaller than the Partition
>[!tip] Pages are now accesses at *Buffer granularity* rather than *Tuple granularity*
>-> reduces the number of required **memory accesses** and **address translations** by a factor of B (*buffer tuple capacity*)

>[!info] The sweet spot for the number of partitions depends on the **Cachelines per Partition Buffer** (*the size of the partition buffer is considered in terms of Cachelines because it only makes sense if cache-resident*)


# Non-Temporal streaming
#nts
I need to write something into memory
- **Standard way** Pull write target into cache -> write into cacheline -> when cacheline is evicted write into memory
This however always evicts a Cacheline from the currently Cached working set (*This of course assumes our entire cache is populated with the working set*)
The newly written value doesn't need to be read again so its existence in cache is pointless
>[!success] Solution: Non temporal (*will not be re-used soon*) streaming stores
>use SIMD instrinsic to write **an Entire Cacheline** (*as of AVX512*) directly into memory without having to cache the line first

>[!danger] Streaming stores are used to write from *SWWBC* that's in cache into a *Partition* that we do not want to be pulled into cache

# Exam question
![[Screenshot From 2025-08-01 17-23-16.png]]
>[!note] There are 2 steps -> more Radix bits -> more pages spread -> 2 levels of TLB overflowing one after the other and thrashing

>[!note] Adding SWWCBs helps by writing to a *Cache resident Buffer* which avoids address translation (*also, fewer address translations due to less granularity of accesses*)

->further Optimizations: fine tuning buffer/ Partition sizes + multi-pass + huge pages
# Using huge Pages
#hugetables
# Numa awareness
#numa