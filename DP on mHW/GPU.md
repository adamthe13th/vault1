#gpu
# Questions
- [ ] what are crystal and crystal Opt
- [ ] is the bitmap used for tracking invalidated tuples valid for both row and column store and where is this bitmap stored?

# Notes
### A100 setup:
- 108 streaming Multiprocessors
- each streaming Multiprocessor has 2 x 32 Cuda cores
- 4 x Warp schedulers 2 x 4 instruction dispatchers
	- this is because each Warp scheduler can use 2 dispatcher per cycle for 2 independent instructions (1 for the CUDA cores and 1 for Memory store / load units)
	- only 2 Warps can be schedules at a time in a SM despite having 4 Warp schedulers
	- **Possible configs** 2 Warp schedulers: each 1 load and 1 compute instr -> full use || 4 Warp schedulers: 2 of them dispatch compute, 2 other dispatch load/ store instructions
- 1 x L1-Cache + shared local memory (pre-volta used to be separate now dynamically partitioned)
### Lock step exec VS. interleaved exec Model
post Volta, they added individual program counters for each thread in a single warp:
	now during branching you can interleave both branches execution rather than finishing one then the other **-> total exec time doesn't change but latency for the second branch is improved if it procedurally returns data**
## Throughput

![[Screenshot From 2025-07-21 12-40-16.png]]



- understandably the roof line model for the L2 finds you run into the peak compute bandwidth faster since the transfer rate stops being the bottleneck for more operations per byte. 