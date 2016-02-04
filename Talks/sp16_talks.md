# 2016 Feb 4
## Practical Data Compression for Modern Memory Hierarchies - Gennady Pekhimenko, Carnegie Mellon University

## B(Delta)I
Base-delta-index compression: one base value + deltas, another implicit 0 value | for decompression
you can use a _vector addition_, and it can be done in few cycles.

Put consequence cache line, in a single line

## Cache Management
Compressed block size relates to data reuse distance

CAMP (compression aware memory policy)

## Main memory comrpession
Challenges:
- Address Computation (non-uniform offset)
- Mapping & Fragmentation (virtual address uniform but ...)

Solution: 
- Linearly compressed pages (LCP) -> fixed offset, (meta data + exception storage)
- changes to PTE

Q) OS should be aware of main memory size: do profiling before
Q) garbage collection? they didn't do it

## BW Compression
Bit toggles is the source of enery spending.

If you compress the data, bit toggles will increase -> toggle aware data compression (HPCA 2016)
