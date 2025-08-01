## Reverse loop
on some (mostly older) architectures/ compilers, reverse loop can actually compile to less instructions making it faster but both normal and reversed should have the same performance.
# Prefetching
>[!warning] Prefetching is only a *hint* processors may ignore it

#cache #prefetch
the `__builtin_prefetch(ptr)` is a T0 hint for temporal data to be fetched into all levels of cache by default.
### Prefetching hints
| Locality Hint    | Numeric | Meaning                          | Cache Level Implication                                                     |
| ---------------- | ------- | -------------------------------- | --------------------------------------------------------------------------- |
| **T0**           | `3`     | High temporal locality           | Keep in all cache levels                                                    |
| **T1**           | `2`     | Moderate locality                | May skip some LRU promotion<br>**L2 and higher**                            |
| **T2**           | `1`     | Low locality                     | May prefetch to L2 or L3 only<br>**L3 and higher or impl. specific choice** |
| **T3** / **NTA** | `0`     | No temporal locality (streaming) | Don’t pollute caches "*Prefetch into non-temporal cache structure*"         |

>[!warning] Keep in mind these all use the cache
> "*Don't pollute the caches*" here means don't pollute the **Hot Caches** L1/L2 but it does have to use LLC at least (*Again this is just a suggestion and they can end up being used if the HW decides*)
> **BUT usually this informs the eviction mechanism that this can be evicted first when there is pressure**

# Non-Temporal/ Streaming loads and stores
#SIMD #cache 
``` cpp
_mm_stream_load_si128(...) 
_mm_stream_ps(...)
``` 
These skip the entire cache to avoid polluting the entire cache hierarchy (*OR load it with minimal caching?*)
>[!info] Still might use *Write-combining buffers* internally

>[!note] *if you cannot bypass you can also flush before and after*
> `_mm_cflush(ptr)` 

>[!note] **LFB** *Line Fill Buffer*
> is a small hardware structure that sits between *L1D* and *L2* and it handles *Outstanding cache misses (Tracks in-flight requests) and cacheline transfers from lower levels*

