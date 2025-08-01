# Cache timing attack
#cache
using the time it takes something to execute/ read to infer characteristics about the information.
in the specific case for cache:
- attacker flushes/ fills specific cache lines
- watches for the time it takes to execute a read to determine whether the other process read something that's still in cache or that was evicted

# Dennard scaling
#transistor
>[!quote] 
>*As Transistors get smaller, their power density stays constant*
>Current and voltage scale down with size -> Power use per area stays the same despite the increase in number of Transistors
## Limits
1. voltage cannot be reduced further due to **Voltage threshold**
2. Current cannot be reduced further due to **Current leakage as transistors get smaller**
3. heat and power consumption become too high
# Dark silicon
#transistor
parts of a CPU are turned off (power is cut off) to make power consumption and thermals manageable. (e.g. AI accelerators are only turned on when they are actually needed. Idle CPU cores (specially in big.LITTLE design))

# Compute express link (CXL)
## cache coherence
#cache 
**CXL.cache protocol** allows both CPU and connected hardware via CXL to cache data separately (CXL connected device has its own cache or accesses memory directly) without losing cache coherency so they **Always see the same data** even if it is caches separately
=> no stale data when one of the devices updates

## memory sharing
#memory
**CXL.mem** protocol allows the CXL connected device to share memory with the CPU (*or even add memory capacity to the system*)
-> the CPU can access the device's memory as if it's part of system RAM
-> **Some device** can also access CPU memory directly (Obviously this would also involve cache coherence)
