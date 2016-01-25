## A Scalable Processing-in-Memory Accelerator for Parallel Graph Processing


### Intro

Scalable graph processing system: it performance increases proportionally with the size of stored graph

Challenges: 
  - items are not processed fast enough
  - data movement is not fast 

First one can be solved by specialized hardware, but memory bandwidth is limited. So, performance does not scale well with data per server.
It means more servers and thereby cost-ineffective and not scalable solutions.

They propose _Tesseract_:
  1. new hardware that uses all bandwidth
  2. communicates with different memory partitions
    - non-blocking so latency hiding
    - atomic memory updates 
  3. programming interface for utilization
  4. 2 hardware prefetcher 
    - one by programmer hints
    - second automatic

Why graph computing is more restricted:
  - less computation per item
  - large amount of random accesses

5 workloads:
 - 10x average speedup
 - 87% energy reduction

### Background and Motivation

Hard to analyze, so exploit data parallelism. Even with that it is hard to get performances, because:
  - large random accesses
  - poor memory locality (entire vertices in each iteration)
  - cannot easily be overlapped due to small amounts of computation

Figure 2 shows the main bottleneck is memory bandwidth. Last bar is just using internal bandwidth of 16HMCs (8TB/s). It shows that **high performance needs massive bandwidth** because of large data size, random access and inability to use memory hierarchies.

#### Note on HMC Bandwidth (Quote)
According to the HMC 1.0 specification, a single HMC provides up to 320 GB/s of external memory bandwidth through eight high-speed serial links. On
the other hand, a 64-bit vertical interface for each DRAM partition (or vault), 32 vaults per cube, and 2 Gb/s of TSV signaling rate [24] together achieve an
internal memory bandwidth of 512 GB/s per cube. Moreover,
this gap between external and internal memory bandwidth
becomes much wider as the memory capacity increases with
the use of more HMCs. Considering a system composed of 16
8 GB HMCs as an example, conventional processors are still
limited to 320 GB/s of memory bandwidth assuming that the
CPU chip has the same number of off-chip links as that of an
HMC. In contrast, PIM exposes 8 TB/s (= 16×512GB/s) of
aggregate internal bandwidth to the in-memory computation
units.