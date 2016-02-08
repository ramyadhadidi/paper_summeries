# 2016 Feb 4
## Practical Data Compression for Modern Memory Hierarchies
## Gennady Pekhimenko - Carnegie Mellon University

### B(Delta)I
Base-delta-index compression: one base value + deltas, another implicit 0 value | for decompression
you can use a _vector addition_, and it can be done in few cycles.

Put consequence cache line, in a single line

### Cache Management
Compressed block size relates to data reuse distance

CAMP (compression aware memory policy)

### Main memory comrpession
Challenges:
- Address Computation (non-uniform offset)
- Mapping & Fragmentation (virtual address uniform but ...)

Solution: 
- Linearly compressed pages (LCP) -> fixed offset, (meta data + exception storage)
- changes to PTE

Q) OS should be aware of main memory size: do profiling before
Q) garbage collection? they didn't do it

### BW Compression
Bit toggles is the source of enegry spending.

If you compress the data, bit toggles will increase -> toggle aware data compression (HPCA 2016)

# 2016 Feb 8
## Stamping Out Concurrency Bugs
## Baris Kasikci - EPFL

Physical shift to multicore in 2006 happened fast, and software developers did not have to tools to correctly wirte
software.

- Solved this with _Hybrid static-dynamic analysis_. (Low-overhead, high accuracy, commodity hardware)
- Runtime Monitoring in production (1~6% overhead)
- Detection - Diagnosis - Classification (all happens in-production)

Challenges of Root Cause Diagnosis:
- efficiency (expensive runtime info gathering)
- Accuracy
- in-production

### Gist
Failure sketch: like crash report, but just runtime instructions of the crash

### Architecture
Architecture: Failure report (core dump, program PC) -> static analyzer -> instrumentation -> refinement with runtime traces 
-> failure sketch computation engine

Should happens multiple time to gradually turn static analysis to precise root cause.

Static analysis (remove slice statements that are not part or crash) + flow control analysis (removes the code that are not executed)

### Tools & Methods:
- Flow Control Analysis: uses intel processor trace
- Data value tracking: with hardware checkpoint (max 4 points)
- Adaptive slice refinement (gradually find control flow graph)
